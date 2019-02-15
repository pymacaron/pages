---
title: Monitoring PyMacaron
---

Monitoring PyMacaron
====================

## Built-in support for scoutapp.com

To activate pymacaron's builtin support for [scoutapp](https://scoutapp.com) just add the
following key to your 'pym-config.yaml':

```
scout_key: <YOUR_SCOUT_API_KEY>
```

PyMacaron will then automatically start the scout agent and monitor both your
Flask endpoints and your eventual asynchronous Celery jobs.

The name of your app in the scout dashboard will by default be 'PYMACARON_LIVE'
for an app running on Amazon Beanstalk and 'PYMACARON_DEV' for an app running
locally.

To change those names, set the following keys in 'pym-config.yaml':

```
app_name_live: <NAME_OF_YOUR_APP_WHEN_RUNNING_LIVE>
app_name_dev: <NAME_OF_YOUR_APP_WHEN_RUNNING_LOCALLY>
```
