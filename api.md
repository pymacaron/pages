---
title: Pymacaron API definition
---

API definition with swagger
===========================

Pymacaron uses OpenAPI (aka swagger) to define microservice APIs. 

You should be familiar with [the OpenAPI specification](https://swagger.io/specification/) and its
[examples](https://github.com/OAI/OpenAPI-Specification/tree/master/examples/v3.0).

The prefered way to store swagger files in a pymacaron project is under the [apis/](https://github.com/pymacaron/pymacaron-helloworld/tree/master/apis) directory.

## Limitations

Pymacaron supports only OpenAPI version 2.

## Custom OpenAPI attributes

Pymacaron extends the OpenAPI standard with the following custom attributes: 

* 'x-parent: path.to.parentclass': in a model definition, make the pydantic class of this model inherit from 'parentclass'. 
* 'x-bind-server: path.to.endpointmethod': in an endpoint definition, tell Flask to call 'endpointmethod' for this route.
* 'x-decorate-server: path.to.endpointdecorator': in an endpoint definition, wrap the endpoint method with the method 'endpointdecorator'. Typically used to [enforce JWT authentication](jwt.html).

## Binding an API endpoint to a Python method

For every API endpoint in your microservice, tell Pymacaron which
python method to execute by setting 'x-bind-server' in the OpenAPI endpoint definition, as in:

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

    from pymacaron import get_model
    from pymacaron.exceptions import PyMacaronException

    def do_login(credentials):
        if not authenticate_user(credentials):
            raise PyMacaronException("Login failed")

        # Get the class representing bravado-core Welcome objects
        return get_model('LoginResponse')(
            token=generate_user_token(),
            profile=get_user_profile(),
        )

```

## Builtin endpoints

All pymacaron services implement the following [default endpoints](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/ping.yaml):

* '/ping': Returns a json dictionary showing the status of the server. Convenient as a health-check endpoint. Does not require authentication.

* '/version': Returns the version of the running server when deployed in a Docker container. Does not require authentication.

* '/auth/version': Same as '/version' but requiring a valid JWT token in the HTTP Authorization header.
