## Deployment

- CodeDeploy is a deployment service that automates application deployments to Amazon EC2 instances, on-premises instances, serverless Lambda functions, or Amazon ECS services.
    - Deployment options
        - in-place deployment: only ec2 and premises
        - blue/green deployment: ec2, lambda, ecs
- Transform section in SAM template specifies the version of AWS SAM to use
- You can detach an Amazon EBS volume from an instance explicitly or by terminating the instance. However, if the instance is running, you must first unmount the volume from the instance. If an EBS volume is the root device of an instance, you must stop the instance before you can detach the volume.
- AWS CloudFormation provides the following Python helper scripts that you can use to install software and start services on an Amazon EC2 instance that you create as part of your stack: cfn-init: Use to retrieve and interpret resource metadata, install packages, create files, and start services.
- A route table contains a set of rules, called routes, that are used to determine where network traffic is directed. a subnet can only be associated with one route table at a time.
- Deployment options
    - Immutable: deployments perform an immutable update to launch a full set of new instances running the new version of the application in a separate Auto Scaling group, alongside the instances running the old version
    - Rolling: updates existing instances
    - Rolling with additional batch deployment: updates existing instances
    - All at once: this will deploy the new version to all existing instances immediately and will not create new EC2 instances. Hence, it is possible that there would be a degradation of the service or worse, system downtime since some instances would be unavailable during the deployment process.
    - Canary: two increments. You can choose from predefined canary options that specify the percentage of traffic shifted to your updated Lambda function version in the first increment and the interval, in minutes, before the remaining traffic is shifted in the second increment.
    - Linear
- `sam package` & `sam deploy`
- AWS CloudFormation StackSets extends the functionality of stacks by enabling you to create, update, or delete stacks across multiple accounts and regions with a single operation
- CloudFormation is incorrect because although this service can certainly be used to deploy Lambda, API Gateway, DynamoDB, and other AWS resources of your serverless application, it doesn’t have the capability to locally build, test, and debug your application like what AWS SAM has
- A task placement strategy is an algorithm for selecting instances for task placement or tasks for termination. Task placement strategies can be specified when either running a task or creating a new service.
    - Amazon ECS supports the following task placement strategies:
        - binpack – Place tasks based on the least available amount of CPU or memory. This minimizes the number of instances in use.
        - random – Place tasks randomly.
        - spread – Place tasks evenly based on the specified value. Accepted values are attribute key-value pairs, instanceId, or host.
- You can configure your Lambda function to pull in additional code and content in the form of layers. A layer is a ZIP archive that contains libraries, a custom runtime, or other dependencies. With layers, you can use libraries in your function without needing to include them in your deployment package.
- Canary is incorrect because this type of deployment method is not readily available in Elastic Beanstalk but only in AWS Lambda.
- Least amount of deployment time is all at once
- AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture. With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors. X-Ray provides an end-to-end view of requests as they travel through your application, and shows a map of your application’s underlying components.
- Amazon Inspector is incorrect because this is primarily used for EC2 and not for Lambda.
- AWS CloudTrail is incorrect because this will only enable you to track all API calls to your Lambda, DynamoDB, and SNS. It is still better to use AWS X-Ray to debug your application.
- An EC2 instance needs to have access to the Internet, via the Internet Gateway or a NAT Instance/Gateway in order to access S3. Alternatively, you can also create a VPC endpoint so your private subnet would be able to connect to S3.
- Lambda: The InvalidParameterValueException will be returned if one of the parameters in the request is invalid.

## Security

