---
title: PyMacaron Overview
---

PyMacaron
=========

Python microservice framework based on Flask, OpenAPI, docker and AWS/beanstalk.

## Overview

### File structure of a PyMacaron microservice

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
└── test              # Unitests, executed with nosetests
|   └── test_pep8.py
|
|   # Acceptance tests for your api, executed with run_acceptance_tests
└── testaccept
    ├── test_v1_user_login.py
    └── test_version.py

```


## Features

[pymacaron](https://github.com/pymacaron/pymacaron) in
itself only lets you build a REST microserviceand run a microservice an API server and run it locally. You may use
additional features by installing the following extra modules:

### Asynchronous task execution

Install [pymacaron-async](https://github.com/pymacaron/pymacaron-async) by
following [these instructions](https://github.com/pymacaron/pymacaron-async#setup).

### Deploying as a container in Amazon Beanstalk

Install [pymacaron-aws](https://github.com/pymacaron/pymacaron-aws) by
following [these instructions](https://github.com/pymacaron/pymacaron-aws#setup).

### Use PyMacaron's own testing framework

A [convenient library](https://github.com/pymacaron/pymacaron-unit) for black-box testing your API endpoints:


## Deep dive


### Swagger specifications

All api endpoints that your service needs, both those it implements and those
it calls as a client, are to be defined as swagger specifications in the format
supported by [pymacaron-core](https://github.com/pymacaron/pymacaron-core).
pymacaron-core uses [Bravado](https://github.com/Yelp/bravado) to handle
marshalling/unmarshalling and validation of your api objects to and from json,
and does all the magic of spawning client and server stubs for all api
endpoints, catching errors, and providing optional database serialization for
your api objects.


### Deployment pipeline

PyMacaron microservices come with a ready-to-use deployment pipeline that packages
the service as a docker image and deploys it on Amazon Elastic Beanstalk with
little configuration required.

For details, see
[pymacaron-aws](https://github.com/pymacaron/pymacaron-aws).


### Elastic Beanstalk configuration

The PyMacaron microservice toolchain is built to deploy services as Docker
images running inside Amazon EC2 instances in Elastic Beanstalk, behind an
Elastic Load Balancer. All the details of setting up those Amazon services is
handled by [pymacaron-aws](https://github.com/pymacaron/pymacaron-aws) and
should be left untouched. A few parameters can be adjusted, though. They are
described in the 'pym-config.yaml' section below.



### Built-in endpoints

The following endpoints are built-in into every pymacaron instance, based
on [this swagger spec](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/ping.yaml):

```
# Assuming you did in a separate terminal:
# $ python server.py --port 8080

$ curl http://127.0.0.1:8080/ping
{}

$ curl http://127.0.0.1:8080/version
{
  "container": "",
  "name": "ping",
  "version": "0.0.1"
}

$ curl http://127.0.0.1:8080/auth/version
{
  "error_description": "There is no Authorization header in the HTTP request",
  "error_id": "17f900c8-b456-4a64-8b2b-83c7d36353f6",
  "status": 401,
  "error": "AUTHORIZATION_HEADER_MISSING"
}

$ curl -H "Authorization: Bearer eyJpc3M[...]y8kNg" http://127.0.0.1:8080/auth/version
{
  "container": "",
  "name": "ping",
  "version": "0.0.1"
}

```


## Recipes


### Loading api clients from a standalone script

It may come very handy within a standalone script to be able to call REST apis
through the pymacaron framework, to get object marshalling and error
handling out of the box. It is done as follows:

```python
import flask
from pymacaron_core.swagger.apipool import ApiPool
from pymacaron.exceptions import is_error
from pymacaron import load_clients

# Declare a Flask app and mock its context
app = flask.Flask(__name__)
with app.test_request_context(''):

    # Then load client libraries against a given set of libraries
    api = API(app)
    api.load_clients(apis=['login', 'search'])

    # And you can now call those apis seamlessly!
    result = ApiPool.login.client.do_login(
        ApiPool.login.model.LoginData(
            name='foobar',
            password='youdontwanttoknow'
        )
    )

    if is_error(result):
        log.error("Oops. Failed to login used")
```
