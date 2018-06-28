---
title: PyMacaron JWT Authentication
---

JWT Authentication
==================

JWT authentication is buit-in into PyMacaron and therefore the prefered
authentication mechanism.


## Generating JWT tokens

Your service should generate JWT tokens using the 'generate_token()' method
from
[pymacaron.auth](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/auth.py).

TODO: how to set token in response of an authentication endpoint


## Requiring JWT authentication around an endpoint

pymacaron allows you to add JWT token authentication around api
endpoints.

TODO: how to add jwt auth in yaml file


## Passing the JWT token when calling an endpoint

Authentication is achieved by passing the JWT session token in the HTTP
Authorization header of api requests:

```Authorization: Bearer {session token}```

Alternatively, you may pass the session token as a cookie named 'token' and
containing the string 'Bearer {session token}'.


## JWT issuer, audience and secret

The JWT issuer, audience and secret should be set via 'pym-config.yaml'. By
default, tokens are valid for 24 hours.


## UserID in the JWT token

JWT tokens issued by pymacaron always have a 'sub' field set to a user ID. You
may set this user ID when generating tokens as an argument to
'pymacaron.auth.generate_token()', or let pymacaron use the default user ID
defined in 'pymacaron.config.get_config().default_user_id'.
