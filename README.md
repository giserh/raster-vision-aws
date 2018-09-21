# Raster Vision AWS Batch runner setup

This repository contains the deployment code that sets up the necessary AWS resources to utilize the AWS Batch runner in [Raster Vision](https://rastervision.io). Deployment is driven by [Terraform](https://terraform.io/) and the [AWS Command Line Interface (CLI)](http://aws.amazon.com/cli/) through a local Docker Compose environment.

## Table of Contents

* [AWS Credentials](#aws-credentials)
* [AMI Creation](#ami-creation)
* [AWS Batch Resources](#aws-batch-resources)

## AWS Credentials

Using the AWS CLI, create an AWS profile for the target AWS environment. An example, naming the profile `raster-vision`:

```bash
$ aws --profile raster-vision configure
AWS Access Key ID [****************F2DQ]:
AWS Secret Access Key [****************TLJ/]:
Default region name [us-east-1]: us-east-1
Default output format [None]:
```

You will be prompted to enter your AWS credentials, along with a default region. These credentials will be used to authenticate calls to the AWS API when using Terraform and the AWS CLI.

## AMI Creation

This step uses packer to install nvidia-docker on the base ECS AMI
in order to run GPU jobs on AWS Batch.

### Configure the settings

Copy the `settings.mk.template` file to `settings.mk`, and fill out the following options:


| `AWS_BATCH_BASE_AMI`         | The AMI of the Deep Learning Base AMI (Amazon Linux) to use.    |
|------------------------------|-----------------------------------------------------------------|
| `AWS_ROOT_BLOCK_DEVICE_SIZE` | The size of the volume, in GiB, of the root device for the AMI. |


To find the latest Deep Learning Base AMI, search in th AMI section of your EC2 AWS console for
`Deep Learning Base AMI (Amazon Linux)`

### Create the AMI

Ensure that the AWS profile for the account you want to create the AMI in is set in your `AWS_PROFILE`
environment variable setting.

Then run:
```shell
> make create-ami
```

This will run packer, which will spin up an EC2 instance, install the necessary resources, create an AMI
off of the instance, and shut the instance down.

### Record the AMI ID

Be sure to record the AMI ID, which will be given in the last line of the output for `make create-ami`
on a successful run.

## AWS Batch

```shell
> make plan
> make apply
```
