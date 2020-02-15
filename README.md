## PyMacaron: a Python microservive framework

A python microservice framework based on Flask and Swagger, using docker for
packaging and deployable on AWS/beanstalk, GKE or Google Cloud RUN.

### Microservices made easy

Create a Flask-based microservice running your REST/json api by doing:

* Fork PyMacaron Helloworld
* Add your API definition to the swagger file
* Implement the API endpoints as python methods
* run 'python server.py'

To deploy it as a docker container to AWS or GCP, set a few configuration
parameters and run 'pymdeploy'.

BOOM! Your are live in the cloud!

PyMacaron abstracts away all the scaffholding of structuring your Python app,
defining routes, serializing/deserializing between json, Python objects and
databases, containerizing your app and deploying it on AWS or GCP. It even
supports asynchronous execution out of the box!

PyMacaron lets you focus on what matters: defining and implementing your API.

### Features

[pymacaron](https://github.com/pymacaron/pymacaron) gives
you:

* Easy implementation of a json-based REST API
* Error handling and reporting around your api endpoints (via slack or email)
* Endpoint authentication based on JWT tokens
* Transparent mapping from OpenAPI schema objects to Python objects, including custom class inheritance
* Automated validation of API data and parameters
* A structured way of blackbox testing your API, integrated in the deploy pipeline
* A production-grade stack (docker/gunicorn/Flask)
* A zero-configuration (almost) deployment pipeline against AWS Beanstalk or Google Cloud Run

### Requirements

PyMacaron requires Python3.

To deploy a pymacaron service, you will need access to a docker repository and
an Amazon AWS or Google GCP account.

### Watch the video!

[https://youtu.be/bf_dO4Gge8A](https://youtu.be/bf_dO4Gge8A)

### Credits

PyMacaron was created and is maintained by [Erwan Lemonnier](https://github.com/erwan-lemonnier).