- If you have resources that are running inside AWS, that need programmatic access to various AWS services,then the best practice is to always use IAM roles. However, for applications running outside of an AWS environment, these will need access keys for programmatic access to AWS resources. In order to use the AWS SDK for your application, you have to create your credentials file first at ~/.aws/credentials for Linux servers
- Cognito user pool for sign ups. Identity pool is to give access to resources and generate temporary aws credentials for unauthenticated users
- If you are getting an Access Denied error when trying to upload a large file to your S3 bucket with an upload request that includes an AWS KMS key then you have to confirm that you have the permission to perform kms:Decrypt actions on the AWS KMS key that you’re using to encrypt the object. To perform a multipart upload with encryption using an AWS Key Management Service (AWS KMS) customer master key (CMK), the requester must have permission to the kms:Decrypt and kms:GenerateDataKey* actions on the key. These permissions are required because Amazon S3 must decrypt and read data from the encrypted file parts before it completes the multipart upload.
- SSE-C headers: x-amz-server-side-encryption-customer-algorithm, x-amz-server-side-encryption-customer-key, x-amz-server-side-encryption-customer-key-MD5
- Including the x-amz-server-side-encryption and x-amz-server-side-encryption-aws-kms-key-id headers in the upload request is incorrect because these headers are primarily used in Server-Side Encryption with AWS KMS-Managed Keys (SSE-KMS) and not for Server-Side Encryption with Customer-Provided Keys (SSE-C).
- A Lambda authorizer is an API Gateway feature that uses a Lambda function to control access to your API
    - There are two types of Lambda authorizers:
        - A token-based Lambda authorizer (also called a TOKEN authorizer) receives the caller’s identity in a bearer token, such as a JSON Web Token (JWT) or an OAuth token.
            - use this for OAuth or SAML
        - A request parameter-based Lambda authorizer (also called a REQUEST authorizer) receives the caller’s identity in a combination of headers, query string parameters, stageVariables, and $context variables.
            - use this for custom
-  it is recommended that you use ACM to provision, manage, and deploy your server certificates. In unsupported Regions, you must use IAM as a certificate manager.
- Amazon RDS supports using Transparent Data Encryption (TDE) to encrypt stored data on your DB instances running Microsoft SQL Server. TDE automatically encrypts data before it is written to storage, and automatically decrypts data when the data is read from storage.
- Enabling RDS Encryption is incorrect because this simply encrypts your Amazon RDS DB instances and snapshots at rest. It doesn’t automatically encrypt data before it is written to storage, nor automatically decrypt data when it is read from storage.
- in s3 object owner can optionally share objects with others by creating a pre-signed URL, using their own security credentials, to grant time-limited permission to download the objects.
- AWS Systems Manager Parameter Store provides secure, hierarchical storage for configuration data management and secrets management. You can store data such as passwords, database strings, and license codes as parameter values. You can store values as plain text or encrypted data.
- Envelope encryption
    - Encrypt
        1. Use the GenerateDataKey operation to get a data encryption key.
        2. Use the plaintext data key (returned in the Plaintext field of the response) to encrypt data locally, then erase the plaintext data key from memory.
        3. Store the encrypted data key (returned in the CiphertextBlob field of the response) alongside the locally encrypted data.
    - Decrypt
        1. Use the Decrypt operation to decrypt the encrypted data key. The operation returns a plaintext copy of the data key.
        2. Use the plaintext data key to decrypt data locally, then erase the plaintext data key from memory.
- S3 upload an object is s3:PutObject. For server side encryption include x-amz-server-side-encryption
- If your identity store is not compatible with SAML 2.0, then you can build a custom identity broker application to perform a similar function. The broker application authenticates users, requests temporary credentials for users from AWS, and then provides them to the user to access AWS resources.
-  the correct description of the envelope encryption process is to encrypt plaintext data with a data key and then encrypt the data key with a top-level plaintext master key.
- AWS Secrets managers has automatic rotation on keys
- An AWS Lambda hook is one Lambda function specified with a string on a new line after the name of the lifecycle event. Hooks are: BeforeAllowTraffic and AfterAllowTraffic
- The consolidated billing feature in AWS Organizations allows you to consolidate payment for multiple AWS accounts or multiple AISPL accounts.
- To upload an object to the S3 bucket which uses SSE-KMS, you have to send a request with an x-amz-server-side-encryption header with the value of aws:kms
    - The value of AES256 is only applicable for SSE-S3 and SSE-C.

## Development with AWS Services

