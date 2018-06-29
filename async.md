---
title: PyMacaron Asynchronous Execution
---

Asynchronous Execution
======================

[pymacaron-async](https://github.com/pymacaron/pymacaron-async) is a an
extension of [pymacaron](http://pymacaron.com) that seamlessly adds
asynchronous task execution based on celery/Redis to your API endpoints.

### Install

Install redis in your dev environment:

```shell
apt-get install redis-server
pip install redis
```

Then install 'pymacaron-async':

```bash
pip install pymacaron-async
```

And add it to your microservice dependencies:

```bash
echo 'pymacaron-async' >> requirements.txt
```

### Usage

Instruct the pymacaron server to start celery workers by adding the following
line in 'pym-config.yaml':

```yaml
with_async: true
```

And make python methods asynchronous by decorating them as follows:

```python
from pymacaron_async import asynctask
from pymacaron_core.swagger.apipool import ApiPool

# Make send_email into an asynchronously
# executable method, called via celery, to
# be executed after the REST endpoint has
# returned.
@asynctask
def send_email(title, body):
    # Call 3-rd party emailing API pass
    pass

# API endpoint, defined in your swagger API spec
def do_signup_user():
    do_stuff()

    # Schedule a task sending this email and go on, not waiting for the result
    send_email.fire('Welcome!', 'You now have an account')

    return ApiPool.myapi.model.Ok()
```

That's all.

### Why and how?

It is considered good behavior for a REST api endpoint to return as quickly as
possible. Any long running task it needs to perform, such as image
manipulation, 3rd party calls whose replies are not immediately needed, logging
or analytics, should be executed after returning the HTTP response to the api
caller.

Unfortunately, this is not trivially done. Except with pymacaron-async :-)

[pymacaron-async](https://github.com/pymacaron/pymacaron-async) adds
asynchronous execution capability to
[pymacaron](https://github.com/pymacaron/pymacaron) servers, by spawning in the
background a celery worker in which all your api modules are loaded. The celery
worker loads your swagger api files in just the same way as for [pymacaron
services](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/__init__.py)
object does, imports all the modules containing your endpoint implementations
and emulates a Flask context including the current user's authentication
details. That way, code executed asynchronously in a celery task sees exactly
the same context as code executing synchronously in the endpoint method.
