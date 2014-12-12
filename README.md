#Provisioneering

A minimal set of scripts to generate an AWS AMI and provision an EC2 instance from the resulting image.

## Setup

Create a user on AWS with the following IAM policy (or similar):

``` json
{
  "Statement": [{
      "Effect": "Allow",
      "Action" : [
        "ec2:AttachVolume",
        "ec2:CreateVolume",
        "ec2:DeleteVolume",
        "ec2:CreateKeypair",
        "ec2:DeleteKeypair",
        "ec2:CreateSecurityGroup",
        "ec2:DeleteSecurityGroup",
        "ec2:AuthorizeSecurityGroupIngress",
        "ec2:CreateImage",
        "ec2:RunInstances",
        "ec2:TerminateInstances",
        "ec2:StopInstances",
        "ec2:DescribeVolumes",
        "ec2:DetachVolume",
        "ec2:DescribeInstances",
        "ec2:CreateSnapshot",
        "ec2:DeleteSnapshot",
        "ec2:DescribeSnapshots",
        "ec2:DescribeImages",
        "ec2:RegisterImage",
        "ec2:CreateTags",
        "ec2:ModifyImageAttribute"
      ],
      "Resource" : "*"
  }]
}
```

Define the following confuration variables:

``` console
# The AWS access key with IAM to be able to provision
AWS_ACCESS_KEY_ID='YOUR-ACCESS_KEY'
AWS_SECRET_ACCESS_KEY='YOUR_SECRET_ACCESS_KEY'

# Define the security group for provisioning
AWS_SECURITY_GROUP_ID='sg-abc123'

# Security group for launched instance
AWS_SECURITY_GROUP_IDS='sg-2345'

# Launch instance SSH key
AWS_KEY_NAME='ssh-key-name-identifier'

# Define instance size
AWS_INSTANCE_SIZE='t1.micro'

# VPC ID for launched instance
AWS_VPC_ID='vpc-34567'

# Launched instance subnet id
AWS_SUBNET_ID='subnet-13241234'

# Launched instance VPC zones
AWS_VPC_ZONES='us-east-1a'

# Tag for instance identification
ENVIRONMENT='staging'

# Base ami image (Ubuntu 14.04 in this case)
AMI_ID='ami-f21b7d9a'
```

Generate an AMI:

``` console
BUILT_AMI=$(bin/generate_ami)
```

Launch the resulting AMI as an EC2 instance
``` console
bin/launch_ami_image
```

Enjoy your newly provisioned server with Docker and Fig pre-installed.
