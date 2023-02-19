# Serverless computing

In a typical client-server model, you have a server that in continuously running and listening to any http requests even if there are no requests. In a serverless model, every time you make a request, that triggers creation of a function/ a block of functionality. Function is not running in the background, and only created when request is made, and after the request, it gets destroyed.

Pros:
> * Cheap: pay for what you use, and server is not continuously running
> * Fully managed & low complexity: you don’t have to worry about devOps, you don’t have to think about horizontal scaling, Kubernetes management etc.
> * Horizontal scaling means scaling by adding more machines to your pool of resources (also described as “scaling out”), whereas vertical scaling refers to scaling by adding more power (e.g. CPU, RAM) to an existing machine (also described as “scaling up”)

Cons:
> * Cold starts: since function does not exist until a trigger happens, this creation takes some time. However, if function already is spun up, then subsequent requests will have a warm start. Thus, it is a good practice to define anything that can be used between lambda invokations globally.
> * You are locked in with the cloud provider.
> * Not suitable for long-term tasks, they are for small tasks and code executable. They timeout after 15min.

Evolution of serverless programming:
| Hosting Hardware | Computational Units | Architecture styles |
| ----------- | ----------- | ----------- |
| physical centers | physical centers | monolithic (everything in single application) |
| shared data centers | virtual machines | N-Tire apps |
| private cloud | containers | microservices |
| public cloud | functions | nanoservices (e.g. lambda) |

Key principals of nanoservices:
* do one thing
* be more useful than the cost incured
* async event based triggers


BaaS vs FaaS
* Backend as a service: traditionally we have instances in the cloud (virtual machines, docker), on top of them we have server running, on top it we have application process, and on top of it we have classes and functions
* Function as a service: we have a platform that host various functions, and these functions are triggered by a resource. The platform will take care of preparing the environment for running our functions.

---
**Lambda**

AWS lambda is part of compute service. It allows us to create custom code that can be triggered by different AWS resources. These resources need to have permission to invoke our lambda functions which is handled via IAM resource policies. And the lambda function will have an IAM role which will have permissions to access resources it wants to.
To spin up a serverless function, in AWS, they are called lambda:
> Run code without thinking about servers or clusters and pay for what you use.


---
**lambda & API Gateway**

If we are triggering a function with http request, that request first goes through another service called API Gateway. API Gateway is then responsible to trigger the creation of the lambda function. So, you have to first configure the API gateway. You can create functions either through console & setup API Gateway manually, or doing through code (serverless.yml). If you do it through code, AWS will handle the configurations for you so you don’t have to do it yourself. This is called Infrastructure as Code (IaC). This is where serverless framework comes into play. Note that Lambda functions are region specific. To start with serverless framework we will need an IAM account (a user with programatic access) to allow serverless framework and local computer to access AWS services, and attach policies so that the serverless has access only to services that it requires.

---
**lifecycle of lambda**

We you deploy a lambda function, that lambda code will be stored into the AWS lambda repository in the AWS cloud (S3). When lambda is triggered, the Lambda service will create a linux machine, and it adds language runtime, and then pulls the code from the repository, load it into memory, and create an instance of that class. you can invoke lambda from:
* console
* AWS CLI or even within code (script) with the SDK AWS provides
* other resources like S3, dynamoDB, etc

Invocation modes:
* Sync => lambda function expects to return a response right away. Example: API Gateway as trigger of Lambda
* Async => lambda function won't return any response.
* Polling => lambda will do polling for instance from Kinesis data streams, dynamoDB, SQS                                                     

---
**deployment of lambda**

When it comes to deployment, there are steps involved:
* packaging and making it into a zip files
* uploading zip file to S3
* create IAM roles required for creating lambda functions
* create all resources lambda function interacts with
* create lambda functions using lambda service
* configure permissions for lambda to interact with other components

To automate this creation of infrastructure, we can use CloudFormation (IaS), but requires writing a lot of code. Here is where SAM (Serverless Application Model) comes into play. It is a framework and the template file for SAM is much lighter than CloudFormation template. SAM is a superset/extension of CloudFormation. There are other serverless framework, but SAM is for AWS.

---
**parameters & return types**

Every lambda function takes two parameters. An `event` that triggers the lambda and has all the data that lambda can use. And the `context` object that will have all the information about lambda's environment. The data types that we can pass and return from lambda function are:
* simple types (string, float, etc)
* list types
* dictionary types

