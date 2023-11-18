---
title: "Security"
---


## Reducing security threats
* NACL - block bad actor
* Host-based firewall - firewalld, ufw, Windows firewall
    * ALB makes it a bit harder
    * Can allow only ALB SG on EC2 instances
* NLB - traffic passes through NLB
* Web Application Firewall (WAF) - IP blocking and filtering, preconfigured injections (SQL Injections, etc - Layer 7)
    * Can attach to CloudFront or ALB
* CloudFront Geo Match can also block countries

## Key Management Service (KMS)
* Regional  secure key management enc/dec
* Managed customer master keys (CMKs)
* Ideal for S3 objects, db passwords, api keys stored in Systems Manager Parameter Store
* Encrypt/Decrypt data up to 4KB in size
* Pay per API call
* Audit capability using CloudTrail - logs to S3
* FIPS 140-2 Level 2 - must provide evidence of tampering
* Level 3 is covered by CloudHSM
* Types of CMK
    * AWS Managed CMK - Free; used by default
    * Customer Managed - Allows key rotation, controlled via key policies and can be enabled/disabled
    * AWS Owned CMK - shared basis, typically don’t see
* Symmetric - same key for enc/dec AES-256. Never leaves AWS unencrypted , used by AWS services. Can generate data keys, data key pairs, random byte strings. Import your own key material
* Asymmetric - public/private key pair. RSA (older) and elliptic-curve cryptography (ECC). Private key never leaves AWS unencrypted. Download the public key and use outside AWS (especially if can’t call KMS apis). AWS services integrated w/ KMS do no support asymmetric CMKs. Sign messages and verify signatures.
* For > 4Kb - can use “envelope encryption”, which is more network efficient

## CloudHSM
* Dedicate hardware security modules
* FIPS 140-2 Level 3 - key data is removed if physical enclosure is opened
* Manage your own keys
* Single tenant, multi-AZ cluster
* No access to the AWS-managed component
* Runs w/in VPC in your account - exposes ENI
* Suggested at least 2 AZ
* Industry-standard APIs
* PKCS#11
* Java Cryptography Extensions (JCE)
* Microsoft CryptoNG (CNG)
* Keep your keys safe - irretrievable if lost

## Systems Manager Parameter Store
* Servlerless
* Passwords, db cons strings, license codes, api keys
* Values can be stored encrypted (KMS) or plaintext
* Separate from source control
* Store in hierarchies - up to 15 levels
* Track Versions
* Set TTL to expire values
* Paths:  /prod/db/mysql/db-string - GetParametersByPath
* Can grant to specific path
* Integration w/ CloudFormation

## Secrets Manager
* Similar to parameter store - no charges up to 10K API Calls
* Secrets manager has cost
* Secrets manager can automatically rotate secrets
* Apply the new key/password in RDS for you
* Generate random secrets - in cloud formation or use in own code
* Shared across accounts

## AWS Shield
* Mitigate DDoS
* AWS Shield Standard - automatically enabled for all customers at no cost, common layer 3/4 attacks
    * SYN/UDP floods
    * Reflection attacks (source is spoofed)
* AWS Shield Advanced 
    * $3,000 per month per org
    * Enhanced protection for EC2, ELB, CloudFront, Global Accelerator, Route 53
    * Business/Enterprise support sutlers get 24x7 access to the DDoS Response Team (DRT)
    * DDoS cost protection


## Web Application Firewall (WAF)
* lets you monitor http(s) to CloudFront, ALB or API Gateway
* Control access to content
* Configure filter rules to allow/deny traffic
    * IP addresses
    * Query string parameters
    * SQL query injection
* Blocked traffic return HTTP 403 Forbidden
* Different behaviors:
    * Allow all requests, except ones you specify 
    * Block all requests, except ones you specify 
    * Count the requests that match the properties you specify
* Request properties:
    * Originating IP address
    * Originating country
    * Request size
    * Values in request headers
    * Regex in request
    * SQL code (injection)
    * Cross-site scripting (XSS)
* AWS Firewall manager
    * Centrally manage firewall rules across an AWS Organization
    * WAF rules:
        * ALB
        * API GW
        * CloudFront distributions
    * AWS Shield Advanced protections:
        * ALB
        * ELB Classic
        * EIP
        * CloudFront distributions
    * Enable security groups for EC2 and ENIs












