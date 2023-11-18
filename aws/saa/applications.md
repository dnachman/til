---
title: "Applications"
---


## SQS
* Distributed Queueing system - pull based (polling)
* Decouple the components of an application to run independently
* 256 KB of text in any format (above that goes to S3)
* 1 minute to 14 days (4 days retention default)
* Visibility timeout is the amount of time that the message is invisible in the SQS queue after a reader picks up that message - max time is 12 hours. If duplicate processing start occurring, increase visibility timeout so gives time for processors to consume.
* Long polling - doesn’t return a response until a message arrives in the q or the poll times out. Short polling returns immediately (even for no messages) - ReceiveMessageWaitTimeSeconds
* Message-oriented API
* Standard Queues
    * nearly unlimited transactions per second
    * Guaranteed to be deliver at least once
    * More than one copy of message might be delivered out order - but maintains best-effort ordering
* FIFO
    * FIFO delivery
    * Exactly Once processing
    * No duplicates
    * Support message groups that allow multiple ordered message queue
    * Limited to 300 TPS

## SWF - Simple Workflow Service
* Coordinate work across distributed application components
* Media processing, web app backends, business process workflows, analytics pipelines 
* Tasks represent invocations of various processing steps in an application which can be performed by executable code, web service calls, human actions, and scripts. (Think about a warehouse w/ human element)
* Workflow executions can last up to 1 year
* Task-oriented API (SDKs, but all accessible via HTTP)
* Task is assigned only once, never duplicated
* Tracks all tasks and events in an application
* SWF Actors:
    * Workflow Starters - App that can initiate a workflow
    * Deciders - control the flow of activity tasks in a workflow execution - if something has finished/failed, a Decider decides what to do next
    * Activity Workers - carry out activity tasks
* Domains provide a way of scoping SWF resources in your account

## SNS - Simple Notification Service
* Pay as you go, no up-front
* Publish messages to subscribers or other apps - Push Based
* Push notification
* Delivery SMS, email to SQS, any http endpoint
* Multiple recipients via topics - an access point for allowing recipients to dynamically subscribe 
* All messages stored redundantly across multiple AZ

## Elastic Transcoder
* Media transcoder in cloud - convert media across formats
* Transcoding presets

## Api Gateway
* Fully managed service that makes it easy for devs to publish, maintain, and monitor API at any scale
* Front door
* Capabilities:
    * Expose HTTPS endpoints to define a restful API
    * Serverlessly connect to Lambda and DynamoDB
    * Send each API endpoint to a  different target
    * Run efficiently w/ low cost
    * Scale effortless and automatic
    * Track and control usage by API
    * Throttle requests to prevent attacks
    * Log to Cloudwatch for monitoring
    * Maintain multiple versions
    * Set request/response transformations
* Deploy API to a stage : API GW Domain by default, custom domain, AWS Certificates manager
* Caching - TTL
* Same Origin Policy - scripts from 2 pages can only talk if on same origin - enforced by web browsers (ignored by Postman and curl)
* Cross Origin Resource Sharing (CORS) - restricted resources can be requested from another domain. OPTIONS call checks - “Origin policy cannot be read at the remote resource”

## Kinesis
* Streaming data is generated continuously by thousands of data sources, typically sending data records simultaneously and in small size
* Makes it easy to load and process data
* Kinesis Streams - data stored in shards
    * Data contained in shards, producers/consumers. Data persistence
    * Shards - total capacity of stream is the sum of the cap of shards
        * 5 tx per second for reads, up to max total data read rate of 2MB/sec and up to 1000 records/sec writes, up to total data write rate of 1 MB/sec (including partition keys)
        * Data capacity is a function of number of shards. Total capacity of the stream is the sum of the capacities of its shards
    * Store data 24 hrs to 7 days (persistence)
* Kinesis Firehose
    * Components: Delivery streams, records of data and destinations
    * no persistence, must do something with it asap
    * Can have lambda function in firehose (output to S3, elastic search etc)
* Kinesis Analytics
    * Analyze streams or firehose and store somewhere else
    * Output to S3, Redshift, Elasticsearch Cluster

## Web Identity Federation and Cognito
* Web Identity Federation lets you give users access to AWS resources after successful authenticated w/ provider like AMZN, FB, GOOG.
* Receives authentication code from the Web ID provider, can be traded for temporary AWS security credentials
* Sign up and in
* Access for guest users
* Acts as Identity Broker w/ Web Identity Provider
* Syncs user data for multiple devices
* Recommended for all mobile applications
* No need to store AWS Credentials in app
* User Pools - user directories used to manage sign up and sign in. Users can sign-in directly to the pool or use Federated. Successful authentication generates a JSON Web token (JWT)
* Identity Pools enable provide temporary AWS credentials (IAM Role) to access AWS services like S3 or DDB
* Cognito tracks the association between user identity and the various devices they sign in from. Uses Push Synchronization to push updates and sync user data across devices using SNS


## Event Processing Patterns
* Event Driven Architecture - Pub/Sub 
    * Decoupled systems running in response to events
    * SNS Topic - async event notifications 
    * Publisher sends message to SNS Topic; all subscribers receive the message
* Dead-Letter Queue (DLQ) - Undeliverable message
    * SNS : as a SQS queue that fail to deliver
    * SQS: maxReceiveCount is exceeded send to DLQ (another SQS)
    * Lambda - async failures (2 retries by lambda) -> either SQS or SNS 
* Fanout Pattern - need to send to multiple systems
    * Publisher send message to SNS Topic first then subscribers get it (multiple SQS queues)
    * Solves this Problem - if send to 2 SQS queue (distributed), what happens if second one fails (no distributed tx) 
    * Can be used to send same data to production and test system 
* S3 Event Notification - receive notifications when events happen in bucket
    * Delivers to SQS, SNS, Lambda 
    * Filter the notifications (*.png)
    * Enable versioning to make sure get notification (otherwise not perfect) 
    * Events:
        * Object Created
        * Object Removed
        * Object Restored
        * RRS object Lost
        * Replication (fails, > 15 mins, object no longer tracked by replication metrics)




