---
title: PyMacaron Error Handling
---

Error Handling
==============

Python microservice framework based on Flask, OpenAPI, docker and AWS/beanstalk.

## Default Error model in PyMacaron APIs

If an endpoint raises an exception, it will be caught by pymacaron and returned
to the caller in the form of an Error json object looking like:

```json
{
    "error": "INVALID_USER",                      # Code identifying this error
    "error_description": "DB entry not found",    # Developer friendly explanation
    "user_message": "Sorry, we don't know you",   # User friendly explanation (optional)
    "status": 401                                 # Same as the response's HTTP status code
}
```

## Custom errors

You can create your own errors by subclassing the class
[PyMacaronException](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/exceptions.py).

To make the endpoint return a json Error object, just raise the exception:

```python
from pymacaron.exceptions import PyMacaronException

class InvalidUserError(PyMacaronException):
    code = 'INVALID_USER'        # Sets the value of the 'error' field in the error json object
    status = 401                 # The HTTP reply status, and 'status' field of the error json object

# An endpoint implementation...
def do_login(userdata):
    raise InvalidUserError("Sorry, we don't know you")
```

## Reporting non-fatal errors

Raising a PyMacaronException causes your endpoint to abort execution.

If you only want to report a non-fatal error but continue executing the
endpoint, you may report the error by calling 'report_error()' from
[pymacaron.crash](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/crash.py).

```python
from pymacaron.exceptions import is_error
from pymacaron.crash import report_error

profile = ApiPool.user.client.get_profile()

# Did the 'get_profile' endpoint return an Error object?
if is_error(profile):
    # Send a crash report to your admins, including the error object
    report_error("Oops. Failed to get user profile", caught=profile)
```

The crash report above will have an auto-generated title starting with the
text 'NON-FATAL BACKEND ERROR', to differentiate from crash reports that resulted
from an exception in the server, reported as 'FATAL BACKEND ERROR'.


You tell pymacaron what to do with crash reports by providing the
'pymacaron.API' constructor with an 'error_reporter' callback:

```python
from pymacaron import API, letsgo

def my_crash_reporter(title, message):

    # title is a short description of the error, while message is a full json
    # crash dump in string format, containing a traceback of the exception
    # caught, data on the caller and runtime, etc. Now, send it to who you
    # want!

    send_email(to='admin@mysite.com', subject=title, body=message)
    tell_slack(channel='crashes', msg="%s\n%s" % (title, message))

def start(port=80, debug=False):

    api = API(
        app,
        port=port,
        debug=debug,
        error_reporter=my_crash_reporter,
        ..
    )

letsgo(__name__, callback=start)
```


## Defining custom exceptions in batch

As an alternative to subclassing PyMacaronException, you can also define your
own exceptions by calling the 'add_error' method as below:

```
from pymacaron.exceptions import add_error

# add_error() generates a class named MyOwnException that inherits from
# PyMacaronException and is properly handled by
# pymacaron. add_error() returns the MyOwnException class

exceptionclass = add_error(
    name='MyOwnException',
    code='MY_OWN_EXCEPTION',
    status=401,
)

# You can then inject the MyOwnException into the current module's namespace
globals()['MyOwnException'] = exceptionclass

# And now you can import it in other modules as well
# from myexceptions import MyOwnException

```

This is convenient if you want to define a large number of custom exceptions at
once.


## Unhandled errors

If your endpoint crashes, an Error instance with a status code above or equal
to 500 will be sent back to the caller, and a crash report will be sent via
'report_error' and the 'error_reporter' callback.


## Decorating errors with 'error_decorator'

You can optionally intercept and manipulate all errors returned by a PyMacaron
microservice by specifying an 'error_decorator' hook as follows:

```python
from pymacaron import API, letsgo

def my_error_decorator(error):
    # Get errors in json format, and return the decorated error
    # In this example: we set a generic 'user_message' that is more
    # friendly that the error_description
    error['user_message'] = 'Something went really wrong! Try again later'
    return error

def start(port=80, debug=False):

    api = API(
        app,
        port=port,
        debug=debug,
        error_decorator=my_error_decorator,
        ..
    )

letsgo(__name__, callback=start)
```

## Automated reporting of slow calls

If an endpoint call exceeds the value 'get_config().report_call_exceeding_ms',
which by default is set to 1000 milliseconds, an error report will be sent with
the 'error_reporter' callback warning of a slow call.

You can change this default limit globally with:

```python
from pymacaron.config import get_config()

# Set the maximum call time to 5 sec - Slower calls trigger an error report
get_config().report_call_exceeding_ms = 5000

```

Or you can do it on a per endpoint basis, using a decorator around the endpoint
methods:

```python
from pymacaron import get_model
from pymacaron.crash import report_slow

# Set the maximum call time for 'do_login_user' to 5 sec
# Slower calls trigger an error report
@report_slow(max_ms=5000)
def do_login_user(login_data):
    ...
    return get_model('LoginResponse')(...)
```
