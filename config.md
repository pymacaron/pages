---
title: PyMacaron Configuration
---

Configuration
=============


## pym-config.yaml

All configuration settings of a PymMacaron microservice should be placed in the
file 'pym-config.yaml' in the root of the microservice project.

As an example, here is the 'pym-config.yaml' from
[pymacaron-helloworld](https://github.com/pymacaron/pymacaron-helloworld/blob/master/pym-config.yaml),
deploying a microservice to AWS Beanstalk:

```yaml
name: hello
docker_repo: <MYREPO>
docker_bucket: <DOCKER_CFG_BUCKET>
deploy_target: aws-beanstalk
aws_user: <IAM_USER_NAME>
aws_keypair: aws-eb
aws_instance_type: t2.micro
aws_cert_arn: <ARN_OF_SSL_CERTIFICATE>

with_async: true

jwt_issuer: api.helloworld.com
jwt_audience: helloworldaudienceisgreat
jwt_secret: PYM_JWT_SECRET
live_url: https://helloworld.com

env_secrets:
  - PYM_JWT_SECRET
```

The same microservice would be deployed to Google Cloud Run with this config:

```yaml
name: hello
docker_repo: <NAME_OF_CLOUD_RUN_SERVICE>  # Ex: helloworld
deploy_target: gcp-cloud-run
gcp_region: <GOOGLE_CLOUD_REGION>         # Ex: europe-west1
gcp_memory: <MAX_CONTAINER_MEMORY>        # Ex: 1G
gcp_request_concurrency: <CONCURRENCY>    # Ex: 80

with_async: true

jwt_issuer: api.helloworld.com
jwt_audience: helloworldaudienceisgreat
jwt_secret: PYM_JWT_SECRET
live_url: https://helloworld.com

env_secrets:
  - PYM_JWT_SECRET
```

## Accessing config parameters

The content of ‘pym-config.yaml’ is automatically loaded into a singleton
object, accessible at any time by calling 'ge_config()':

```python
from pymacaron.config import get_config

# To access the key-values defined in pym-config.yaml:

print get_config().live_url

conf = get_config()
print(conf.this)
print(conf.that)
```

## Environment variables and secrets

You may not want to write all settings into the configuration file.

In particular, you should absolutely not commit secret keys into that file.

The proper way to add secrets to the configuration file is to set them from
environment variables and tell PyMacaron to import those environment variables
into the configuration singleton by listing their name under the 'env_secrets'
yaml array in 'pym-config.yaml'.

All config attributes whose value matches one of the names listed in
‘env_secrets’ will automatically have the content of the corresponding
environment variable substituted to their value. This is very convenient when
putting secrets in ‘pym-config.yaml’, as shown below:

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

Access those environment variable in your code just like other settings:

```python
from pymacaron.config import get_config

print(get_config().aws_secret_access_key)
```

When deploying your microservice, PyMacaron automatically imports all
environment variables listed under 'env_secrets' into Elastic Beanstalk where
they get loaded at runtime into the server’s Docker container.


## Reference

PyMacaron expects the following key-values to be set in 'pym-config.yaml':

* 'name' (MANDATORY): a short name for this project, also used when naming
  elastic beanstalk environments.

* 'live_url' (MANDATORY): url to the live server running this api.

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

The following variables are needed if you want to deploy to Google Cloud Run
using
[pymacaron-gcp](https://github.com/pymacaron/pymacaron-gcp):

* 'deploy_target' (MANDATORY): set to 'gcp-cloud-run'

* 'docker_repo' (MANDATORY): Name of the Cloud Run service in which your
  container will run.

* 'gcp_region' (MANDATORY): the GCP region to deploy to (ex: europe-west1)

* 'gcp_memory' (MANDATORY): amount of memory to give to the container (ex: 1G)

* 'gcp_request_concurrency' (MANDATORY): number of simultaneous requests your
  container can handle before having to trigger autoscaling.

The following variables are needed if you want to deploy to Google Cloud Run
using
[pymacaron-gcp](https://github.com/pymacaron/pymacaron-gcp):

* 'deploy_target' (MANDATORY): set to 'gke'

* 'docker_repo' (MANDATORY): ID of the Google Cloud project you are deploying into.

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