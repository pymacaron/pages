---
title: PyMacaron's Deployment Pipeline
---

Deployment Pipeline
===================

PyMacaron microservices are designed to support multiple deployment
targets. Currently, both AWS Beanstalk and Google Cloud Run are supported.

## Deployment pipeline with pymdeploy

[bin/pymdeploy](https://github.com/pymacaron/pymacaron/blob/master/bin/pymdeploy)
implements the deployment pipeline of PyMacaron microservices, which consists
of the following steps:

1. Execute unittests under 'test/' with nosetests. Stop if tests fail.

1. Generate a [docker image](http://pymacaron.com/docker.html) in which the app
   starts inside gunicorn.

1. Start this image in a local docker instance and run acceptance tests from
   the 'testaccept/' directory against it. Stop if tests fail.

1. Push that image to a container registry, either docker.io or gcr.io.

1. Deploy the image to a cloud environment: either by creating a [new Beanstalk
   environment on AWS](http://pymacaron.com/aws.html), or by doint a rolling
   deploy to a [staging environment in GKE](http://pymacaron.com/gke.html) or
   [Google Cloud Run](http://pymacaron.com/gcr.html).

1. Run acceptance tests against the cloud environment. Stop if they fail.

1. Deploy the image to a live cloud environment: either by doing a blue/green
   deployment of the newly created Beanstalk environment on AWS, or by re-deploying
   the image to a live environment on GKE or Google Cloud Run.

1. Run acceptance tests again, this time against the live environment, as a
   final validation.

### Usage

To execute the full deployment pipeline, do:

```
cd your-project-root
pymdeploy
```

For more usage examples, see 'pymdeploy --help'.

## Targeted clouds

### AWS Beanstalk

See [instructions here](http://pymacaron.com/aws.html).

### Google Kubernetes Engine (GKE)

See [instructions here](http://pymacaron.com/gke.html).

### Google Cloud Run

See [instructions here](http://pymacaron.com/gcr.html).

## Identifying runtime inside the app

How can a pymacaron service know if it is being executed inside a container started by 'pymtest', or in a staging respectively live deployment on google cloud or aws? By looking at the 'PYM_ENV' environment variable, documented [here](http://pymacaron.com/config.html).

