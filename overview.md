---
title: PyMacaron Overview
---

Overview
========

[pymacaron](https://github.com/pymacaron/pymacaron) is a python microservice
framework based on Flask, OpenAPI, docker and AWS/beanstalk.

## Core features

### OpenAPI specifications

At the core of pymacaron lies your API specifications, in the form of yaml
files following the OpenAPI/swagger standard.

Those API specifications describe every endpoint in your REST/JSON APIs, the
data they accept as parameters and return as results, and all the
objects used in your API.

### Self-defined routes

A PyMacaron server is basically a Flask server in which the routes defined
in your OpenAPI specification are automatically added upon start.

[pymacaron-core](https://github.com/pymacaron/pymacaron-core) extends the
standard OpenAPI format to let you bind API endpoints to python methods and
add JWT-based authentication to chosen endpoints

[More details here](http://pymacaron.com/api.html).

### Endpoints that take and return objects

PyMacaron transparently converts OpenAPI schema objects into objects
that are passed to the methods implementing endpoints and returned by them.
Custom extensions of the OpenAPI specifications let you
make thess objects inherit from your own classes, or become serializable to and from databases.

[More details here](http://pymacaron.com/models.html).

### JWT authentication

You can add authentication around API endpoints in a PyMacaron microservice by
using PyMacaron's built-in support for JWT tokens. [More details
here](http://pymacaron.com/jwt.html).

## Pluggable features

By itself, [pymacaron](https://github.com/pymacaron/pymacaron) only lets you
build a REST microservice and run it as a local server.

The following plugins may be used for additional features:

### Deployment pipeline

PyMacaron microservices come with a [ready-to-use deployment
pipeline](http://pymacaron.com/deploy.html) that packages the service as a
docker image and deploys it on Amazon Elastic Beanstalk with little
configuration required.

This deployment pipeline is implemented in
[pymacaron-aws](https://github.com/pymacaron/pymacaron-aws). [More details
here](http://pymacaron.com/deploy.html).

### Asynchronous task execution

[pymacaron-async](https://github.com/pymacaron/pymacaron-async) adds support
for asynchronous task execution to your API endpoints. [More details
here](http://pymacaron.com/async.html).

### Blackbox testing framework

[pymacaron-unit](https://github.com/pymacaron/pymacaron-unit) is a unittesting
library to write blackbox tests targeting your API endpoints. [More details
here](http://pymacaron.com/testing.html).

## File structure of a PyMacaron microservice

A REST api microservice built with pymacaron has a directory tree
looking like this:

```
.
|   # apis/ contains the swagger specifications of your
|   # microservice apis
├── apis
│   └── myservice.yaml
│   └── sendgrid.yaml
│   └── auth0.yaml
|
|   # Implementation of your api's endpoints
├── myservice
│   └── api.py
│
├── LICENSE           # Always have a licence :-)
├── README.rst        # and a readme!
|
├── pym-config.yaml   # PyMacaron settings
|
├── server.py         # Your API server
|
├── requirements.txt  # Your python dependencies
|
└── test              # Unitests, executed with nosetests
|   └── test_pep8.py
|
|   # Acceptance tests for your api, executed with pymtest
└── testaccept
    ├── test_v1_user_login.py
    └── test_version.py

```
