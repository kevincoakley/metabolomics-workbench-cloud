# Adding EKS Kubernetes Workers

Get the Security Group and VPC IDs of the EKS-VPC:

    $ aws cloudformation describe-stacks --stack-name eks-vpc | egrep "sg-|vpc-"

    "OutputValue": "sg-00000000000000000",
    "OutputValue": "vpc-00000000000000000",

Get the Subnet ID in us-west-2a for the VPC:

    $ aws ec2 describe-subnets --filters "Name=vpc-id,Values=vpc-00000000000000000" "Name=availability-zone,Values=us-west-2a" | grep subnet- | grep -v Arn

    "SubnetId": "subnet-00000000000000000",

Select a EC2 Key Pair for the instance:

    $ aws ec2 describe-key-pairs

Get the AMI ID for the eks-optimized EC2 image:

    https://docs.aws.amazon.com/eks/latest/userguide/eks-optimized-ami.html
    
A modified version of the EKS CloudFormation template from https://amazon-eks.s3-us-west-2.amazonaws.com/cloudformation/2019-02-11/amazon-eks-nodegroup.yaml to support spot instances has been uploaded to https://s3-us-west-2.amazonaws.com/mw-ucsd-cf/amazon-eks-spot-nodegroup.yaml
    
Launch the EKS Kubernetes Workers using the CloudFormation template:
    
    $ aws cloudformation create-stack --stack-name eks-us-west-2a-spot --template-url https://s3-us-west-2.amazonaws.com/mw-ucsd-cf/amazon-eks-spot-nodegroup.yaml --parameters ParameterKey=ClusterName,ParameterValue=workbench-prod ParameterKey=ClusterControlPlaneSecurityGroup,ParameterValue=sg-00000000000000000 ParameterKey=NodeGroupName,ParameterValue=eks-us-west-2a-spot ParameterKey=NodeAutoScalingGroupMinSize,ParameterValue=1 ParameterKey=NodeAutoScalingGroupDesiredCapacity,ParameterValue=1 ParameterKey=NodeAutoScalingGroupMaxSize,ParameterValue=3 ParameterKey=NodeInstanceType,ParameterValue=m5.xlarge ParameterKey=NodeImageId,ParameterValue=ami-00000000000000000 ParameterKey=NodeVolumeSize,ParameterValue=20 ParameterKey=KeyName,ParameterValue=KEYPAIR_NAME ParameterKey=VpcId,ParameterValue=vpc-00000000000000000 ParameterKey=Subnets,ParameterValue=subnet-00000000000000000 ParameterKey=SpotPrice,ParameterValue=1 --capabilities CAPABILITY_IAM
    
Wait for the CloudFormation Stack status to become CREATE_COMPLETE:

    $ aws cloudformation describe-stacks --stack-name eks-us-west-2a-spot | grep StackStatus
    
    "StackStatus": "CREATE_COMPLETE",
    
Get the EKS Kubernetes Worker's NodeInstanceRole from CloudFormation:

    $ aws cloudformation describe-stacks --stack-name eks-us-west-2a-spot | grep eks-us-west-2a-spot-NodeInstanceRole
    
    "OutputValue": "arn:aws:iam::123456789012:role/eks-us-west-2a-spot-NodeInstanceRole-000000000000"
    
Create a file named aws-auth-cm.yaml with the following Kubernetes ConfigMap configruation, replace <ARN of instance role (not instance profile)> with the output from above:

    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: aws-auth
      namespace: kube-system
    data:
      mapRoles: |
        - rolearn: <ARN of instance role (not instance profile)>
          username: system:node:{{EC2PrivateDNSName}}
          groups:
            - system:bootstrappers
            - system:nodes

Add the Kubernetes ConfigMap configruation to the Kubernetes Cluster:

    $ kubectl apply -f aws-auth-cm.yaml
    
Verify the EKS Kubernetes Workers have joined the Kubernetes Cluster:

    $ kubectl get nodes --watch
