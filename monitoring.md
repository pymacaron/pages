---
title: Monitoring PyMacaron
---

Monitoring PyMacaron
====================

PyMacaron does not offer monitoring in itself, but is designed to support third
party monitoring frameworks (such as scoutapp) and make it easy to monitor
custom section of code.

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

## Custom monitoring

To monitor time spent in a given section of code, use the 'monitor' decorator
provided by PyMacaron:

```
from pymacaron.monitor import monitor

def my_web_endpoint(data):
    do_this()

    # Report time spent while calling the 'call_third_party_authenticate'
    # method and log it to whatever monitoring system pymaracon has been set up
    # with.
    with monitor(kind='HTTP', method='Authentication')
        call_third_party_authenticate()

```

The monitor decorator will in turn call the monitoring framework you have
chosen (such as scoutapp).