All information on requestId, build duration, etc will be in `context`. To see full list: [AWS Lambda context object in Python](https://docs.aws.amazon.com/lambda/latest/dg/python-context.html)
```
def first_lambda(event, context):
    return event
```
---
**timeout setting**

You can define a `Timeout` value for your function, so that if execution goes above it, it will timeout. AWS has a max timeout limit of 15min. Default is 3s.

---
**SAM commands**
```
sam validate
sam build
sam deploy --guided
aws cloudformation delete-stack --stack-name <stackname>
sam local start-api # emulate your application's API

# invike lambda remotely. 
# `event` means async (we get `202` response)
# `RequestResponse` is sync (we get `200` response)
aws lambda invoke --invocation-type Event --function-name <FunctionName> outputfile.txt
aws lambda invoke --invocation-type RequestResponse --function-name <FunctionName> outputfile.txt
```
---
**SAM template**

* In SAM templates, in order to reference a resource which is not created and does not have a arn yet, you can use intrinsic functions and just reference the resource name in the templat. `!Ref` is the syntax. There are many more intrisic functions available, like `!Sub`, `!GetAtt`, see: [intrinsic function reference - AWS CloudFormation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference.html)

---
**DeadLetterQueue and error handling**
In Sync invocation, we can handle the exception via try-except block and return HTTP status code,but in case of ASync invocation of lambda, there is no upstream component waiting, that is where we need a retry logic, and after retry, we will then log the exception and give up. In this case, the message/data is lost. This is where DeadLetterQueue (DLQ) comes in (like in any messaging service); the event will be forwarded to the DLQ that we configure, and we can also configure a lambda function for instance that can read this message and do something that we want.

---
**SQS as event**
When API Gateway is used as trigger for lambda, the entire process in Sync. To make this aSync, we can insert a SQS (Simple Queue Service) between API Gateway and Lambda, to act as Lambda's trigger. Here, when client sends a message, API Gateway sends this message to the queue and SQS will return a unique id (messageId) back to the client to track the message. It is the responsibility of lambda service to poll message from SQS. Whenever there is a message, it will invoke the lambda function. The lambda services reads messages in batches, by default in batches of 5. The number of messages in each batch can be configured as well. When it hands a batch to a lambda, this batch will be hidden to other lambdas (VisibilityTimeoutSetting). Also, if a message fails in the batch, the entire batch will be processed again, so our lambda should handle duplicated message (which were already processed the first time). And if message fails too many time, we can again configure DeadLetterQueue.

---
**Serverless framework and `serverless.yaml`**

The Serverless Framework is a free and open-source web framework written using Node.js. Serverless is the first framework developed for building applications on AWS Lambda. Applications developed with Serverless can be deployed to other function as a service providers, including Microsoft Azure with Azure Functions, IBM Bluemix with IBM Cloud Functions based on Apache OpenWhisk, Google Cloud using Google Cloud Functions, Oracle Cloud using Oracle Fn, Kubeless based on Kubernetes, Spotinst and Webtask by Auth0.

* How to deploy your stack, `serverless deploy` if you have modified the yaml file; else if you have just modified the function code `serverless deploy function --function myFunction`. Serverless terminates the deployment process if all file hashes are the same. Each deployment publishes a new version for each function in your service. Zip files of your Functions' code are uploaded to your Code S3 Bucket. More info in [Serverless Framework - Deploying to AWS ](https://www.serverless.com/framework/docs/providers/aws/guide/deploying).
* Service (a.k.a. project) is the framework’s unit of organisation, and is defined when you first run the serverless command to create the template project. However, later on, when an application grows, you can break it out into multiple services. If you are using AWS as a provider, all functions inside the service are AWS Lambda functions.
* All of the Lambda functions in your serverless service can be found in `serverless.yml` under the functions property. The handler property points to the file and module containing the code you want to run in your function. Your functions can either inherit their settings from the provider property or you can specify properties at the function level.
* Every AWS Lambda function needs permission to interact with other AWS infrastructure resources within your account. These permissions are set via an AWS IAM Role. You can set permission policy statements within this role via the `provider.iam.role.statements` property.
* When building serverless applications, it is quite common to have code that is shared across Lambda functions. It can be your custom code, that is used by more than one function, or a standard library, that you add to simplify the implementation of your business logic. Previously, you would have to package and deploy this shared code together with all the functions using it. Now, you can put common components in a ZIP file and upload it as a Lambda Layer. Your function code doesn’t need to be changed and can reference the libraries in the layer as it would normally do
* Parameters can be used for storing secrets and adopting stage-specific configurations:
* Interesting functionality “Deploying multiple services in a monorepository via Serverless Framework Compose“: [Serverless Framework - Composing services](https://www.serverless.com/framework/docs/guides/compose)

---
**References**
1. [Serverless Framework with AWS Lambda Crash Course](https://youtu.be/woqLi6NEW58)
2. [Serverless using AWS Lambda for Python Developers](https://www.udemy.com/share/104hyi3@XcxtA57s7nlBuyBqOJTpFyYUKlB6cNklXyIjvFLXmpT8ejBXo9SSxt-yHEZxTn0_DA==/)