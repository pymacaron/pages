---
title: PyMacaron Testing
---

Testing strategy
================

Pymacaron's deployment pipeline expects both standard python unittests under the 'test/' directory and custom API tests
under the 'testaccept/' directory.

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

API tests are test suites that inherit from 'pymacaron.test.PyMacaronTestCase' and call the API endpoints on a local server instance to reproduce real API usage scenarios.

Example of an API test:

```python
from pymacaron.test import PyMacaronTestCase

class Tests(PyMacaronTestCase, MyTestCase):

    def setUp(self):
        super().setUp()

    def test_v4_group_create__no_jwt_auth(self):
        self.assertCallReturnError(
            'POST',
            '/api/v4/group/create',
            401,
            'USER_NOT_AUTHENTICATED',
        )
        
    def test_v4_group_create__viral(self):
        tstuser_token = self.get_tstuser_token()

        j = self.assertCallReturnJson(
            'POST',
            '/api/v4/group/create',
            {
                'name': ' Book  lovers  ',
                'type': 'PUBLIC',
                'reach': 'VIRAL',
                'categories': ['SPORTS', 'BOOK_CLUB'],
                'hashtags': ['gofrendlytest'],
            },
            auth=tstuser_token,
        )

        self.assertEqual(j, {
            # the json data expected in response
        })
```


## Running acceptance tests

Acceptance tests are designed to be executed against a running instance of the
API server, be it a server you are running locally in a separate terminal, or
in a docker container, or a live cluster.

Then run [pymtest](https://github.com/pymacaron/pymacaron/blob/master/bin/pymtest):

```shell
pymtest testaccept/*.py
```

It is recommended to name your test files after the endpoint they target. So
one test file per tested API endpoint:

```
testaccept/test_v3_user_create.py
testaccept/test_v3_user_xxx_delete.py
testaccept/test_v3_user_xxx.py
```

Those tests should therefore treat the API as a blackbox and focus solely on
making API calls and testing the results. API calls should be made using test
methods from [pymacaron-unit](https://github.com/pymacaron/pymacaron-unit).

See
[pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld/blob/master/testaccept/test_version.py)
for examples of acceptance tests.

## pymtest usage

```shell
pymtest --help
```
