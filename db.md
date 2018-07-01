---
title: PyMacaron Database support
---

Database Support
================

PyMacaron lets you transparently serialise API objects to and from an underlying database.

Currently, only Amazon DynamoDB is supported as a serialisation backend.

## Serialisation with DynamoDB

[pymacaron-dynamodb](https://github.com/pymacaron/pymacaron-dynamodb) is a an
extension of [pymacaron](http://pymacaron.com) that lets you add serialisation
between your API objects and Amazon's Dynamodb.

### Install

```bash
pip install pymacaron-dynamodb
```

And add it to your microservice dependencies:

```bash
echo 'pymacaron-dynamodb' >> requirements.txt
```

### API definition

Here is an example adding serialisation to a Profile object, where serialising
is handled by the class 'myserver.db.profile.PersistentProfile'.

In the Swagger file defining your API, add the 'x-persist' attribute to the
objects you want to serialise:

```yaml

  Profile:
    type: object
    description: A profile that can persist to DynamoDB
    x-persist: myserver.db.profile.PersistentProfile
    properties:
      profile_id:
        type: string
        format: profile_id
        description: Unique Profile ID.

```

### In your code

Now create the 'myserver.db.profile' module containing the class
'PersistentProfile':

```python

from pymacaron_dynamodb import PersistentSwaggerObject, DynamoDBItemNotFound

class PersistentProfile(PersistentSwaggerObject):
    api_name = 'myserver'
    model_name = 'Profile'
    table_name = 'some-dynamodb-table-name'
    primary_key = 'profile_id'

# And use PersistentProfile as in the examples below:

def profile_exists(profile_id):
    """Return true if this profile exists"""
    return PersistentProfile.get_table().has_item(profile_id=profile_id)

def get_profile(profile_id):
    """Retrieve profile from dynamodb or an exception if profile does not exist"""
    try:
        p = PersistentProfile.load_from_db(profile_id)
        return p
    except DynamoDBItemNotFound as e:
        raise MyOwnException("Profile %s does not exist" % profile_id)

```

Loading and saving profiles can now be done as follows:

```python
from myserver.db.profile import get_profile, profile_exists
from pymacaron.exceptions import PyMacaronException

class UserNotFoundError(PyMacaronException):
    code = 'USER_NOT_FOUND'
    status = 404

def my_endpoint(user_id):

    if not profile_exists(user_id):
       raise UserNotFoundError('This user does not exists')

    profile = get_profile(user_id)

    # Just to illustrate updating the DynamoDB profile:
    profile.view_count = profile.view_count + 1
    profile.load_to_db()

    # Return profile as a json API object
    return profile
```