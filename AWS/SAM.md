## AWS SAM

 AWS Serverless Application Model (SAM)

-   The AWS Serverless Application Model (SAM) is an open-source framework for building serverless applications. 
-   It provides shorthand syntax to express functions, APIs, databases, and event source mappings. With just a few lines per resource, you can define the application you want and model it using YAML.    
-   During deployment, SAM transforms and expands the SAM syntax into AWS CloudFormation syntax, enabling you to build serverless applications faster.


[AWS SAM](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification.html) is a model used to define serverless applications on AWS.

Serverless applications are applications composed of functions triggered by events. A typical serverless application consists of one or more AWS Lambda functions triggered by events such as object uploads to [Amazon S3](https://aws.amazon.com/s3), [Amazon SNS](https://aws.amazon.com/sns) notifications, and API actions. Those functions can stand alone or leverage other resources such as [Amazon DynamoDB](https://aws.amazon.com/dynamodb) tables or S3 buckets. The most basic serverless application is simply a function.

## Configuration IDE
#### Pycharm
 [AWS with pycharm](https://medium.com/@bezdelev/how-to-test-a-python-aws-lambda-function-locally-with-pycharm-run-configurations-6de8efc4b206)

#### VSCODE

```shell
sam build 

sam deploy -guided 

sam local invoke PreProcessLiftFunction -e events/event.json
```
