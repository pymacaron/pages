---
title: Server and Endpoints
---

Implementing your API
=====================

The only code you need to write to implement your pymacaron microservice is one of:

* The 'server.py' file, which you can mostly keep unchanged from the one
  provided in [pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld).

* Your endpoint implementations located in python modules whose path are
  specified in the yaml files of your API specifications.


## Server code

A standard PyMacaron 'server.py' looks like:

```python
import os
import sys
import logging
from flask import Flask
from flask_cors import CORS
from pymacaron import API, letsgo


log = logging.getLogger(__name__)


# WARNING: you must declare the Flask app as show
# below, keeping the variable name 'app' and the
# file name 'server.py', since gunicorn is configured
# to lookup the variable 'app' inside the code
# generated from 'server.py'.

app = Flask(__name__)
CORS(app)
# Here you could add custom routes, etc.


def start(port=80, debug=False):

    # Your swagger api files are under ./apis, but you
    # could have them anywhere else really.

    here = os.path.dirname(os.path.realpath(__file__))
    path_apis = os.path.join(here, "apis")

    # Tell pymacaron to spawn apis inside this Flask app.
    # Set the server's listening port, set Flask debug
    # mode on or not. Other configuration parameters,
    # such as JWT issuer, audience and secret, are set
    # in 'pym-config.yaml'.

    api = API(
        app,
        port=port,
        debug=debug,
    )

    # Find all API specifications and load them into
    # pymacaron-core

    api.load_apis(path_apis)

    # Optionally, publish the api specifications under
    # the route '/doc/<api-name>', so you may read them
    # in the Swagger-UI:
    # api.publish_apis()

    # Start the Flask app and serve all endpoints
    # defined in 'apis/myservice.yaml'

    api.start(serve="myservice")


# Entrypoint
letsgo(__name__, callback=start)
```

## Start the server

You start your server by going into the project's root directory and doing:

```shell
python server.py --port 8080
```

## Endpoint implementations

See [pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld)
for examples.