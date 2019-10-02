---
title: PyMacaron models as instances of api objects
---

PyMacaron models
================

## API endpoints take and return objects

The core design principle of PyMacaron is to abstract all the scaffholding of setting up
a Flask microservice, its routes, its deployment pipeline, etc. The only code you should
have to write is the actual implementation of each endpoint.

An API endpoint in PyMacaron is a method that takes an object whose attributes are defined
in the api's swagger file, and return an other object also defined in the swagger file. Both
objects are instances of [PyMacaronModel](https://github.com/pymacaron/pymacaron-core/blob/master/pymacaron_core/models.py).

## Defining a model with swagger

PyMacaron models are defined in the swagger definition of the API.

Here is for example the definition of the error object returned by PyMacaron:

```yaml
  Error:
    type: object
    description: An api error
    properties:
      status:
        type: integer
        format: int32
        description: HTTP error code.
      error:
        type: string
        description: A unique identifier for this error.
      error_description:
        type: string
        description: An internal error message intended for the developer.
      user_message:
        type: string
        description: A user-friendly error message, in the user's language, to be displayed in the app.
    required:
      - status
      - error
      - error_description
    example:
      status: 500
      error: SERVER_ERROR
      error_description: local variable 'l' is None
      user_message: Something went wrong! Try again later.

```

## Using a model instance

PyMacaron models come with builtin methods:

### Constructor

Assuming your api is defined in a swagger file called 'myapi.yaml', and contains the Error model defined above, you would instantiate an error like this:

```python
from pymacaron import get_model

error = get_model('Error')(
    status=403,
    error='ACCESS_DENIED',
    user_message='You are not allowed to use the admin api',
)
```

### Get and set attributes

Via attribute name:

```python
error.status = 500
print error.status
```

Or via the dictionary interface:

```python
error['status'] = 500
print error['status']
del error['status']
```

### Convert to and from JSON

```python
from pymacaron import get_model

j = error.to_json()

error = get_model('Error').from_json(j)
```
