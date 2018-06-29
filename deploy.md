---
title: PyMacaron Deployment
---

Deployment Pipeline
===================

[pymacaron-aws](https://github.com/pymacaron/pymacaron-aws) is a deployment
pipeline for PyMacaron microservices, deploying them as Docker containers on
amazon Elastic Beanstalk.

## Deployment pipeline

The directory
[/bin](https://github.com/pymacaron/pymacaron-aws/tree/master/bin) contains
the following tools for deploying pymacaron microservices:

### deploy_pipeline

[bin/deploy_pipeline](https://github.com/pymacaron/pymacaron-aws/blob/master/bin/deploy_pipeline)
implements the deployment pipeline of pymacaron microservices, which consists
of the following steps:

1. Execute unittests under 'test/' with nosetests. Stop if tests fail.

1. Generate a docker image in which the app starts inside gunicorn.

1. Start this image in a local docker instance and run acceptance tests from
   the 'testaccept/' directory against it. Stop if tests fail.

1. Push that image to hub.docker.com.

1. Start an Elastic Beanstalk environment running single-container docker
   instances, and load the app image in it.

1. Run the acceptance tests again, this time against the Beanstalk
   environment. Stop if tests fail.

1. Swap the new Beanstalk environment with the current live one ([blue/green
   deployment](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/using-features.CNAMESwap.html)).
   Your app is now live!

1. Run acceptance tests again, this time against the live environment, as a
   final validation.

Voila! After a few minutes your microservice will be live, running as a docker
container in Amazon Elastic Beanstalk, with all the goodies of auto-scalling
and self-repair it offers :-)

### Usage

To execute the full deployment pipeline, do:

```
cd your-project-root
deploy_pipeline --push --deploy
```

To execute only steps 1 to 4:

```
deploy_pipeline --push
```

To only push the image to hub.docker.com:

```
deploy_pipeline --no-build --push
```

To only deploy to Amazon:

```
deploy_pipeline --no-build --deploy
```

Easy!

## Prerequisites

You will need:

* An Amazon AWS account with Beanstalk enabled

* Access to a docker registry on [hub.docker.com](https://hub.docker.com/)

* A pymacaron microservice ready to be deployed

## Setup

Here are the steps you must follow before being able to deploy a pymacaron
microservice to AWS.

### Install Docker

You need to be able to run a docker container locally on your development host,
as part of the deployment pipeline for pymacaron microservices. Simply install
docker engine as follows:

```shell
apt-get install docker docker-engine
```

### Create a S3 bucket for docker config

Beanstalk's way of receiving the docker configuration for an image relies on a
S3 bucket to pass the configuration.

In the amazon aws console, create a S3 bucket with a name of your choice
'DOCKER_CFG_BUCKET'. In this bucket, create an empty directory called
'docker'.

### Docker registry credentials

Here we assume that you are using 'hub.docker.com' as your image repository.

From a terminal, login into docker hub, on the same host where you will run the
deployment pipeline (to create the '~/.docker/config.json file'):

```shell
docker login
```

Find the <auth-token> and <email> in '~/.docker/config.json', and upload to
'S3/klue-config/docker/dockercfg' the following file:

```shell
{
  "https://index.docker.io/v1/": {
    "auth": "<auth-token>",
    "email": "<email>"
  }
}
```

With that, Amazon will be able to fetch your microservice image from the docker
registry.

### Configure AWS credentials

In the Amazon AWS console, setup an IAM user with the name of your choice,
<IAM_USER_NAME>, with the following rights:

* AmazonEC2ReadOnlyAccess
* AWSElasticBeanstalkFullAccess
* And a custom policy giving this user access to the 'klue-config' S3 bucket:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::<DOCKER_CFG_BUCKET>/docker/dockercfg"
            ],
            "Effect": "Allow"
        }
    ]
}
```

Still in the IAM console, attach the custom policy to
'aws-elasticbeanstalk-ec2-role' and 'aws-elasticbeanstalk-service-role'.

Then, under the user's 'Security Crendetials' in the IAM console, create an
access key for the user <IAM_USER_NAME> and note its 'Access key ID' and
'Secret access key'.

In a terminal on the host from which you will deploy the microservice,
configure the aws profile of the <IAM_USER_NAME>:

```shell
aws configure --profile <IAM_USER_NAME>
# Enter the 'Access key ID' and 'Secret access key' for klue-publish
# Choose the default region that suits you (ex: eu-west-1)
```

### Create an Elastic Beanstalk application for your micro-service

In the Amazon aws console, create an EBS application:
* Log into the [aws console](https://eu-west-1.console.aws.amazon.com/elasticbeanstalk)
* Go to Elastic Beanstalk console
* Click 'Create new application'
* Click 'Create web server' environment
* Environment Type: Select platform 'Generic/Docker', environment type is 'load balancing, auto scaling'
* Environment Info: set url to '<YOUR_NEW_SERVICE_NAME>'
* Configuration Details: select EC2 key pair, health check url '/ping'
* Keep all other settings to default
* Create environment!

From the root directory of your microservice:

```shell
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
eb init --region eu-west-1 --profile <IAM_USER_NAME>

eb list
eb use <YOUR_NEW_SERVICE_NAME>
```

Calling 'eb use' marks this Beanstalk application as the current live instance
of the micro-service, that will be swapped with the new instance upon every
deploy.

## Configure your microservice

Your microservice project should contain in its root a file called
'pym-config.yaml' containing at least the following key-value pairs for the
deploy pipeline to work:

```yaml
name: <YOUR_NEW_SERVICE_NAME>
docker_repo: <NAME_OF_YOUR_ROOT_REPO_ON_DOCKER.IO>
docker_s3_bucket: <DOCKER_CFG_BUCKET>
iam_user: <IAM_USER_NAME>
ssh_keypair: <NAME_OF_SSH_KEYPAIR_TO_USE_IN_EC2>
```

See [here](http://pymacaron.com/config.html) for details on 'pym-config.yaml'.

## Manual removal of out-dated Beanstalk environment

WARNING! The deploy pipeline does not remove the old application environment
after swapping it away in favor of a new one. The old environment is kept so
that you may swap it back into live position should your new environment be
failing despite all acceptance tests.

Each environment consumes resources that you'll have to pay for, so you should
regularly delete manually the old environments from your Beanstalk application.