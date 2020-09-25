# Ubuntu Linux 16.04 LTS AMI Builder

## Description
This repo builds a custom AMI of Ubuntu Linux 16.04 LTS with your own scripts and packages installed.

This repo is designed to be built using GitLab CI/CD, but can be easily customized to work with other CI/CD solutions (GitHub, Jenkins, etc.)

## Prerequisites
- AMI Packer Docker Image
- AWS CLI
- AWS Role or access key/secret key with EC2 Launch/AMI Creation permissions

## Customization
By default, a shell file (`configure.sh`) is provided to contain customized code. If you wish to change this filename or add more steps, you will need to edit the `base-ami.json` file with your new steps. See [Packer documentation](https://packer.io/docs/provisioners/index.html) for more information.

## Deployment Process
1. Upon commit, the pipeline will first validate whether the `base-ami.json` file is valid. If not, the process will end and an error will appear in the pipeline screen.
2. If the validation process is successful, the CI/CD pipeline will move to the deploy phase.
3. GitLab will pull down the Packer docker image specified in `.gitlab-ci.yml` and execute the defined builder tasks.
4. Upon successful build, the output AMI will be displayed and visible in your AWS account.

## Git Branches
This repo is designed for use with two branches: `sandbox` and `master`.

GitLab CI/CD must contain the following variables:
- PRD_AWS_ACCESS_KEY
- PRD_AWS_REGION
- PRD_AWS_SECRET_KEY
- PRD_BUILD_SUBNET_ID
- PRD_BUILD_VPC_ID
- SBX_AWS_ACCESS_KEY
- SBX_AWS_REGION
- SBX_AWS_SECRET_KEY
- SBX_BUILD_SUBNET_ID
- SBX_BUILD_VPC_ID

(Note: if using IAM roles, you do not need to specify an access key or secret key. However, you may need to revise the code to properly query the EC2 Metadata API.)

## IAM Role Permissions
Your attached policy should contain the following permissions for Packer to work
```json
{
  "Version": "2012-10-17",
  "Statement": [{
      "Effect": "Allow",
      "Action" : [
        "ec2:AttachVolume",
        "ec2:AuthorizeSecurityGroupIngress",
        "ec2:CopyImage",
        "ec2:CreateImage",
        "ec2:CreateKeypair",
        "ec2:CreateSecurityGroup",
        "ec2:CreateSnapshot",
        "ec2:CreateTags",
        "ec2:CreateVolume",
        "ec2:DeleteKeyPair",
        "ec2:DeleteSecurityGroup",
        "ec2:DeleteSnapshot",
        "ec2:DeleteVolume",
        "ec2:DeregisterImage",
        "ec2:DescribeImageAttribute",
        "ec2:DescribeImages",
        "ec2:DescribeInstances",
        "ec2:DescribeInstanceStatus",
        "ec2:DescribeRegions",
        "ec2:DescribeSecurityGroups",
        "ec2:DescribeSnapshots",
        "ec2:DescribeSubnets",
        "ec2:DescribeTags",
        "ec2:DescribeVolumes",
        "ec2:DetachVolume",
        "ec2:GetPasswordData",
        "ec2:ModifyImageAttribute",
        "ec2:ModifyInstanceAttribute",
        "ec2:ModifySnapshotAttribute",
        "ec2:RegisterImage",
        "ec2:RunInstances",
        "ec2:StopInstances",
        "ec2:TerminateInstances"
      ],
      "Resource" : "*"
  }]
}
```