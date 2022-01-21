## Pymacaron

Pymacaron is a python microservice framework based on OpenAPI, pydantic and Flask, using docker for
packaging and deployable onto AWS Beanstalk, Google Kubernetes and Google Cloud Run.

### Pymacaron vs FastAPI

Pymacaron and [FastAPI](https://fastapi.tiangolo.com/) are quite similar. They are both based on [pydantic](https://pydantic-docs.helpmanual.io/) and they both ensure a strict mapping between OpenAPI specification and actual server code.

But pymacaron takes an OpenAPI-first approach: pymacaron generates pydantic classes and Flask code based on OpenAPI specifications. FastAPI generates OpenAPI specifications based on pydantic classes.

And pymacaron uses Flask, FastAPI does not.

### Get started

To create a pymacaron service:

* Fork [pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld)
* Add your API definition to the swagger file under [apis/](https://github.com/pymacaron/pymacaron-helloworld/tree/master/apis)
* Implement the API endpoints as python methods
* Run 'python server.py'

To deploy it as a docker container to AWS or GCP, set a few configuration
parameters and run 'pymdeploy'.

BOOM! Your are live in the cloud!

Pymacaron abstracts away all the scaffholding of structuring your Python app,
defining routes, serializing/deserializing between json, Python objects and
databases, containerizing your app and deploying it on AWS or GCP. It even
supports asynchronous execution out of the box!

Pymacaron lets you focus on what matters: defining and implementing your API.

### Features

[pymacaron](https://github.com/pymacaron/pymacaron) provides:

* Auto-generation of Flask routes
* Auto-declaration of pydantic models for all swagger objects
* Auto-conversion of python exceptions into your API's own error model
* Reporting of python exceptions via channels of your choice
* Builtin validation of API parameters and replies
* A unittest framework for API level testing
* Docker packaging based on gunicorn
* Builtin support for asynchronous task execution
* Builtin support for server profiling (using [Scoutapm](https://scoutapm.com/apps/161818))
* A deployment pipeline targetting AWS Beanstalk, Google GKE or Google Cloud Run

### Requirements

* Python3.8

* For deployment, access to a docker repository and an Amazon AWS or Google GCP account.

### Watch the video!

[https://youtu.be/bf_dO4Gge8A](https://youtu.be/bf_dO4Gge8A)

### Credits

Pymacaron (2016-2022) was created and is maintained by [Erwan Lemonnier](https://github.com/erwan-lemonnier).
