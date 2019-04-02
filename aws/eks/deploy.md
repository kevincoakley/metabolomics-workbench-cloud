# Deploying Elastic Kubernetes Service

### Amazon EKS Prerequisites

Complete the following prerequisites at https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html

* Create your Amazon EKS Service Role
* Create your Amazon EKS Cluster VPC
* Install and Configure kubectl for Amazon EKS
* Download and Install the Latest AWS CLI

### Deploy EKS

Get the IAM EKS-Role ARN:

    $ aws iam get-role --role-name EKS-Role | grep Arn

    "Arn": "arn:aws:iam::123456789012:role/EKS-Role",


Get the VPC subnets and security group names from CloudFormation:

    $ aws cloudformation describe-stacks --stack-name eks-vpc | egrep "sg-|subnet-"

    "OutputValue": "sg-00000000000000000",
    "OutputValue": "subnet-00000000000000000,subnet-00000000000000001,subnet-00000000000000002",

Create the EKS cluster:

    $ aws eks --region us-west-2 create-cluster --name workbench-prod --role-arn arn:aws:iam::123456789012:role/EKS-Role --resources-vpc-config subnetIds=subnet-00000000000000000,subnet-00000000000000001,subnet-00000000000000002,securityGroupIds=sg-00000000000000000
    
List Clusters:

    $ aws eks list-clusters

Show Cluster Details:

    $ aws eks describe-cluster --name workbench-prod
