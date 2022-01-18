---
title: Pymacaron models to instantiate API objects
---

Pymacaron models
================

## Swagger models as python objects

Pymacaron generates an object class for every object defined in the API's swagger specifications.

Pymacaron objects are declared using [pydantic](https://pydantic-docs.helpmanual.io/) and [PymacaronBaseModel](https://github.com/pymacaron/pymacaron-core/blob/master/pymacaron_core/models.py). Pymacaron objects can be accessed via 'pymacaron.apipool'.

Assuming the file 'apis/myapi.yaml' contains:

```yaml
definitions:

  MyLocation:
    type: object
    description: The user's location
    properties:
      lat:
        description: Latitude
        type: number
      lng:
        description: Longitude
        type: number
```

Pymacaron generates the python class 'MyLocation':

```python
from pydantic import BaseModel
from pymacaron.model import PymacaronBaseModel
class MyLocation(PymacaronBaseModel, BaseModel):
    lat: Optional[float] = None
    lng: Optional[float] = None
```

And it can be instantiated as follows:

```python
from pymacaron import apipool

l0 = apipool.myapi.MyLocation()
l1 = apipool.myapi.MyLocation(lat=1, lng=2)
```

## Swagger types to python types

Pymacaron handles mapping swagger types to their python analog.

## Loading swagger models

When [starting the server](http://pymacaron.com/code.html), the method 'load_apis()' is called. It finds the project's swagger specifications and generates their object classes and Flask routes.

```python
api.load_apis(path='apis/')
```

You can also explicitely generate pymacaron classes from a swagger file by doing:

```python
from pymacaron import apipool

apipool.load_swagger(
    'myapi',
    'apis/myapi.yaml',
)

# class MyLocation now exists:
l = apipool.myapi.MyLocation(lat=1, lng=2)
```

## Using pymacaron models

Pymacaron models inherit all [pydantic builtin methods](https://pydantic-docs.helpmanual.io/). Additional methods are also inherited from PymacaronBaseModel (see below). Object attributes are set as usual:

```python
from pymacaron import apipool

l = apipool.MyLocation(lat=1, lng=2)
l.lat = 3
s = l.lat + l.ng
```

### Convert to and from JSON dictionaries

```python
from pymacaron import apipool

l = apipool.MyLocation(lat=1, lng=2)

j = l.to_json()

l = apipool.MyLocation.from_json(j)
```

### Model name

Get the model's name, aka the name of the OpenAPI schema object this model is
based on:

```python
>>> l.get_model_name()
'MyLocation'
```

### Model properties

Get a list of the model's properties:

```python
>>> l.get_model_properties()
['lat', 'lng']
```


## Under the hood

If your swagger specifications is located at 'apis/myapi.yaml', pymacaron generates and loads the file 'apis/myapi_models.py'. Look at this file to
see how pymacaron models are declared.

## Using inheritance

Pymacaron models can be declared to inherit from a custom class. This allows you to add arbitrary methods to any API object, a very convenient pattern when building more advanced server logic.

### Adding inheritance in swagger

Use the 'x-parent' declaration in the OpenAPI specification to automagically
make all instances of a given schema object inherit from a given python
class. Let's make 'MyLocation' inherit from a custom class:

```yaml
definitions:

  MyLocation:
    type: object
    description: The user's location
    x-parent: my.module.LocationLogic
    properties:
      lat:
        description: Latitude
        type: number
      lng:
        description: Longitude
        type: number
  Question:
    type: object
    description: A question, inheriting from a custom class
    x-parent: helloworld.models.Question
    properties:
      question:
        type: string
        description: A string
```

Assuming 'my.module' contains:

```python
class LocationLogic():

    def get_google_maps_url(self):
        url = do_smart_things_to_generate_this_url()
        return url
```

Now, instances of 'MyLocation' also have the method 'get_google_maps_url()':

```python
from pymacaron import apipool

l = apipool.MyLocation(lat=1, lng=2)
l.get_google_maps_url()
```
