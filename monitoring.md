---
title: Logging and Monitoring with PyMacaron
---

Logging and Monitoring with PyMacaron
=====================================

## Logging

### Cloudwatch on AWS

Pymacaron microservices that are deployed to AWS are configured by default to
store their logs in Cloudwatch. Logs can be browsed in the AWS console for
Cloudwatch under the 'Logs' tab for the corresponding Beanstalk environment.

### Stackdriver on GCP

Pymacaron microservices that are deployed to Cloud Run and GKE are
automatically integrated with Stackdriver. Logs can be browsed in the
Stackdriver console.

## Supported monitoring frameworks

PyMacaron does not offer monitoring in itself, but is designed to support third
party monitoring frameworks (such as scoutapp) and make it easy to monitor
custom section of code.

### scoutapp.com

To activate pymacaron's builtin support for [scoutapp](https://scoutapp.com)
just add the following key to your 'pym-config.yaml':

```
scout_key: <YOUR_SCOUT_API_KEY>
```

PyMacaron will then automatically start the scout agent and monitor both your
Flask endpoints and your eventual asynchronous Celery tasks.

## Naming your app

You may want to set the display name of your pymacaron app in the 3d party
monitoring framework. Do that by setting the following keys in
'pym-config.yaml':

```
app_name_live: <NAME_OF_YOUR_APP_WHEN_RUNNING_LIVE>
app_name_dev: <NAME_OF_YOUR_APP_WHEN_RUNNING_LOCALLY>
```

Those leys are set by default to 'PYMACARON_LIVE' for an app running on Amazon
Beanstalk and 'PYMACARON_DEV' elsewhere.

### Custom monitoring

Most 3d party monitoring frameworks support custom instrumentation to let you
monitor specific sections of your code, in addition to the default monitoring
they provide. PyMacaron hides away the specific mechanisms of monitoring
frameworks behind its own 'monitor' method, used as follows:

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
