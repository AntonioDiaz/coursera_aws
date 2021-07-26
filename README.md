
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
- [Week 03](#week-03)
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
![cognito_user_pool](https://user-images.githubusercontent.com/725743/127025925-904df7f0-ca98-4a8e-9bb4-f58e19b3616d.png)

### Cognito Federated Identities

## Week 03

## Week 04

## Week 05

## Week 06
