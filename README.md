# USAP Guest Lecture

### Preface

* What is AWS
  * What is EC2
  * What is VPC
  * What is CloudFormation
    * What is a stack
    * What is our stack creating
* What is an SSH key

* Note: Anywhere you see `sXXXXXXX` put your student number.
* Note: You should do all the following on the RMIT core teaching machines

### Creating an IAM User

0. I will demonstrate this in the lecture as it involves the AWS Web Console
1. These credentials should be treated like a password, that's basically what they are
  0. You can put them in your `~/.bashrc` but make sure the file is only readable by you `chmod 600 ~/.bashrc`
  1. You can also export them manually for each shell, the choice is yours

  Regardless, they are to be exported as follows

  ```
  export AWS_ACCESS_KEY_ID=CHANGEME
  export AWS_SECRET_ACCESS_KEY=CHANGEME
  ```

### Setting up your environment

0. Ensure you have the following in your `~/.bashrc` (you do this to avoid having to specify `--region` to all `aws` commands)

```
AWS_DEFAULT_REGION=ap-southeast-2
AWS_REGION=ap-southeast-2
```

1. Reload your shell `exec bash`

### Generating and uploading an SSH Key

0. `ssh-keygen -t rsa -b 4096 -f ~/.ssh/sXXXXXXX`
  0. It will ask you for an encryption passphrase, put one you will remember
1. `aws ec2 import-key-pair --key-name sXXXXXXX --public-key-material file://~/.ssh/sXXXXXXX.pub`
  0. This uploads the public key to AWS so your instance can use it

### Showing the existing VPC setup

0. `aws ec2 describe-subnets`

You will see something like this (clearly the IDs will be different)

Take note of the `VpcId` and one `SubnetId`

```
{
    "Subnets": [
        {
            "VpcId": "vpc-c970a0ac", 
            "CidrBlock": "172.31.0.0/20", 
            "MapPublicIpOnLaunch": true, 
            "DefaultForAz": true, 
            "State": "available", 
            "AvailabilityZone": "ap-southeast-2a", 
            "SubnetId": "subnet-a37681fa", 
            "AvailableIpAddressCount": 4091
        }, 
        {
            "VpcId": "vpc-c970a0ac", 
            "CidrBlock": "172.31.16.0/20", 
            "MapPublicIpOnLaunch": true, 
            "DefaultForAz": true, 
            "State": "available", 
            "AvailabilityZone": "ap-southeast-2b", 
            "SubnetId": "subnet-a5dc6ac0", 
            "AvailableIpAddressCount": 4091
        }
    ]
}
```

### Starting an EC2 Instance using AWS CloudFormation

**THIS WILL COST YOU MONEY**

Deploy the stack using CloudFormation (substitute the parameters you obtained earlier)

```
aws cloudformation create-stack --stack-name sXXXXXXX --template-body file://ec2.json --parameters \
ParameterKey=VpcId,ParameterValue=VPCID \
ParameterKey=SubnetId,ParameterValue=SUBNETID \
ParameterKey=KeyName,ParameterValue=sXXXXXXX
```

### Connecting to the Instance

Get the public hostname from the Stack Outputs

`aws cloudformation describe-stacks --stack-name sXXXXXXX --query 'Stacks[*].Outputs'`

```
[
    [
        {
            "Description": "The private DNS name assigned to the instance", 
            "OutputKey": "PrivateDnsName", 
            "OutputValue": "ip-10-50-48-234.ap-southeast-2.compute.internal"
        }, 
        {
            "Description": "The Id of the created instance", 
            "OutputKey": "InstanceId", 
            "OutputValue": "i-19793db6"
        }, 
        {
            "Description": "The private IP address assigned to the instance", 
            "OutputKey": "PrivateIp", 
            "OutputValue": "10.50.48.234"
        }, 
        {
            "Description": "The availability zone the instance was launched into", 
            "OutputKey": "AvailabilityZone", 
            "OutputValue": "ap-southeast-2a"
        }, 
        {
            "Description": "The public DNS name assigned to the instance", 
            "OutputKey": "PublicDnsName", 
            "OutputValue": "ec2-52-62-157-205.ap-southeast-2.compute.amazonaws.com"
        }
    ]
]
```

SSH to the instance `ssh -i ~/.ssh/sXXXXXXX ec2-user@public-dns-name`

### You can delete everything with

`aws cloudformation delete-stack --stack-name sXXXXXXX`
