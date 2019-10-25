---
title: Deploying PyMacaron on Google Cloud Run
---

Deployment to Google Cloud Run
==============================

Google [Cloud Run](https://cloud.google.com/run/) is a fully managed serverless
platform for running your containers. You can easily deploy PyMacaron
microservices to Cloud Run using the
[pymacaron-gcp](https://github.com/pymacaron/pymacaron-gcp).


## LIMITATIONS!

### Async execution not supported!

Note that Google Cloud Run only supports microservices that have no activity
outside request handling. See ['Avoiding background
activities'](https://cloud.google.com/run/docs/tips).

Therefore PyMacaron's async task implementation based on Celery/Redis will not
work with Cloud Run.

### HTTPS not default!

Cloud Run services have both there HTTP and HTTPS ports exposed. It's the
developer's responsibility to actively redirect all HTTP requests to HTTPS.

## Deployment pipeline

When deploying to Cloud Run, 'pymdeploy' goes through the following specific
deployment steps:

1. Deploy the docker image as a new revision to a google Cloud Run project
   whose name is the service's name suffixed with '-staging'.

1. Run the acceptance tests against that staging environment. Stop if tests fail.

1. Re-deploy the docker image as a new revision to a google Cloud Run project
   whose name is the service's name suffixed with '-live'.

'pymgcp' sets up the Google Run service based on the parameters defined in
'pym-config.yaml'. It is however up to you to map the live environment to a
custom domain of your choice.


## Prerequisites

You will need to have:

* Installed the [google SDK](https://cloud.google.com/sdk/docs/).

* Run 'gcloud init'

* Run 'gcloud auth configure-docker'

* Installed 'pymacaron-gcp': ```pip install pymacaron-gcp```


## Configuration in pym-config.yaml

To be able to deploy against Cloud Run, the following key-values must be
present in your project's pym-config.yaml:

```yaml
docker_repo: <PROJECT_ID>      # The ID of your gcp project
deploy_target: gcp-cloud-run
gcp_region: europe-west1       # Set to the gcp region your service should be deployed to
gcp_memory: 1G                 # Max memory granted to one container
gcp_request_concurrency: 8     # Number of concurrent requests a container may handled before
                               # triggering autoscaling
```

See [here](http://pymacaron.com/config.html) for details on 'pym-config.yaml'.
