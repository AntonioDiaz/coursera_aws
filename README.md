
# Building Modern Java Applications on AWS

<!-- TOC updateonsave:true depthfrom:2 -->

- [Intro](#intro)
- [Labs](#labs)
- [Week 01](#week-01)
    - [Reading: Cloud9, AWS APIs, AWS CLI](#reading-cloud9-aws-apis-aws-cli)
    - [Testing Cloud9](#testing-cloud9)
    - [Lab 1](#lab-1)
- [Week 02 - API Gateway](#week-02---api-gateway)
    - [Mocking GET Response](#mocking-get-response)
    - [Mocking POST](#mocking-post)
    - [Models, Mappings, Request Validation Notes](#models-mappings-request-validation-notes)
    - [Cognigo User Pool](#cognigo-user-pool)
    - [Cognito Federated Identities](#cognito-federated-identities)
    - [Api Gateway and Cognito workflow](#api-gateway-and-cognito-workflow)
- [Week 03 - Lambda](#week-03---lambda)
    - [Intro Lambda](#intro-lambda)
    - [The Lambda Execution Environment](#the-lambda-execution-environment)
    - [Lambda Permissions](#lambda-permissions)
    - [Lambda Execution](#lambda-execution)
    - [AWS Lambda Push/Pull Model](#aws-lambda-pushpull-model)
    - [Security and Compliance with AWS Lambda](#security-and-compliance-with-aws-lambda)
    - [Asynchronous vs Synchronous Lambda Functions](#asynchronous-vs-synchronous-lambda-functions)
    - [Versions and Alias](#versions-and-alias)
- [Week 04](#week-04)
- [Week 05](#week-05)
- [Week 06](#week-06)

<!-- /TOC -->

## Intro
https://www.coursera.org/learn/building-modern-java-applications-on-aws/


## Labs
* Lab 1:  
Create a static __Amazon S3__ website with a bucket policy that restricts access to the website via IP Address.
The website will be created using the AWS SDK and AWS CLI.

* Lab 2:  
Setup mock backend API using Amazon __API Gateway__ REST APIs. 
You will setup 3 API endpoints using the AWS SDK and AWS CLI, these endpoints will respond to requests with mocked data. 
You will test this mock API using the website setup in Lab 1 make AJAX calls to the mock API.

* Lab 3:  
Secure the API that was built in Lab 2 by adding authentication via __Amazon Cognito__ User Pools.

* Lab 4:  
Create AWS __Lambda functions__ to host the backend for your API. You will then configure the secured API built in Lab 3 to trigger to the lambda functions, instead of using mock integrations.

* Lab 5:  
Create an asynchronous state machine using AWS __Step Functions__ for a reporting feature of the API. You will then configure the API to run this state machine when a request hits an API endpoint you built in the previous labs.

* Lab 6:  
Use AWS __X-Ray__ to trace requests through your distributed application. You will also make improvements to your application using various AWS service features like Amazon API Gateway Response Caching, as well as code modifications. Then you will test and view the performance improvements in the AWS X-Ray Console.


## Week 01

* Architecture  
<img width="1277" alt="Screenshot 2021-07-11 at 19 50 45" src="https://user-images.githubusercontent.com/725743/125205377-35baea00-e282-11eb-8899-a1726efcbdad.png">


* Into to Cloud9
https://aws.amazon.com/es/cloud9/

* Introduction to AWS API Management Console CLI SDK
<img width="1277" alt="Screenshot 2021-07-11 at 19 50 45" src="https://user-images.githubusercontent.com/725743/125206020-3d2fc280-e285-11eb-8f29-647964393d6a.png">

### Reading: Cloud9, AWS APIs, AWS CLI
* AWS APIs  
    * Everything in AWS is an API call and every AWS Service has its own set of APIs that you interact with.  
    * When you send HTTP requests to AWS, you sign the requests so that AWS can identify who sent them. 
    * You sign requests with your AWS access key, which consists of an __access key ID__ and __secret access key__. You need to sign HTTP requests only when you manually create them. 
    * When you use the AWS Command Line Interface (AWS CLI) or one of the AWS SDKs to send requests to AWS, these tools automatically sign the requests for you with the access key that you specify when you configure the tools. 
    * When you use these tools, you don't need to learn how to sign requests yourself. 
    * To read about how requests need to be signed click here: https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html
    
* AWS Command Line Interface
    * The AWS Command Line Interface (AWS CLI) is an open source tool that enables you to interact with AWS services using commands in your command-line shell. 
    * Installation of the AWS Command Line Interface is fairly straightforward, and __if you’re using Amazon EC2 instances or AWS Cloud9, the tools are already installed for you__.
    * After configuration, the AWS CLI enables you to start running commands that implement functionality equivalent to that provided by the browser-based AWS Management Console from the command prompt in your favorite terminal program:
        * __Linux shells__ – Use common shell programs such as bash, zsh, and tcsh to run commands in Linux or macOS.
        * __Windows command line__ – On Windows, run commands at the Windows command prompt or in PowerShell.
        * __Remotely__ – Run commands on Amazon Elastic Compute Cloud (Amazon EC2) instances through a remote terminal program such as PuTTY or SSH, or with AWS Systems Manager.
    * Read information about the AWS CLI at: https://aws.amazon.com/cli/ 
    * Read information about installing the AWS CLI at: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html
    * Read information about configuring the AWS CLI at: https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html

* AWS Cloud9
    * AWS Cloud9 operates as a cloud-based IDE, and has a wide variety of functionality already built in to help you with the development of and collaboration of your projects. A particular area where Cloud9 can assist is when working with the AWS Command Line Interface. Because the CLI tools are already installed in your environment, all you need to do is configure them to get started. 
    * You access the AWS Cloud9 IDE through a web browser. You can configure the IDE to your preferences. You can switch color themes, bind shortcut keys, enable programming language-specific syntax coloring and code formatting, and more.  
    * Read more about Cloud9 at: https://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html
    * AWS Cloud9 managed temporary credentials have permission restrictions on their own which may restrict some API calls you are using. You can find the list of those restrictions at: https://docs.aws.amazon.com/cloud9/latest/user-guide/how-cloud9-with-iam.html#sec-auth-and-access-control-temporary-managed-credentials

* AWS Credentials and Programmatic Access
    * AWS Credentials and Programmatic Access
Read about best practices with AWS Credentials here: https://docs.aws.amazon.com/general/latest/gr/aws-access-keys-best-practices.html

### Testing Cloud9
```shell
Check Java Version
    java -version
    
Update Java Version
    sudo yum install java-1.8.0-openjdk-devel
    
Configure Java 
    sudo alternatives --config java

Remove Old Java Version
    sudo yum remove java-1.7.0-openjdk-devel
    
Check Version  
    java -version
    
Download Maven
    sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo 
    sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo

Install Apache Maven
    sudo yum install -y apache-maven
    
Chevk Maven Version
    mvn -v
    
Set Java Home
    export JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64
Check Maven Version
    mvn -v
````

* AWS SDK for Java uses unchecked exceptions
    * AmazonServiceException
    * AmazonClientException

* Default credential chain
    * Environment variables
    * Java system properties
    * Default credentials profile
    * Amazon EXS credential provider
    * Instance profile credentials (IAM role)

### Lab 1
Get the front end up, by uploading a simple front-end where a user can search for an item.
* Steps  
    1. How to create a new S3 bucket using the command line (terminal) on Cloud9. Or the CMD_LINE.
    2. How to upload specific access permissions for the hosted website, and how to use an IP condition rule to only allow access to a specific IPv4 address.
    3. How to upload a local website to an S3 bucket, using a script leveraging the AWS SDK (called from Cloud9).  All while setting metadata on those objects.
    4.[Optional] You will learn what the JS code in the website is doing if you care are interested. This will give you a heads up for how the APIs are being liaised with for the later labs.

* Prepare environment:
    * Move to folder: `cd /home/ec2-user/environment`
    * 
    
* Command create bucket:
`aws s3api create-bucket --bucket ada-2021-07-14-s3site --region us-west-2 --create-bucket-configuration LocationConstraint=us-west-2`
* Create permision file: website_security_policy.json
```
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": [
                "arn:aws:s3:::ada-2021-07-14-s3site/*",
                "arn:aws:s3:::ada-2021-07-14-s3site"
            ],
            "Condition": {
                "IpAddress": {
                    "aws:SourceIp": [
                        "81.61.227.13/32"
                    ]
                }
            }
        },
        {
            "Sid": "DenyOneObjectIfRequestNotSigned",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::ada-2021-07-14-s3site/report.html",
            "Condition": {
                "StringNotEquals": {
                    "s3:authtype": "REST-QUERY-STRING"
                }
            }
        }
    ]
}
```

https://ada-2021-07-14-s3site.s3-us-west-2.amazonaws.com/index.html

## Week 02 - API Gateway
* Models and Mapping
    * Validate request / responses with models
    * Transform request / resposes with mappings
### Mocking GET Response
* Go to Integration Response
* On mapping templates copy the response.  
<img width="1225" alt="integration_response" src="https://user-images.githubusercontent.com/725743/126361216-6f3db535-1cc4-45a4-ac44-ffcac17e1cf0.png">
* Mock response example

```json
    [
    #if ($input.params('role')=="manager")
    {   
        "name": "Diego Pablo Simeone",
        "role": "manager"
    },{  
        "name": "Luis Aragones",
        "role": "manager"
    }
    #elseif ($input.params('role')=="player")
    {  
        "name": "Diego Godin",
        "role": "player"
    }
    #end
    ]
```

### Mocking POST
* Generate the Model on the left menu "Models"  
```json
{
  "$schema": "http://json-schema.org/draft-04/schema#",
  "type": "object",
  "properties": {
    "name": {
      "type": "string"
    },
    "role": {
      "type": "string"
    }
  },
  "required": [
    "name",
    "role"
  ]
}
```

* Create the POST method
* On Method Request, select the new mapping on the "Request body" and "validate body".

<img width="976" alt="api_gateway_model_validation" src="https://user-images.githubusercontent.com/725743/126364081-427842df-473f-4df5-a47e-7adcf58cd190.png">


### Models, Mappings, Request Validation Notes
* Request Validation
    * API Gateway can perform basic validation. This enables you, the API developer, to focus on app-specific deep validation in the backend. You can offload basic validation to API Gateway. 
    * For the basic validation, API Gateway verifies either or both of the following conditions:
        * The required request parameters in the URI, query string, and headers of an incoming request are included and non-blank.
        * The applicable request payload adheres to the configured JSON schema request model of the method.
        * Validation is performed in the Method Request and Method Response of the API. You can associate models to validate against at the Method Request or Method Reponse level for each HTTP method. This means that different HTTP methods under a resource can use different models.  

    * Read more about Request Validation at: https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-method-request-validation.html
* Models  
    * A model in API Gateway allows you to define a schema for validating requests and responses.

    * The model is used to define the format of the incoming data on the Method Request, or the format of the outgoing data on the Method Response. 

    * For instructions on how to create a Model click here: https://docs.aws.amazon.com/apigateway/latest/developerguide/how-to-create-model.html

* Mappings  
    * Mappings are templates written in Velocity Template Language (VTL) that you can apply to the Integration Request or Integration Response of a REST API. The mapping template allows you to transform data, including injecting hardcoded data, or changing the shape of the data before it passes to the backing service or before sending the response to the client. 

    * You can access information on the payload of the request and other data in your mapping template by using variables. Read about the variables that are available to use in your VTL mappings here: https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html  
    
* Stage Variables
    * Stage variables are name-value pairs that you can define as configuration attributes associated with a deployment stage of a REST API. They act like environment variables and can be used in your API setup and mapping templates.

    * With deployment stages in API Gateway, you can manage multiple release stages for each API, such as alpha, beta, and production. Using stage variables you can configure an API deployment stage to interact with different backend endpoints. 

    * You can reference stage variables in Mapping templates through the use of `$stageVariables`. 

    * Read more about stageVariables here: https://docs.aws.amazon.com/apigateway/latest/developerguide/stage-variables.html

    * Read more about how to use $stageVariables and other variables and functions you can use in your Mapping templates here: https://docs.aws.amazon.com/apigateway/latest/developerguide/api-gateway-mapping-template-reference.html    

### Cognigo User Pool
![cognito_user_pool_02](https://user-images.githubusercontent.com/725743/127027542-b99b2a30-7460-4f5d-8850-46c5dc02f281.png)
![cognito_user_pool](https://user-images.githubusercontent.com/725743/127025925-904df7f0-ca98-4a8e-9bb4-f58e19b3616d.png)

### Cognito Federated Identities
![cognito_federated_identities](https://user-images.githubusercontent.com/725743/127026939-07cefd1d-9361-4b45-bb31-f882dd039f8b.png)
![cognito_federated_identities_02](https://user-images.githubusercontent.com/725743/127027556-ca51aadc-f861-4f30-9ef1-47d5bfe1e553.png)

### Api Gateway and Cognito workflow
![api_gateway_flow](https://user-images.githubusercontent.com/725743/127653721-76c34722-0b08-4629-90eb-c391a98ced68.png)


## Week 03 - Lambda
* Event 
* Concurrency
* Trigger
* Permision
    * Execution permissions: What your Lambda function can do. Minimun permision are definded: AwsLambdaBasicExecutionRole.
    * Resouces-based policies: what can invoke or manage your Lambda function.

### Intro Lambda  
* AWS Lambda is a compute service that lets you run code without provisioning or managing servers. AWS Lambda executes your code only when needed and scales automatically, from a few requests per day to thousands per second. You pay only for the compute time you consume - there is no charge when your code is not running. With AWS Lambda, you can run code for virtually any type of application or backend service - all with little to no administration in regards to environment provisioning and scaling.

* Read the Developer Guide for AWS Lambda here: https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html

### The Lambda Execution Environment  
* When AWS Lambda executes your Lambda function, it provisions and manages the resources needed to run your Lambda function. When you create a Lambda function, you specify configuration information, such as the amount of memory and maximum execution time that you want to allow for your Lambda function. 

* It takes time to set up an execution context and do the necessary "bootstrapping", which adds some latency each time the Lambda function is invoked. You typically see this latency when a Lambda function is invoked for the first time or after it has been updated because AWS Lambda tries to reuse the execution context for subsequent invocations of the Lambda function.

* After a Lambda function is executed, AWS Lambda maintains the execution context for some time in anticipation of another Lambda function invocation. In effect, the service freezes the execution context after a Lambda function completes, and thats the context for reuse, if AWS Lambda chooses to reuse the context when the Lambda function is invoked again. This execution context reuse approach has the following implications:

    * Objects declared outside of the function's handler method remain initialized, providing additional optimization when the function is invoked again. For example, if your Lambda function establishes a database connection, instead of reestablishing the connection, the original connection is used in subsequent invocations. We suggest adding logic in your code to check if a connection exists before creating one.

* Each execution context provides __512 MB__ of additional disk space in the /tmp directory. The directory content remains when the execution context is frozen, providing transient cache that can be used for multiple invocations. You can add extra code to check if the cache has the data that you stored. For information on deployment limits, see AWS Lambda limits.

* Background processes or callbacks initiated by your Lambda function that did not complete when the function ended resume if AWS Lambda chooses to reuse the execution context. You should make sure any background processes or callbacks in your code are complete before the code exits.

* Read more about the Lambda execution context here: https://docs.aws.amazon.com/lambda/latest/dg/runtimes-context.html

### Lambda Permissions
There are two types of permissions to be aware of when working with AWS Lambda. 
* __Execution Permissions__   
A lambda function’s execution permissions are controlled by an IAM Role. When you create a lambda function you associate an IAM Role with the function. This Role will contain policies that either allow or deny specific API calls to be made. In order for the code running in the lambda function to make calls to AWS APIs the execution role must contain the permissions for those API calls. Read more about execution permissions by clicking here: https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html 

* __Resource-based policies__.  
Resource-based policies are used to control access to invoking and managing a lambda function. Resource-based policies let you grant usage permission to other accounts on a per-resource basis. You also use a resource-based policy to allow an AWS service to invoke your function.

* For Lambda functions, you can grant an account permission to invoke or manage a function. You can add multiple statements to grant access to multiple accounts, or let any account invoke your function. To read more about resource-based permissions click here: https://docs.aws.amazon.com/lambda/latest/dg/access-control-resource-based.html

### Lambda Execution  
When your code executes in Lambda, the service will spin up a micro-virtual machine that will download your code and all the dependencies required and then execute it. The technology used for those micro-VMs is called __Firecracker__ which is an open source virtualization technology. AWS created this technology with Lambda in mind as well as for multi-tenant container which is being used in the AWS Fargate service. By open-sourcing it, we now allow anyone to contribute and expand the capabilities of this technology. It also allows anyone to use this to build their own version of Lambda on-premises if you really wanted to. You can find more information about this technology here:   

https://firecracker-microvm.github.io/


### AWS Lambda Push/Pull Model
* There are multiple ways to invoke lambda functions. You can invoke the function directly by calling the invoke API as shown in the demos, or you can setup triggers to invoke the lambda function. 

* Read about how other AWS Services integrate with AWS Lambda here: https://docs.aws.amazon.com/lambda/latest/dg/lambda-services.html

* When setting up a trigger, there are two main models: 
    1.  __The push model__  
        * Is where a trigger sends an event to lambda, which then invokes your lambda function. When the push model is used, the __resource-based policy__ must allow the trigger to invoke the lambda function.  
        * Examples: S3, API Gateway.
    <img width="1409" alt="lambda_push" src="https://user-images.githubusercontent.com/725743/127732085-fc71cdcf-bd71-4146-aaae-7240ff114650.png">

    2. __The pull model__  
        * Some AWS Services being configured as triggers for lambda functions do not push events to lambda, but instead are the __source of events__, and AWS Lambda pulls the event from the event source, and then invokes the lambda function.  
        * A common example of a trigger that follows this model is Amazon Simple Queue Service (SQS). SQS allows messages to build up in a queue, and then your code would process those messages. Instead of having SQS invoke the lambda every time it gets a message, the messages build up in the queue and you define an __Event Source Mapping__ as the trigger for the lambda function.    
        * AWS Lambda will pull the events from SQS to the lambda function. You can configure how many messages to pull and at what frequency. 
        * Your code does not need to handle polling the queue for messages, instead the lambda service does that for you and passes the messages to your code through it’s event. 
        * Services that follow pull model: SQS, Amazon Kinesis, Amazon DynamoBD Stream.
        * Execution Permision on the Lambda must include the service where you need to pull. 
        <img width="500" alt="lambda_pull" src="https://user-images.githubusercontent.com/725743/127732314-402c118e-ab29-4319-86b0-ff06988f8c68.png">
        * Read more about Event Source Mappings here: https://docs.aws.amazon.com/lambda/latest/dg/invocation-eventsourcemapping.html

### Security and Compliance with AWS Lambda
You must remember to apply the shared responsibility model when using AWS Lambda. 
The following documentation shows you how to configure Lambda to meet your security and compliance objectives: https://docs.aws.amazon.com/lambda/latest/dg/lambda-security.html

* Data Protection
    * Encryption in transit: AWS Lambda endpoints only supports secure connections over HTTPS.
    * Encryption at rest: AWS Lambda always encrypt environment variables at rest.
    * Key configurations allow you to use an encryption key managed by AWS Management Service.
    * Encryption Helpers enable encryption of environment variables on the client side.
* Access services inside VPC through ENI (Elastic Network Interface).
<img width="500" alt="lambda_vpc_eni" src="https://user-images.githubusercontent.com/725743/127744624-87477281-df42-416a-98f1-93c33c19028c.png">

### Asynchronous vs Synchronous Lambda Functions
* When invoking a Lambda function via the CLI or the SDK, the type of invocation (synchronous or asynchronous) can be selected based on the API call used. When using an AWS service to trigger the Lambda function, the type of invocation is typically decided by the trigger configured. For example, S3 invokes Lambda asynchronously. 

* However, there are certain services that allows the type of invocation to be selected. Amazon API Gateway is one of those. The default setting is for API Gateway to invoke Lambda synchronously. 
* For most REST API operations, the client will want a response from the Lambda function so it makes sense to have Lambda executed synchronously during those times. 
* In the case of a long-latency operation, it would make sense to invoke it asynchronously and use another mechanism to let the client know that the operation is completed. That’s why it’s possible to choose how to invoke Lambda in API Gateway. 
* However, this option is not available when using the __Lambda proxy__ feature of API Gateway. Lambda must be configured with a custom integration. Then, the method execution can be configured to invoke the Lambda function asynchronously using the __‘X-Amz-Invocation-Type’__ header set to ‘Event’ in the Integration Request. Or the client can decide which type of invocation to use by setting that header in the request to API Gateway. More information can be found here:

* https://docs.aws.amazon.com/apigateway/latest/developerguide/set-up-lambda-integration-async.html

* Remember to configure lambda destinations when using asynchronous invocations.

### Versions and Alias
* __Version__ includes the code and all associated dependencies, the Lambda run-time for the function, all of the setting and environment variables, and a unique Amazon resource name or ARN.
* __Alias__ is like a pointer to a specific Lambda function version and just like the functions and versions, it can be accessed by utilizing the alias ARN.

* AWS Lambda versions and aliases can help to provide a lot of control when managing your function code. Make sure you understand their base concepts to best utilize these features.

* You can publish a new version of your AWS Lambda function when you create new or update existing functions. Each version of a lambda function gets it’s own unique Amazon Resource Name (ARN). You can then use these ARNs to use different versions of the Lambda function for different purposes.

* You also have the ability to create aliases for AWS Lambda functions. Aliases are essentially pointers to one specific Lambda version.  

* Each alias you create has a unique ARN. An alias can only point to one function version, not to another alias. You can update an alias to point to a new version of the function.

* This is handy for a number of reasons, one being the following: consider event sources such as Amazon S3 which can be configured to invoke your Lambda function. These event sources maintain a mapping that identifies the function to invoke when events occur. If you specify a Lambda function alias in the mapping configuration, you don't need to update the mapping when the function version changes.

* Read about versioning here: https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html

* Read about aliases here: https://docs.aws.amazon.com/lambda/latest/dg/configuration-aliases.html

### Creating a Java Lambda Function 
* Objects in handler:
    * __Event__: information on the request
        * You can define your own custom import type
    * __Context__: informatin about the invocation
        * AWS request ID
        * Lambda function version
        * ARN
        * Execution environment details
        * Cognito identity Information
    * __Return__ can be a primitive or a json.

* You can run Java code in AWS Lambda. Lambda provides runtimes for Java that execute your code to process events. Your code runs in an Amazon Linux environment that includes AWS credentials from an AWS Identity and Access Management (IAM) role that you manage.

* Read more about Java Lambda Functions at: https://docs.aws.amazon.com/lambda/latest/dg/lambda-java.html

* Your Lambda function's __handler__ is the method in your function code that processes events. When your function is invoked, Lambda runs the handler method. When the handler exits or returns a response, it becomes available to handle another event. A handler for a Java Lambda function can take multiple forms. 

* For Java functions, you can use POJOs for custom input and output types. Read about handlers here: https://docs.aws.amazon.com/lambda/latest/dg/java-handler.html

* To create a Java Lambda Function you need to write your code and then package it up into a __deployment package__. A deployment package is a ZIP archive that contains your compiled function code and dependencies. You can upload the package directly to Lambda, or you can use an Amazon S3 bucket, and then upload it to Lambda. If the deployment package is larger than 50 MB, you must use Amazon S3.

* Read about how to create a deployment package for a Java Lambda Function at: https://docs.aws.amazon.com/lambda/latest/dg/java-package.html

* Read about how to create an AWS Lambda function with the AWS Toolkit at: https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/create-new-lambda.html

* Read about running and debugging an AWS Lambda function with the AWS Toolkit at: https://docs.aws.amazon.com/toolkit-for-jetbrains/latest/userguide/invoke-lambda.html

### Handler GET Dragons
```java
package com.mycompany.app;

//imports ...

public class App implements RequestHandler<APIGatewayProxyRequestEvent, APIGatewayProxyResponseEvent> {

    private static final AWSSimpleSystemsManagement ssm = AWSSimpleSystemsManagementClientBuilder.defaultClient();
    private static final AmazonS3 s3Client = AmazonS3ClientBuilder.defaultClient();

    @Override
    public APIGatewayProxyResponseEvent handleRequest(APIGatewayProxyRequestEvent event, Context context) {
        String dragonData = readDragonData(event);
        return generateResponse(dragonData);
    }

    protected static String readDragonData(APIGatewayProxyRequestEvent event) {
        Map<String,String> queryParams = event.getQueryStringParameters();
        String bucketName = getBucketName();
        String key = getKey();
        String query = getQuery(queryParams);
        SelectObjectContentRequest request = generateJSONRequest(bucketName, key, query);
        return queryS3(request);
    }

    private static String queryS3(SelectObjectContentRequest request) {
        final AtomicBoolean isResultComplete = new AtomicBoolean(false);
        // Call S3 Select
        SelectObjectContentResult result = s3Client.selectObjectContent(request);

        // Anonymous inner class implementation
        // to define what actions to complete 
        // for every object in the result stream
        InputStream resultInputStream = result.getPayload().getRecordsInputStream(
                new SelectObjectContentEventVisitor() {
                    @Override
                    public void visit(SelectObjectContentEvent.StatsEvent event)
                    {
                        System.out.println(
                                "Received Stats, Bytes Scanned: " + event.getDetails().getBytesScanned()
                                        +  " Bytes Processed: " + event.getDetails().getBytesProcessed());
                    }

                    /*
                     * An End Event informs that the request has finished successfully.
                     */
                    @Override
                    public void visit(SelectObjectContentEvent.EndEvent event)
                    {
                        isResultComplete.set(true);
                        System.out.println("Received End Event. Result is complete.");
                    }
                }
        );

        String text = null;
        try {
            text = IOUtils.toString(resultInputStream, StandardCharsets.UTF_8.name());
        } catch (IOException e) {
            // In the real world, you should do actual error handling here
            // do not just log a message and move on in a production system
            System.out.println(e.getMessage());
        }
        return text;
    }

    private static SelectObjectContentRequest generateJSONRequest(String bucketName, String key, String query) {
        SelectObjectContentRequest request = new SelectObjectContentRequest();
        request.setBucketName(bucketName);
        request.setKey(key);
        request.setExpression(query);
        request.setExpressionType(ExpressionType.SQL);

        InputSerialization inputSerialization = new InputSerialization();
        inputSerialization.setJson(new JSONInput().withType("Document"));
        inputSerialization.setCompressionType(CompressionType.NONE);
        request.setInputSerialization(inputSerialization);

        OutputSerialization outputSerialization = new OutputSerialization();
        outputSerialization.setJson(new JSONOutput());
        request.setOutputSerialization(outputSerialization);

        return request;
    }

    private static String getBucketName() {
        GetParameterRequest bucketParameterRequest = new GetParameterRequest().withName("dragon_data_bucket_name").withWithDecryption(false);
        GetParameterResult bucketResult = ssm.getParameter(bucketParameterRequest);
        return bucketResult.getParameter().getValue();
    }

    private static String getKey() {
        GetParameterRequest keyParameterRequest = new GetParameterRequest().withName("dragon_data_file_name").withWithDecryption(false);
        GetParameterResult keyResult = ssm.getParameter(keyParameterRequest);
        return keyResult.getParameter().getValue();
    }

    private static String getQuery(Map<String,String> queryParams) {
        if(queryParams != null) {
            System.out.println("we have params");
            if (queryParams.containsKey("family")) {
                System.out.println(queryParams.get("family"));
                return "select * from S3Object[*][*] s where s.family_str =  '" + queryParams.get("family") + "'";

            } else if (queryParams.containsKey("dragonName")) {
                return "select * from S3Object[*][*] s where s.dragon_name_str =  '" + queryParams.get("dragonName") + "'";
            }
        }

        return "select * from s3object s";
    }
    
    private static APIGatewayProxyResponseEvent generateResponse(String dragons) {
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        APIGatewayProxyResponseEvent response = new APIGatewayProxyResponseEvent();
        response.setStatusCode(200);
        response.setBody(gson.toJson(dragons));
        return response;
    }
}
```

### Lambda Commands
* Create (first we need to build project and create the zip):  
`aws lambda create-function --function-name listDragons --runtime java8  --role <IAM ROLE ARN> --handler com.mycompany.app.App::handleRequest --publish --zip-file fileb://my-app.zip --timeout 90 --memory-size 448`
* Execute:  
`aws lambda invoke --function-name listDragons output.txt`

## Week 04

## Week 05

## Week 06