- You can use the --page-size option to specify that the AWS CLI request a smaller number of items from each call to the AWS service.
- The unit of scale for AWS Lambda is a concurrent execution. If you set the concurrent execution limit for a function, the value is deducted from the unreserved concurrency pool. By default, an AWS account’s concurrent execution limit is 1000 which will be shared by all Lambda functions. Take note that AWS Lambda will keep the unreserved concurrency pool at a minimum of 100 concurrent executions. Hence, you can only allocate a concurrent execution limit of 900 for a single Lambda function or 450 for two functions.
- Multipart upload allows you to upload a single object as a set of parts
- Use the BatchWriteItem APIand Use the Putltem API are incorrect because these are DynamoDB APIs and not S3.
- you can set up an origin failover by creating an origin group with two origins with one as the primary origin and the other as the second origin, which CloudFront automatically switches to when the primary origin fails. This will alleviate the occasional HTTP 504 errors that users are experiencing.
- A segment can break down the data about the work done into subsegments. Subsegments provide more granular timing information and details about downstream calls that your application made to fulfill the original request. A subsegment can contain additional details about a call to an AWS service, an external HTTP API, or an SQL database. You can even define arbitrary subsegments to instrument specific functions or lines of code in your application.
- metadata is incorrect because this does not record the calls to AWS services and resources that are made by the application. Segments and subsegments can include a metadata object containing one or more fields with values of any type, including objects and arrays.
- AWS CodeStar provides the tools you need to quickly develop, build, and deploy applications on AWS. With AWS CodeStar, you can use a variety of project templates to start developing applications on Amazon EC2, AWS Lambda, and AWS Elastic Beanstalk. The project dashboard in AWS CodeStar makes it easy to centrally monitor application activity and manage day-to-day development tasks such as recent code commits, builds, and deployments. Because AWS CodeStar integrates with Atlassian JIRA, a third-party issue tracking and project management tool, you can create and manage JIRA issues in the AWS CodeStar dashboard.
- Cloud9 is incorrect because it is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with your browser.
- OpsWorks is incorrect because it is a configuration management service that provides managed instances of Chef and Puppet.
- Amazon Cognito Sync is an AWS service and client library that enables cross-device syncing of application-related user data. You can use it to synchronize user profile data across mobile devices and the web without requiring your own backend.
- The purpose of resharding in Amazon Kinesis Data Streams is to enable your stream to adapt to changes in the rate of data flow. You split shards to increase the capacity (and cost) of your stream. You merge shards to reduce the cost (and capacity) of your stream.
- You can monitor the MySQL error log, slow query log, and the general log. The MySQL error log is generated by default; you can generate the slow query and general logs by setting parameters in your DB parameter group.
- All of the APIs created with Amazon API Gateway expose HTTPS endpoints only. Amazon API Gateway does not support unencrypted (HTTP) endpoints.
- HTTP_PROXY ou only need to set the HTTP method and the HTTP endpoint URI, according to the backend requirements. Connection does not pass through API Gateway but between client and backend
- AWS is incorrect because this type is only used for Lambda custom integration. Take note that the scenario uses an application hosted in EC2 and not in Lambda.
- AWS_PROXY is incorrect because this type is primarily used for Lambda proxy integration. The scenario didn’t mention that it uses a serverless application or Lambda.
- HTTP is incorrect because this type is only used for HTTP custom integration where you need to specify how the incoming request data is mapped to the integration request and how the resulting integration response data is mapped to the method response.
- For Lambda functions that process Kinesis or DynamoDB streams, the number of shards is the unit of concurrency. If your stream has 100 active shards, there will be at most 100 Lambda function invocations running concurrently.
- Global secondary index — an index with a partition key and a sort key that can be different from those on the base table. A global secondary index is considered “global” because queries on the index can span all of the data in the base table, across all partitions.
    - Queries or scans on this index consume capacity units from the index, not from the base table.
    - Queries on this index support eventual consistency only.
- Local secondary index — an index that has the same partition key as the base table, but a different sort key. A local secondary index is “local” in the sense that every partition of a local secondary index is scoped to a base table partition that has the same partition key value.
- These services have a lot of elasticity. meaning they can instantly grow or shrink to match the requirements of a specific application.
    - Amazon ECS
    - Amazon EC2 Spot Fleets
    - Amazon EMR clusters
    - Amazon AppStream 2.0 stacks and fleets
    - Amazon DynamoDB
- Simple workflow service
    - use markers to record events in the workflow execution history for application specific purposes. 
    - signals is incorrect because it just enables you to inject information into a running workflow execution.
    - Timers is incorrect because it just enables you to notify your decider when a certain amount of time has elapsed and does not meet the requirement in this scenario.
    - tags is incorrect because it just enables you to filter the listing of the executions when you use the visibility operations, which once again does not meet the requirement in this scenario.
