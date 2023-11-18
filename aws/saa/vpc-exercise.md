---
title: VPC Exercise
---

A great way to test practical understanding of VPC

- Create VPC for large block: 10.0.0.0/16
- Create private subnet 10.0.2.0/24
- public subnets 10.0.1.0/24 . Autoassign public IP after create
- Create igw in the public subnet. Associate after create
- Create new route table for public subnet (keep default private) 
  - Edit public route table to set destination 0.0.0.0/0 to igw
- Set up Nat GW or VPC S3 Endpoints to get updates on private subnet (nat gw is in public subnet)
  - Or nat instance from community ami - disable source/destination checks on this instance, SG allow inbound from 10.0.2.0/24
  - edit default/private route table to route to ngw 
- Create flow logs - need Role to write to CW logs
- Create a web server in the public subnet w/ apache:
```
    #!/bin/bash
    yum update
    yum upgrade -y
    yum install httpd -y
    sudo yum install ec2-instance-connect -y
    cd /var/www/html
    echo "<html><h1>Hello World</h1></html>" > index.html
    service httpd start
    chkconfig httpd on
```
- Create a db server in the private subnet
  - security group should allow comms from private subnet (10.0.1.0/24)
- Create bastion host
  - Using ssh-add and ssh-agent:
  ```
      ssh-add 
      ssh-add ~/.ssh/private-key.pem
      ssh-add -l or ssh-add -L
      ssh -A user@remotehost.com
  ```

