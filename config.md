---
title: PyMacaron Configuration
---

Configuration
=============


## pym-config.yaml - A global configuration object

The file 'pym-config.yaml' is the one place to find all configurable variables
used by PyMacaron microservices.

The content of 'pym-config.yaml' is automatically loaded into a singleton
object, accessible at any time by calling:

```python
from pymacaron.config import get_config

# You can access all key-values defined in pym-config.yaml:

print get_config().live_host

# And you can defined additional values of your own, though it is recommended
# to add all static values directly in pym-config.yaml to avoid race
# conditions at import time

get_config().my_api_key = 'aeouaeouaeouaeouaeou'
get_config().my_api_secret = '2348172438172364'
```

As described below, one attribute of 'pym-config.yaml' that pymacaron
supports is 'env_secrets': its value should be a list of environment variables
that will be automatically imported into Elastic Beanstalk and loaded at
runtime into the server's Docker container. This is the recommended way of
passing secrets into EC2 instances without commiting them inside your code.

All config attributes whose value matches one of the names listed in
'env_secrets' will automatically have the content of the corresponding
environment variable substituted to their value. This is very convenient when
putting secrets in 'pym-config.yaml', as shown below:

```yaml
# So, assuming you have set the environment variable MY_AWS_SECRET,
# your aws user configuration becomes as simple as:

aws_default_region: eu-central-1
aws_access_key_id: OTh0KhP89JKiudehIasd90blr
aws_secret_access_key: MY_AWS_SECRET

env_secrets:
  - MY_AWS_SECRET

# And 'aws_secret_access_key' will automagically have its value replaced with
# the value of the environment variable MY_AWS_SECRET
```

### pym-config.yaml - Expected key-values

pymacaron expects the following attributes to be set in
'pym-config.yaml':

* 'name' (MANDATORY): a short name for this project, also used when naming
  elastic beanstalk environments.

* 'live_host' (MANDATORY): url to the live server running this api.

* 'env_secrets' (OPTIONAL): names of environment variables that will be passed
  to Elastic Beanstalk and loaded at runtime into the Docker container in
  Elastic Beanstalk. This is the recommended way of passing secrets to the
  container without commiting them inside your code. Other config attributes
  whose value is one of the names listed in 'env_secrets' will have their value
  automatically substituted with the content of the environment variable.

* 'jwt_secret', 'jwt_audience', 'jwt_issuer' (OPTIONAL): values, or names of
  environment variables containing these values, setting respectively the JWT
  secret, JWT audience and JWT issuer used for generating and validating JWT
  tokens. Not needed if the API does not use authentication.

* 'default_user_id' (OPTIONAL): the default user ID to use when generating JWT
  tokens.

The following variables are needed if you want to deploy to Elastic Beanstalk
using
[pymacaron-aws](https://github.com/pymacaron/pymacaron-aws):

* 'aws_user' (MANDATORY): name of the IAM user to use when creating the
  Beanstalk environment.

* 'aws_keypair' (MANDATORY): name of the ssh keypair to deploy on the server's
  EC2 instances.

* 'aws_instance_type' (MANDATORY): the type of EC2 instance to run servers on
  (ex: 't2.micro').

* 'aws_cert_arn' (OPTIONAL): amazon ARN of a SSL certificate to use in the
  service's load balancer. If specified, the live service will be configured to
  listen on port 443 (https). If not, if will listen on port 80 (http).

* 'docker_repo' (MANDATORY): name of the hub.docker.com organization or user to
  which to upload docker images, and from which Amazon will retrieve those
  images.

* 'docker_bucket' (MANDATORY): name of the Amazon S3 bucket to which to upload
  the service's Amazon configuration.

[Here is an
example](https://github.com/pymacaron/pymacaron-helloworld/blob/master/pym-config.yaml)
of 'pym-config.yaml'.