- Amazon S3 Transfer Acceleration enables fast, easy, and secure transfers of files over long distances between your client and your Amazon S3 bucket. Transfer Acceleration leverages Amazon CloudFront’s globally distributed AWS Edge Locations. As data arrives at an AWS Edge Location, data is routed to your Amazon S3 bucket over an optimized network path.
- Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API. They act like environment variables and can be used in your API setup and mapping templates.
- Partitions are usually throttled when they are accessed by your downstream applications much more frequently than other partitions (that is, a “hot” partition), or when workloads rely on short periods of time with high usage (a “burst” of read or write activity). To avoid hot partitions and throttling, you must optimize your table and partition structure.
    - Implement error retries and exponential backoff
    - Refactor your application to distribute your read and write operations as evenly as possible across your table.
- AWS Step Functions provides serverless orchestration for modern applications. Orchestration centrally manages a workflow by breaking it into multiple steps, adding flow logic, and tracking the inputs and outputs between the steps.

## Refactoring

- Amazon ElastiCache for Redis is a blazing fast in-memory data store that provides sub-millisecond latency to power internet-scale real-time applications. Use an ElastiCache for Redis cluster to store the user session state of the application.
- Use projection-expression to get specific fields in DB instead of all the fields
- Cloudfront can be configured to use HTTPS only. For encryption of in flight requests configure Origin Protocol policy and Viewer protocol policy
- Short polling is 15 or 20 seconds. 
- There are two primary reasons why records may be delivered more than one time to your Amazon Kinesis Data Streams application: producer retries and consumer retries. Your application must anticipate and appropriately handle processing individual records multiple times.
- DynamoDB Streams captures a time-ordered sequence of item-level modifications in any DynamoDB table, and stores this information in a log for up to 24 hours. Applications can access this log and view the data items as they appeared before and after they were modified, in near real time.
- DynamoDB Accelerator (DAX) feature simply takes the performance of the DynamoDB table to the next level with response times in microseconds for millions of requests per second for read-heavy workloads.
- appspec.ymlis incorrect because this is used to manage each application deployment as a series of lifecycle event hooks in CodeDeploy and not in Elastic Beanstalk.
- You can define periodic tasks in a file named cron.yaml in your source bundle to add jobs to your worker environment’s queue automatically at a regular interval.
- FIFO queues have all the capabilities of the standard queue. The most important features of this queue type are FIFO (First-In-First-Out) delivery and exactly-once processing. Unlike standard queues, FIFO queues don’t introduce duplicate messages. FIFO queues help you avoid sending duplicates to a queue. To configure deduplication, you must do one of the following:
    - Enable content-based deduplication.
    - Explicitly provide the message deduplication ID (or view the sequence number) for the message.
- Amazon DynamoDB Accelerator (DAX) is a fully managed, highly available, in-memory cache for DynamoDB that delivers up to a 10x performance improvement – from milliseconds to microseconds – even at millions of requests per second
- Integrating Amazon Kinesis Data Firehose with the Amazon Kinesis Data Stream to increase the capacity of the stream is incorrect because Kineses Data Firehose just provides a way to reliably transform and load streaming data into data stores and analytics tools. 
- The –summary parameter is incorrect because this only displays the summary information (number of objects, total size) of objects returned from an “s3 ls” command.
-  Using Query operations and reducing the page size of your query are the more cost-effective solutions in this scenario.
-  Your sign-in page URL has the following format, by default: https://Your_AWS_Account_ID.signin.aws.amazon.com/console/
- AWS Lambda supports synchronous and asynchronous invocation of a Lambda function. You can control the invocation type only when you invoke a Lambda function. RequestResponse(default) synchronous. Event (asynchoronous)

## Monitoring and Troubleshooting

