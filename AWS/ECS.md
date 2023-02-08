Amazon Elastic Container **Service** (ECS) helps **schedule and orchestrate containers across a fleet of servers**. It involves installing an agent on each container host that takes instructions from the ECS control plane and relays them to the local Docker image on each one. ECS makes this easy by providing an [optimized Amazon Machine Image (AMI)](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-optimized_AMI.html) that launches automatically using the ECS console or [CLI](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ECS_CLI.html) and that you can use to launch container hosts yourself.

## ECR

Amazon Elastic Container **Registry** (Amazon ECR) is an AWS managed container image registry service that is secure, scalable, and reliable. Amazon ECR supports private repositories with resource-based permissions using AWS IAM. This is so that specified users or Amazon EC2 instances can access your container repositories and images. You can use your preferred CLI to push, pull, and manage Docker images, Open Container Initiative (OCI) images, and OCI compatible artifacts
### Components of Amazon ECR

Amazon [ECR](https://eu-central-1.console.aws.amazon.com/ecr/get-started?region=eu-central-1) contains the following components:

* Registry

An Amazon ECR private registry is provided to each AWS account; you can create one or more repositories in your registry and store images in them. For more information, see [Amazon ECR private registry](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Registries.html).

* Authorization token

Your client must authenticate to Amazon ECR registries as an AWS user before it can push and pull images. For more information, see [Private registry authentication](https://docs.aws.amazon.com/AmazonECR/latest/userguide/registry_auth.html).

* Repository

An Amazon ECR repository contains your Docker images, Open Container Initiative (OCI) images, and OCI compatible artifacts. For more information, see [Amazon ECR private repositories](https://docs.aws.amazon.com/AmazonECR/latest/userguide/Repositories.html).

* Repository policy

You can control access to your repositories and the images within them with repository policies. For more information, see [Private repository policies](https://docs.aws.amazon.com/AmazonECR/latest/userguide/repository-policies.html).

* Image

You can push and pull container images to your repositories. You can use these images locally on your development system, or you can use them in Amazon ECS task definitions and Amazon EKS pod specifications. For more information, see [Using Amazon ECR images with Amazon ECS](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ECR_on_ECS.html) and [Using Amazon ECR Images with Amazon EKS](https://docs.aws.amazon.com/AmazonECR/latest/userguide/ECR_on_EKS.html).




## How to bootstrap
[cdk](https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html)

Bootstrapping is the deployment of an AWS CloudFormation template to a specific AWS environment (account and Region). The bootstrapping template accepts parameters that customize some aspects of the bootstrapped resources (see [Customizing bootstrapping](https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html#bootstrapping-customizing)). Thus, you can bootstrap in one of two ways.

-   Use the AWS CDK Toolkit's **cdk bootstrap** command. This is the simplest method and works well if you have only a few environments to bootstrap.
    
-   Deploy the template provided by the AWS CDK Toolkit using another AWS CloudFormation deployment tool. This lets you use AWS CloudFormation StackSets or AWS Control Tower and also the AWS CloudFormation console or the AWS CLI. You can make small modifications to the template before deployment. This approach is more flexible and is suitable for large-scale deployments.