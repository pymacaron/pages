---
title: PyMacaron Get Started HOWTO
---

Get Started
===========

### Install PyMacaron

```
pipenv install pymacaron
pipenv install pymacaron-aws
pipenv install pymacaron-async
```

### Bootstraping example

Bootstrap your project by cloning
[pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld).

### Start the server locally

```bash
cd pymacaron-helloworld
python server --port 8080
```

### Run acceptance tests

You run acceptance tests against the above server (started in a separate
terminal) like this:

```bash
cd projectroot
run_acceptance_tests --local
```

### Deploy

```bash
deploy_pipeline --push --deploy
```
