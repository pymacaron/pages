---
title: PyMacaron Error Handling
---

Error Handling
==============

Pymacaron expects endpoints to return a valid apipool model instance on success, and to raise a PyMacaronException upon failure.

## PyMacaronExceptions

A PyMacaronException has the attributes:

* 'code': an uppercase string code for this exception
* 'status': the numerical HTTP status code for this error
* 'user_message': (optional) a user-friendly error message

## Custom exceptions

All exceptions raised by your endpoints should inherit from 'pymacaron.exceptions.PyMacaronException', as in:

```python
from pymacaron.exceptions import PyMacaronException

class InvalidUserError(PyMacaronException):
    code = 'INVALID_USER'        # Sets the value of the 'error' field in the error json object
    status = 401                 # The HTTP reply status, and 'status' field of the error json object

# An endpoint implementation...
def do_login(userdata):
    raise InvalidUserError("Sorry, we don't know you")
```

## Other exceptions

Any exception raised by your endpoint that does not inherit from PyMacaronException will be converted to
the generic 'SERVER_ERROR' exception:

```python
class UnhandledServerError(PyMacaronException):
    code = 'SERVER_ERROR'
    status = 500
```

Other python exceptions will be caught and reported as 


## Default json formatting of errors

When an endpoint raises a python exception, it gets caught by pymacaron and returned
to the caller as a json dict with the structure:

```json
{
    "error": "INVALID_USER",                      # The exception's code
    "error_description": "DB entry not found",    # String representation of the exception
    "user_message": "Sorry, we don't know you",   # User friendly explanation (optional)
    "status": 401                                 # Same as the response's HTTP status code
}
```


## Custom json formatting

To implement your own json format for API errors, declare an 'error_callback' when instantiating the pymacaron.API object:

```python
from pymacaron import API, letsgo

def custom_error_callback(e):
    """Takes a PyMacaronException and returns a json dict representing it in your API's own error format"""
    return {
        ...
    }

def start(port=None, debug=None):
    # ...
    api = API(
        app,
        port=port,
        debug=debug,
        error_callback=custom_error_callback,
    )
    
    api.load_apis(...)
    api.start()

letsgo(__name__, callback=start)
```

## Reporting exceptions

You can instruct pymcaron to report exceptions to a method of your choice. This error reporter method should have the signature:

```python
def my_error_reporter(title='', data={}, exception=None):
    """Takes a 'title', a data dict containing details about the trace and request context, and the exception instance.
    If called via 'report_warning()', the exception is undefined.
    """
    pass
```

A custom error reporter is defined when instantiating pymacaron.API:

```python

def start(port=None, debug=None):
    # ...
    api = API(
        app,
        port=port,
        debug=debug,
        error_reporter=my_error_reporter,
    ) 
    
    api.load_apis(...)
    api.start()

letsgo(__name__, callback=start)
```

## Reporting non-fatal errors

You can trigger a call to your error reporter from anywhere in your endpoint by calling 'report_warning("custom message")'.

See [pymacaron.crash](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/crash.py) for details.

## Batch declaration of custom exceptions

As an alternative to subclassing PyMacaronException, you can also define your
own PyMacaronExceptions by calling 'pymacaron.exceptions.add_error()':

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

# You can then inject MyOwnException into the current module's namespace
globals()['MyOwnException'] = exceptionclass

# And now you can import it in other modules as well
# from myexceptions import MyOwnException

```

This is convenient if you want to define a large number of custom exceptions at
once.
