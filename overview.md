---
title: PyMacaron Overview
---

Overview
========

[pymacaron](https://github.com/pymacaron/pymacaron) is a python microservice
framework based on Flask, OpenAPI, docker, AWS or GCP, Celery and Bravado-Core.

PyMacaron aims at abstracting away all the scaffholding of setting up a Flask
microservice, its routes, its deployment pipeline, etc. The only code you
should have to write are the the python methods bound to every API endpoint.

## Core features

### OpenAPI specifications

At the core of pymacaron lies your API definitions, in the form of yaml files
following the [OpenAPI/swagger standard](https://swagger.io/specification/).

Those API definitions describe every endpoint in your REST/JSON APIs: which
data they accept as parameters and what data they return. An OpenAPI
specification also describes every data model used in your API.

### Self-defined routes

A PyMacaron server is basically a Flask server whose routes are automatically
added at start time, based on the endpoints defined in your OpenAPI/swagger
files.

[pymacaron-core](https://github.com/pymacaron/pymacaron-core) extends the
standard OpenAPI format to let you bind API endpoints to python methods and add
JWT-based authentication to chosen endpoints.

[More details here](http://pymacaron.com/api.html).

### Endpoints that take and return objects

PyMacaron transparently converts the json body of a request into a PyMacaron
Model instance, basically a python object with getters, setters and convenient
builtin methods.

This object is passed to the python method bound to the API endpoint, together
with other query parameters, and this method later returns an other PyMacaron
Model instance, that will be serialized to json and returned to the caller.

Those objects are defined in your OpenAPI specification, in the form of
['schema
objects'](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/3.0.0.md#schemaObject).

PyMacaron also extends the OpenAPI standard to let you assign a parent class to
schema objects, or to make them serializable to/from a database.

[More details here](http://pymacaron.com/models.html).

### JWT authentication

You can add authentication around API endpoints in a PyMacaron microservice by
using PyMacaron's built-in support for JWT tokens. [More details
here](http://pymacaron.com/jwt.html).

## Pluggable features

### Core features

By itself, [pymacaron](https://github.com/pymacaron/pymacaron) only lets you
build a REST microservice and run it as a local server.  Pymacaron uses
[pymacaron-core](https://github.com/pymacaron/pymacaron-core) to take an
OpenAPI yaml specification and start a Flask app implementing
it. Pymacaron-core is in fact a thin layer on top of
[bravado-core](https://github.com/Yelp/bravado-core).

Other pymacaron features are available as plugins:

### Deployment pipeline

PyMacaron microservices come with a [deployment
pipeline](http://pymacaron.com/deploy.html) that packages the service as a
[docker image](http://pymacaron.com/docker.html) and deploys it to [Amazon
Beanstalk](http://pymacaron.com/aws.html), [GKE](http://pymacaron.com/gke.html)
or [Google Cloud Run](http://pymacaron.com/gcr.html).

### Asynchronous task execution

[pymacaron-async](https://github.com/pymacaron/pymacaron-async) adds support
for asynchronous task execution to your API endpoints, based on Celery and
Redis.

[More details
here](http://pymacaron.com/async.html).

### Blackbox testing framework

[pymacaron-unit](https://github.com/pymacaron/pymacaron-unit) is a unittesting
library to write blackbox tests targeting your API endpoints.

[More details
here](http://pymacaron.com/testing.html).

### Object persistence

Object persistence is achieved by letting PyMacaron models inherit from a class
that handles persistence and serialisation.
[pymacaron-dynamodb](https://github.com/pymacaron/pymacaron-dynamodb) does that
between Amazon's DynamoDB and PyMacaron models.
