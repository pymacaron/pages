---
title: PyMacaron Get Started HOWTO
---

Get Started
===========

### Git clone helloworld

Bootstrap your project by cloning
[pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld) and
installing its dependencies:

```shell
git clone git@github.com:pymacaron/pymacaron-helloworld.git
cd pymacaron-helloworld
virtualenv -p python3.8 env-pymacaron
source env-pymacaron/bin/activate
pip3 install -r requirements.txt
```

### Or install PyMacaron by hand

```shell
pipenv install pymacaron
pipenv install pymacaron-docker
pipenv install pymacaron-aws
pipenv install pymacaron-gcp
pipenv install pymacaron-async
```

### Start the server locally

```shell
cd pymacaron-helloworld
python server --port 8080
```

### Run acceptance tests

Run acceptance tests against the above server (started in a separate
terminal):

```shell
$ cd pymacaron-helloworld
$ pymtest
=> Will execute tests testaccept/
=> Getting an access token for the test user
=> Using token: [..some jwt token..]
=> Running acceptance tests against 127.0.0.1:8080
test_async (test_async.Test) ... ok
test_auth (test_auth.Test) ... ok
test_auth_version (test_builtins.Test) ... ok
test_ping (test_builtins.Test) ... ok
test_version (test_builtins.Test) ... ok
test_inheritance (test_inheritance.Test) ... ok

----------------------------------------------------------------------
Ran 6 tests in 0.587s

OK
```

What happened above is that you just executed all the test files located under 'testaccept/',
which test various endpoints implemented in the Helloworld API.

You can also run unittests the usual way:

```shell
$ nosetest -xv test/
```

### Deploy

```shell
$ pymdeploy
```
