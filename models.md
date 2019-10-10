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



## Using model instances

PyMacaron models come with builtin methods:

### Implicit construction

The body of POST requests gets automatically converted into its corresponding PyMacaron model when passed to its endpoint method.

### Explicit construction

Taking the [openapi definition used in 'pymacaron-helloworld'](https://github.com/pymacaron/pymacaron-helloworld/blob/master/apis/helloworld.yaml), you can explicitely instantiate an Error object with:

```python
from pymacaron import get_model

error = get_model('Error')(
    status=403,
    error='ACCESS_DENIED',
    error_description='You are not allowed to use the admin api',
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

### Update from dict

For convenience, you may update multiple attributes at once in a model, based on key-values in a dictionary:

```python
error.update_from_dict({
    'error': 'THIS_OTHER_ERROR',
    'error_description': 'blablabla',
})
```
