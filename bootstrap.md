---
title: PyMacaron Get Started HOWTO
---

Get Started
===========

## Install PyMacaron

```shell
pipenv install pymacaron
pipenv install pymacaron-docker
pipenv install pymacaron-aws
pipenv install pymacaron-async
```

## Bootstraping

Bootstrap your project by cloning
[pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld) and
installing its dependencies:

```shell
git clone git@github.com:pymacaron/pymacaron-helloworld.git
cd pymacaron-helloworld
pip install -r requirements.txt
```

### Start the server locally

```shell
cd pymacaron-helloworld
python server --port 8080
```

### Run acceptance tests

You run acceptance tests against the above server (started in a separate
terminal) like this:

```shell
$ cd projectroot
$ pymtest --local
```

### Deploy

```shell
$ pymdeploy
```

### Testing

pymacaron-helloworld comes with tests of its default endpoints:

```shell
$ pymtest
=> Running locally against 127.0.0.1:8080
=> Will execute tests testaccept/
=> Getting an access token for the test user
=> Using token: [..some jwt token..]
=> Running acceptance tests against 127.0.0.1:8080
test_auth_version (test_version.Test) ... ok
test_ping (test_version.Test) ... ok
test_version (test_version.Test) ... ok

----------------------------------------------------------------------
Ran 3 tests in 0.462s

OK
```
## Default endpoints

All PyMacaron services implement a few default endpoints defined in this [OpenAPI specification](https://github.com/pymacaron/pymacaron/blob/master/pymacaron/ping.yaml). Those are:

* '/ping': Returns a json dictionary showing the status of the server. Convenient as a health-check endpoint. Does not require authentication.

* '/version': Returns the version of the running server when deployed in a Docker container. Does not require authentication.

* '/auth/version': Same as '/version' but requiring a valid JWT token in the HTTP Authorization header.


