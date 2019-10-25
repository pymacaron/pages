---
title: Deploying PyMacaron on Google Kubernetes Engine
---

Deployment to Google Kubernetes Engine
======================================

PyMacaron microservices can be deployed to [GKE
clusters](https://cloud.google.com/kubernetes-engine/) using the
[pymacaron-gcp](https://github.com/pymacaron/pymacaron-gcp) addon.

## Prerequisites

You will need to have:

* Installed the [google SDK](https://cloud.google.com/sdk/docs/).

* Run 'gcloud init'

* Run 'gcloud auth configure-docker'

* Created two GKE clusters in your Google project: one having the name of your
  app suffixed with '--staging' (ex: 'helloworld-staging') and one with
  '--live' (ex: 'helloworld-live')

* Configure the live cluster using the tool of your choice. PyMacaron will
  configure the staging cluster for you.

* Installed 'pymacaron-gcp': ```pip install pymacaron-gcp```


### Creating the staging cluster

PyMacaron does not create the staging cluster. You should do it yourself and
name it '<YOUR_APP_NAME>-staging'. The recommended setup for the staging
cluster is a 1 zone cluster, with autoscaling from 1 to 2 instances (to avoid
resource exhaustion during new deploy), using a cheap low mem/low cpu instance
type.

PyMacaron does however configure the staging cluster, by creating a deployment,
adding a service and an ingress on the first deploy, then updating the
deployment on all further deploys.


### Creating and configuring the live cluster

PyMacaron does not create nor configure the live cluster. It only does a
rolling deploy towards it by applying a deployment named
'<YOUR_APP_NAME>-live'.

You should therefore create the cluster yourself using whatever tool you want,
but make sure that all resources you deploy share the app name
'<YOUR_APP_NAME>-live'.


## Deployment pipeline

When deploying to GKE, 'pymdeploy' goes through the following specific
deployment steps:

1. Deploy the docker image as a new deployment to a google GKE cluster named
  after your app's name suffixed with '-staging'. Optionally add a service and
  an ingress to expose the staging environment to internet.

1. Run the acceptance tests against the staging environment. Stop if tests fail.

1. Re-deploy the docker image as a new deployment to the live GKE cluster.


## Configuration in pym-config.yaml

To be able to deploy against GKE, the following key-values must be present in
your project's pym-config.yaml:

```yaml
docker_repo: <PROJECT_ID>      # The ID of your gcp project
deploy_target: gke
```

See [here](http://pymacaron.com/config.html) for details on 'pym-config.yaml'.
