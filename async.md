---
title: PyMacaron Asynchronous Execution
---

Asynchronous Execution
======================


pymacaron comes with built-in support for asynchronous method execution
by way of Celery and Redis. All you need to do is to add the 'with_async'
key in 'pym-config.yaml':

```yaml
with_async: true
```

And decorate asynchronous methods as follows:

```python
from pymacaron_async import asynctask
from pymacaron_core.swagger.apipool import ApiPool

# Make send_email into an asynchronously executable method, called via celery
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

That's all. Read more about it on [pymacaron-async's github
page](https://github.com/pymacaron/pymacaron-async).
