## PyMacaron

Python microservice framework based on Flask, OpenAPI, docker and AWS/beanstalk.

### From API design to live server

Create and deploy a Flask-based microservice running a REST api in a Docker
container on amazon AWS Elastic Beanstalk, in 4 steps:

* Fork and tweak PyMacaron Helloworld
* Write a Swagger/OpenAPI specification for your api
* Implement the API endpoints as python methods
* Deploy

BOOM! Your are live on Amazon AWS!

PyMacaron abstracts away all the scaffholding of structuring your Python app,
defining routes, serializing/deserializing between json, Python objects and
databases, containerizing your app and deploying it on Amazon.

What's left in your codebase is the only thing that matters: the business
logic.

### The PyMacaron ecosystem

[pymacaron](https://github.com/pymacaron/pymacaron) uses
[pymacaron-core](https://github.com/pymacaron/pymacaron-core) which takes a
swagger/OpenAPI yaml specification and starts a Flask app implementing it.

[pymacaron](https://github.com/pymacaron/pymacaron) uses
[pymacaron-aws](https://github.com/pymacaron/pymacaron-aws) to package the
microservice into a Docker container and deploy it to Amazon Elastic Beanstalk.

[pymacaron](https://github.com/pymacaron/pymacaron) uses
[pymacaron-unit](https://github.com/pymacaron/pymacaron-unit) to execute
blackbox acceptance tests against your live/dev microservice.

[pymacaron-async](https://github.com/pymacaron/pymacaron-async) allows you to
execute asynchronous tasks triggered by API calls, and
[pymacaron-dynamodb](https://github.com/pymacaron/pymacaron-dynamodb)
implements seamless serialisation between DynamoDB and API objects.

### Features

[pymacaron](https://github.com/pymacaron/pymacaron) gives
you:

* Easy implementation of a json-based REST API
* A best practice auto-scalling setup on Elastic Beanstalk
* Error handling and reporting around your api endpoints (via slack or email)
* Endpoint authentication based on JWT tokens
* Transparent mapping from API json data and DynamoDB to Python objects
* Automated validation of API data and parameters
* A structured way of blackbox testing your API, integrated in the deploy pipeline
* A production-grade stack (docker/gunicorn/Flask)