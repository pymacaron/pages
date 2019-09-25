---
title: PyMacaron API definition
---

API definition with swagger
===========================

PyMacaron uses the OpenAPI format to define microservice APIs, formerly known
as the swagger format.

PyMacaron relies on
[pymacaron-core](https://github.com/pymacaron/pymacaron-core) to generate the
Flask app running your microservice API.

You will need to know how to write an OpenAPI specification before getting
started. See for example [the OpenAPI
specification](https://swagger.io/specification/) and these
[examples](https://github.com/OAI/OpenAPI-Specification/tree/master/examples/v3.0).

[pymacaron-core](https://github.com/pymacaron/pymacaron-core) extends the OpenAPI standard with a few special attributes to
bind endpoints to python methods and serialise api objects to databases.

## Binding an API endpoint to a Python method

For every API endpoint in your microservice, you need to tell PyMacaron which
python method to execute. You do that with the 'x-bind-server' attribute. Below
an example of a fictional login endpoint:

```yaml
/login:
  post:
    summary: Login a user.
    produces:
      - application/json
    x-bind-server: myserver.handlers.do_login
    parameters:
      - in: body
        name: body
        description: User login credentials.
        required: true
        schema:
          $ref: "#/definitions/Credentials"
    responses:
      200:
        description: API version
        schema:
          $ref: '#/definitions/TokenAndProfile'
      default:
        description: Error
        schema:
          $ref: '#/definitions/Error'
```

Your login method could look like:

```python

    from pymacaron_core.swagger.apipool import ApiPool
    from pymacaron_core.exceptions import PyMacaronCoreException

    def do_login(credentials):
        if not authenticate_user(credentials):
            raise PyMacaronException("Login failed")

        # Get the class representing bravado-core Welcome objects
        return ApiPool.login.model.TokenAndProfile(
            token=generate_user_token(),
            profile=get_user_profile(),
        )

```

## Authentication

To require JWT authentication on some of your API endpoints, you need to set the
'x-decorate-request' and 'x-decorate-server' attributes for those endpoints, as
described [here](jwt.html).

## Mandatory Error object

PyMacaron requires that you define an Error object in your microservice API. It should
look as follows:

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
        description: A humanly readable error message in the user''s selected language.
    required:
      - status
      - error
      - error_description
    example:
      status: 500
      error: SERVER_ERROR
      error_description: Expected data to send in reply but got none
```
