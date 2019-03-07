---
title: PyMacaron Packaging & Deployment
---

Packaging with Docker
=====================

PyMacaron microservices are designed to be packaged as Docker images.
[pymacaron-docker](https://github.com/pymacaron/pymacaron-docker) contains the tools
required to build a Docker image containing and starting your microservice.

### Prerequisites

You need to be able to run a docker container locally on your development host,
as part of the deployment pipeline for PyMacaron microservices. Simply install
docker engine as follows:

```shell
apt-get install docker docker-engine
```

And install pymacaron-docker:

```shell
pip install pymacaron-docker
```

### Configuration

Make sure that the following key pairs are set in your project's 'pym-config.yaml':

```yaml
name: <PROJECT NAME>
docker_repo: <MYREPO>
```

Your microservice's Docker image will be named '<PROJECT NAME>-<VERSION>' where '<VERSION>'
is a unique number generated based on the date and the git revision number of your project.

### Login to your docker repository

Using the same unix user with which you will later run the deployment pipeline, login to
the Docker repository to which your microservice's image will be uploaded ('docker_repo' in
'pym-config.yaml'):

```shell
docker login
```

This creates the file '~/.docker/config.json file' containing your docker auth token.



Deployment
==========

PyMacaron microservices are designed to support multiple deployment
targets. For the moment however, only AWS Beanstalk is supported.

### Deployment pipeline with pymdeploy

[bin/pymdeploy](https://github.com/pymacaron/pymacaron/blob/master/bin/pymdeploy)
implements the deployment pipeline of PyMacaron microservices, which consists
of the following steps:

1. Execute unittests under 'test/' with nosetests. Stop if tests fail.

1. Generate a docker image in which the app starts inside gunicorn.

1. Start this image in a local docker instance and run acceptance tests from
   the 'testaccept/' directory against it. Stop if tests fail.

1. Push that image to hub.docker.com.

1. Deploy the image to a live environment, AWS Beanstalk or other,
   as specified in pym-config.yaml.

1. Run acceptance tests again, this time against the live environment, as a
   final validation.

### Usage

To execute the full deployment pipeline, do:

```
cd your-project-root
pymdeploy
```

For more usage examples, see 'pymdeploy --help'.