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

All the attributes defined in the model's OpenAPI schema can be accessed or set as normal python instance attributes:

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

## Under the hood

PyMacaron uses [bravado-core](https://github.com/Yelp/bravado-core) models to map an OpenAPI schema object onto a Python object instance. A PyMacaron model instance is in fact a composite object that contains an instance of the corresponding bravado-core model instance, and redirects all set/get calls on attributes to it.

## Using inheritance

PyMacaron models become really powerful when used in combination with class inheritance.

### Adding inheritance in OpenAPI

Use the 'x-parent' declaration in the OpenAPI specification to automagically make all instances of a given schema object
inherit from a given python class. Here is an example taken from [pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld/blob/master/apis/helloworld.yaml):

```yaml
  Question:
    type: object
    description: A question, inheriting from a custom class
    x-parent: helloworld.models.Question
    properties:
      question:
        type: string
        description: A string
```

And the Question class is defined as such:

```python
class Question():

    def to_reply(self):
        return "You said: %s" % self.question
```

Now, any instance of 'Question' also has the method 'to_reply()':

```python
q = get_model('Question')(question='who are you?')
print q.to_reply()
```
