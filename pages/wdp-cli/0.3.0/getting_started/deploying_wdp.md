---
title: Deploying WDP
version: 0.3.0
permalink: /wdp-cli_0.3.0_deploying_wdp.html
toc: true
---

On this tutorial, we are going to deploy a new WDP infrastructure on a 
preexisting Kubernetes cluster, and make some common operations with it. 

### Prerequisites

- A preconfigured [Kubernetes](https://kubernetes.io/) cluster
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installed
and with context configured to have admin access to this kubernetes cluster.
- WDP-CLI installed
- A S3 Bucket and Access Keys with write permissions on this bucket. 
- A postgresql database

We recommend to read documentation about Prelude and Terra-box before follow 
this tutorial to learn some important concepts. 

### Adding a new deploy

First of all, we need to add a new deploy. To do that we need:
- Terra-box repository url (git format)
- A git repository (storage repository) url to save the deploy state and configuration (git format)

{% include note.html content="the storage repository could have sensible 
information, so **don't** use a public repository." %}

Then, you can execute to create a deploy called `example`:

```
wdp config deploy add example -t <TERRABOX_URL> -s <STORAGE_URL> -k <K8S_CONTEXT>
```

More information about this command in the [CLI Reference]()

### Configuring WDP version and platform

Now we choose which version of WDP we want to use and for which platform
(see [supported platforms]()). To list available WDP versions and platforms execute:

```
wdp deploy list-versions
wdp deploy list-platforms
```

{% include note.html content="supported platforms could be different for each 
WDP version." %}

For example, with this commands you can use 1.0.0 (Dance) version of WDP for on-premise
platform. 

```
wdp deploy use-version 1.0.0
wdp deploy use-platform onpremise
```

### Editing vars

As it's explained on [Terra-box documentation](), we need to configure some
required variables in order deploy [global components]() of WDP. 

For our example, we need to edit base and platform vars. Let's start with base. 
Executing `wdp deploy edit base`, a text editor will be opened to edit base variables:

```
## BASE - CONFIGURABLE VARS - GLOBAL COMPONENTS

# context = "CONFIGURE"
# namespace = "CONFIGURE"

# psql_server = "CONFIGURE"
# psql_port = "OPTIONAL"
# s3_endpoint = "CONFIGURE"

# kong_pg_database = "CONFIGURE"
# kong_pg_user = "CONFIGURE"
# kong_pg_password = "CONFIGURE"

# druid_psql_user = "CONFIGURE"
# druid_psql_password = "CONFIGURE"

# druid_bucket = "CONFIGURE"
# druid_s3_access_key = "CONFIGURE"
# druid_s3_secret_key = "CONFIGURE"

# wizz-in_pg_user = "CONFIGURE"
# wizz-in_pg_password = "CONFIGURE"
# wizz-in_admin_email = "OPTIONAL"
# wizz-in_admin_password = "OPTIONAL"
# wizz-in_app_url = "OPTIONAL"

## wizz-in optional mail settings
# wizz-in_mail_server = "OPTIONAL"
# wizz-in_mail_port = "OPTIONAL"
# wizz-in_mail_use_tls = "OPTIONAL"
# wizz-in_mail_username = "OPTIONAL"
# wizz-in_mail_password = "OPTIONAL"

# grafana_pg_user = "CONFIGURE"
# grafana_pg_password = "CONFIGURE"
# grafana_admin_user = "CONFIGURE"
# grafana_admin_password = "CONFIGURE"

# wizz-vis_pg_user = "CONFIGURE"
# wizz-vis_pg_password = "CONFIGURE"
# wizz-vis_app_url = "OPTIONAL"
# wizz-vis_mapbox_token = "OPTIONAL"
```

You have to provide every variable commented with `CONFIGURE`. Check the
[vars reference]() to learn more about vars.

{% include note.html content="You have to pre-create databases for every
service that requires a user and a password for postgresql, and configure them
on previous vars." %}

Also, platform vars must be configured using `wdp deploy edit platform`. In this 
example, we are using the `onpremise` platform:
```
## ONPREMISE PLATFORM PLUGIN - CONFIGURABLE VARS - GLOBAL COMPONENTS

# cdomain = "CONFIGURE"

# BATUTA
# batuta_ingress_host = "OPTIONAL"

# GRAFANA
# grafana_ingress_host = "OPTIONAL"

# KONG
# kong_ingress_host_http = "OPTIONAL"
# kong_ingress_host_https = "OPTIONAL"

# WIZZ-IN
# wizz-in_ingress_host = "OPTIONAL"

# WIZZ-VIS
# wizz-vis_ingress_host = "OPTIONAL"
```
`cdomain` is the DNS domain that will be configured for 
[ingress controllers](https://kubernetes.io/docs/concepts/services-networking/ingress/) on k8s. 

### Applying the deploy

Now, you have the deploy configured to be applied on the kubernetes cluster. To 
do that, you have to execute:

```
wdp deploy apply <COMMIT MESSAGE>
```

This command will show you a terraform plan with a description of deployment operations and will
ask you if you want to apply this changes. 
```
Are you sure to apply the current deploy? [y/N]
```
Press `y` to apply them and wait for deployment to finish. 

Check the [CLI reference]() to know more about this command. 

