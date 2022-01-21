---
title: Pymacaron models to instantiate API objects
---

Pymacaron models
================

## OpenAPI models as python objects

Pymacaron generates an object class for every object defined in the loaded OpenAPI specifications.

Every endpoint body payload and every endpoint reply defined in OpenAPI maps to a pymacaron model class.

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

j = l.to_json(
    datetime_encoder=lamdba d: d.isoformat(),
    exclude_unset=True,
    exclude_none=True,
)

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

## Using inheritance

A pymacaron model can be declared to inherit from a custom class.

This allows you to add arbitrary methods to any API object, a very convenient pattern when building more advanced server logic.

### Declaring inheritance in OpenAPI

Add 'x-parent: path.to.parent' to the OpenAPI definition of a model to make
its pymacaron class inherit from 'parent'.

The example below makes 'MyLocation' inherit from 'LocationLogic':

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
