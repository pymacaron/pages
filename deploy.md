---
title: PyMacaron's Deployment Pipeline
---

Deployment Pipeline
===================

PyMacaron microservices are designed to support multiple deployment
targets. Currently, both AWS Beanstalk and Google Cloud Run are supported.

### Deployment pipeline with pymdeploy

[bin/pymdeploy](https://github.com/pymacaron/pymacaron/blob/master/bin/pymdeploy)
implements the deployment pipeline of PyMacaron microservices, which consists
of the following steps:

1. Execute unittests under 'test/' with nosetests. Stop if tests fail.

1. Generate a [docker image](http://pymacaron.com/docker.html) in which the app
   starts inside gunicorn.

1. Start this image in a local docker instance and run acceptance tests from
   the 'testaccept/' directory against it. Stop if tests fail.

1. Push that image to the container registry specified in pym-config.yaml.

1. Deploy the image to a live environment, [AWS
   Beanstalk](http://pymacaron.com/aws.html) or [Google Cloud
   Run](http://pymacaron.com/gcp.html). This step is itself divided into
   substeps specific of the targeted cloud platform. If all goes well, the
   docker image ends up running live and publicly accessible on the net.

1. Run acceptance tests again, this time against the live environment, as a
   final validation.

### Usage

To execute the full deployment pipeline, do:

```
cd your-project-root
pymdeploy
```

For more usage examples, see 'pymdeploy --help'.
