## AWS Lambda function 

AWS Lambda is a serverless, event-driven compute service that lets you run code for virtually any type of application or backend service without provisioning or managing servers. You can trigger Lambda from over 200 AWS services and software as a service (SaaS) applications, and only pay for what you use.

AWS Lambda automatically runs code in response to [multiple events](http://docs.aws.amazon.com/lambda/latest/dg/intro-core-components.html#intro-core-components-event-sources), such as HTTP requests via [Amazon API Gateway](https://aws.amazon.com/api-gateway/), modifications to objects in [Amazon Simple Storage Service](https://aws.amazon.com/s3/) (Amazon S3) buckets, table updates in [Amazon DynamoDB](https://aws.amazon.com/dynamodb/), and state transitions in [AWS Step Functions](https://aws.amazon.com/step-functions/).

![](https://lh5.googleusercontent.com/O8ae54Nz90nhusbCNrySVc3M_KHbhTAgzC70LXUgQXbe6FhrTBwO-NQI2IJJ0PIXuTyaGIcPwQg09leWDAes8eAOMvfT-JLBK6Ph-AKnZufGS8Omvc62CD-EeXthD2lCRxuzUL0ctjC6KydM-1y9H7edYPYjFfss4YCWVpo_KSNGphIGQuDMHVCL)


### Template anatomy 

[https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html)

  
### Lambda context : 

[https://docs.aws.amazon.com/lambda/latest/dg/python-context.html](https://docs.aws.amazon.com/lambda/latest/dg/python-context.html)


-   When Lambda runs your function, it passes a context object to the [handler](https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html).

-    This object provides methods and properties that provide information about the invocation, function, and execution environment. 

-   For more information on how the context object is passed to the function handler, see [Lambda function handler in Python](https://docs.aws.amazon.com/lambda/latest/dg/python-handler.html).

### Lambda Event


## AWS Lambda Application 

-   An AWS Lambda application is a combination of Lambda functions, event sources, and other resources that work together to perform tasks. 
-   You can use AWS CloudFormation and other tools to collect your application's components into a single package that can be deployed and managed as one resource. 
-   Applications make your Lambda projects portable and enable you to integrate with additional developer tools, such as AWS CodePipeline, AWS CodeBuild, and the AWS Serverless Application Model command line interface (SAM CLI).
-   The [AWS Serverless Application Repository](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/) provides a collection of Lambda applications that you can deploy in your account with a few clicks. The repository includes both ready-to-use applications and samples that you can use as a starting point for your own projects. You can also submit your own projects for inclusion.


## AWS Step Functions

Step Functions is a serverless orchestration service that lets you combine [AWS Lambda](https://aws.amazon.com/lambda/) functions and other AWS services to build business-critical applications. Through Step Functions' graphical console, you see your application’s workflow as a series of event-driven steps.

 Step Functions is based on state machines and tasks.
-  A state machine is a workflow.    
-  A task is a state in a workflow that represents a single unit of work that another AWS service performs. Each step in a workflow is a state.
