
---
title: PyMacaron Project Files
---

Project files
=============

## File structure of a PyMacaron microservice

A REST api microservice built with pymacaron has a directory tree
looking like this:

```
.
|   # apis/ contains the swagger specifications of your
|   # microservice apis
├── apis
│   └── myservice.yaml
│   └── sendgrid.yaml
│   └── auth0.yaml
|
|   # Implementation of your api's endpoints
├── myservice
│   └── api.py
│
├── LICENSE           # Always have a licence :-)
├── README.rst        # and a readme!
|
├── pym-config.yaml   # PyMacaron settings
|
├── server.py         # Your API server
|
├── requirements.txt  # Your python dependencies
|
└── test              # Unitests, executed with nosetests
|   └── test_pep8.py
|
|   # Acceptance tests for your api, executed with pymtest
└── testaccept
    ├── test_v1_user_login.py
    └── test_version.py

```
