#Provisioneering

A minimal set of scripts to generate an AWS AMI and provision an EC2 instance from the resulting image.

## Setup

### Prerequisites:

- [Node](http://nodejs.org/download/) for parsing json
- [Pip](https://pip.pypa.io/en/latest/installing.html) for installing awscli. Alternate installation instructions [here](http://docs.aws.amazon.com/cli/latest/userguide/installing.html)


Configure AWS CLI
``` console
  aws configure
```

Follow the prompts. They will lead the way.


---

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

Define the following configuration variables:

``` console
# The AWS access key with IAM to be able to provision
AWS_ACCESS_KEY_ID='YOUR-ACCESS_KEY'
AWS_SECRET_ACCESS_KEY='YOUR_SECRET_ACCESS_KEY'

# Define the security group for provisioning
AWS_SECURITY_GROUP_ID='sg-e4969181'

# Launch instance SSH key
AWS_KEY_NAME='ssh-key-name-identifier'

# Define instance size
AWS_INSTANCE_SIZE='t2.small'

# VPC ID for launched instance
AWS_VPC_ID='vpc-36a92853'

# Launched instance subnet id
AWS_SUBNET_ID='subnet-6216b115'

# Launched instance VPC zones
AWS_VPC_ZONES='us-east-1a'

# Tag for instance identification
RAILS_ENVIRONMENT='staging'

# Base ami image (Ubuntu 14.04 in this case)
AWS_SOURCE_AMI='ami-9eaa1cf6'

# The tag name for your new EC2 instance
SERVER_NAME="ihat-${RAILS_ENVIRONMENT}"

# Your Github deploy token
RAILS_DEPLOY_GIT_ACCESS_TOKEN='21342341234'

```

Generate an AMI:

``` console
BUILT_AMI=$(bin/generate_ami) || cat build.log
```

Launch the resulting AMI as an EC2 instance
``` console
bin/launch_ami_image
```

Enjoy your newly provisioned server with Docker and Fig pre-installed.

# Provisioning with Ansible

We are using the [debops](http://debops.org/) framework for provisioning servers.

See the debops [documentation](http://docs.debops.org/en/latest/installation.html) for installation instructions.

The tl;dr version:

```
sudo pip install debops
debops-update
```

Store the password to the vault:

```
echo "[password redacted]" > ~/.vault_pass.txt
```

To run the debops playbooks with our custom playbooks, run:

```
bin/provision
```

Run only the playbooks tagged with rails\_deploy:
```
bin/provision -t rails_deploy 
```

Or start at cloning the project's source code (for CI or if re-deploying code)

```
 bin/provision -t rails_deploy --start-at-task="Clone the app's source code"
 ```

To encrypted secrets for a server (ihat-staging in this case):

```
ansible-vault edit ansible/inventory/host_vars/ihat-staging.yml --vault-password-file ~/.vault_pass.txt
```
