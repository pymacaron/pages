---
title: PyMacaron Testing
---

Testing Strategy
================

PyMacaron supports standard python unittests but also custom blackbox tests
that target your API endpoints. Those tests are located in two different
directories inside your PyMacaron project:


```
.
|
|   # Unitests, executed with nosetests
└── test
|   └── test_pep8.py
|
|   # Acceptance tests for your api, executed with pymtest
└── testaccept
    ├── test_v1_user_login.py
    └── test_version.py

```

## Blackbox testing

Blackbox acceptance tests target the api endpoints, and are executed via the
tool
[pymtest](https://github.com/pymacaron/pymacaron/blob/master/bin/pymtest)
that comes packaged with pymacaron.

It is recommended to name your test files after the endpoint they target. So
one test file per tested API endpoint.

Acceptance tests are designed to be executed against a running instance of the
API server, be it a server you are running locally in a separate terminal, or
in a docker container, or a live instance in Elastic Beanstalk.

Those tests should therefore treat the API as a blackbox and focus solely on
making API calls and testing the results. API calls should be made using test
methods from [pymacaron-unit](https://github.com/pymacaron/pymacaron-unit).

See
[pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld/blob/master/testaccept/test_version.py)
for an example of acceptance tests.

## pymtest usage

See 'pymtest --help' for details.