- environment variables are used by AWS Lambda to facilitate communication with X-Ray: _X_AMZN_TRACE_ID & AWS_XRAY_CONTEXT_MISSING
- Take note that there are certain differences between CloudWatch and Enhanced Monitoring Metrics. CloudWatch gathers metrics about CPU utilization from the hypervisor for a DB instance, and Enhanced Monitoring gathers its metrics from an agent on the instance. As a result, you might find differences between the measurements, because the hypervisor layer performs a small amount of work.   The differences can be greater if your DB instances use smaller instance classes because then there are likely more virtual machines (VMs) that are managed by the hypervisor layer on a single physical instance. Enhanced Monitoring metrics are useful when you want to see how different processes or threads on a DB instance use the CPU.
- VPC Flow Logs is incorrect because this is just a feature that enables you to capture information about the IP traffic going to and from network interfaces in your VPC.
-  CloudTrail logs to track all API calls and capture information about the IP traffic going to and from your VPC is incorrect because although you can indeed use CloudTrail to track the API call, it can’t capture information about the IP traffic of your VPC.
- 504 error is timed out
- a large number of incoming requests will most likely produce an HTTP 502 or 429
- AWS CloudTrail is a service that enables governance, compliance, operational auditing, and risk auditing of your AWS account.  CloudTrail provides event history of your AWS account activity, including actions taken through the AWS Management Console, AWS SDKs, command line tools, and other AWS services. This event history simplifies security analysis, resource change tracking, and troubleshooting.
- X-Ray cosiderations for EC2
    - Set the namespace subsegment field to aws for AWS SDK calls and remote for other downstream calls
    - Set the metadata object with any additional custom data that you want to store in the segment.
- All data in DynamoDB Streams is subject to a 24 hour lifetime. You can retrieve and analyze the last 24 hours of activity for any given table; however, data older than 24 hours is susceptible to trimming (removal) at any moment.
- The AWS X-Ray SDK does not send trace data directly to AWS X-Ray. To avoid calling the service every time your application serves a request, the SDK sends the trace data to a daemon, which collects segments for multiple requests and uploads them in batches. Use a script to run the daemon alongside your application. To properly instrument your application hosted in an EC2 instance, you have to install the X-Ray daemon by using a user data script.
- AWS WAF is a web application firewall that lets you monitor the HTTP and HTTPS requests that are forwarded to an Amazon API Gateway API, Amazon CloudFront or an Application Load Balancer. AWS WAF also lets you control access to your content. Based on conditions that you specify, such as the IP addresses that requests originate from or the values of query strings, API Gateway, CloudFront or an Application Load Balancer responds to requests either with the requested content or with an HTTP 403 status code (Forbidden). You also can configure CloudFront to return a custom error page when a request is blocked.
- ExposeHeader element refers to the header that will be exposed to the response and not a constraint for the request.
- You can search for segments associated with specific information in the X-Ray console or by using the GetTraceSummaries API.
- Annotations are simple key-value pairs that are indexed for use with filter expressions. Use annotations to record data that you want to use to group traces in the console, or when calling the GetTraceSummaries API. X-Ray indexes up to 50 annotations per trace.
- use the GetTraceSummaries API to get the list of trace IDs of the application and then retrieve the list of traces using BatchGetTraces API in order to develop the custom debug tool.
-  Monitor the IntegrationLatency metrics to measure the responsiveness of the backend.
- Monitor the Latency metrics to measure the overall responsiveness of your API calls.
- Aside from the usual metrics, Cloudwatch logs also tracks the memory, swap, and disk space utilization metrics of your server.
- Cloudwatch received and aggregates from EC2 every 5 min by default For detailed monitoring it is 1 min
- To properly instrument your applications in Amazon ECS, you have to create a Docker image that runs the X-Ray daemon, upload it to a Docker image repository, and then deploy it to your Amazon ECS cluster. You can use port mappings and network mode settings in your task definition file to allow your application to communicate with the daemon container. UDP port 2000
- However, take note that CloudWatch does not monitor the memory, swap, and disk space utilization of your instances. If you need to track these metrics, you can install a CloudWatch agent in your EC2 instances.











- Since the Lambda function returns the result in XML format, it will cause the 502 errors in the API Gateway. Hence, the correct answer is that there is an incompatible output returned from a Lambda proxy integration backend.
- To define a nested application in your serverless application, use the AWS::Serverless::Application resource type.
- AWS::Serverless::Function is incorrect because this resource type describes configuration information for creating a Lambda function. You can describe any event source that you want to attach to the Lambda function—such as Amazon S3, Amazon DynamoDB Streams, and Amazon Kinesis Data Streams.
- You can choose Memcached over Redis if you have the following requirements:
    - You need the simplest model possible.
    - You need to run large nodes with multiple cores or threads.
    - You need the ability to scale out and in, adding and removing nodes as demand on your system increases and decreases.
    - You need to cache objects, such as a database.