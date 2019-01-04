---
title: Config
version: 0.3.0
permalink: /wdp-cli_0.3.0_config.html
toc: false
---
{% include toch3.html %}


`config` command allows you to configure the CLI to work with specific resources
of WDP. Each `wdp config` subcommand configures different CLI parts.

## Deploy

WDP CLI allows you to manage multiple WDP deployments using the same tool.
Using this config command you can see which deploys are you managing and which
is set to work currently with the `wdp deploy` command. 

### add

#### Synopsis

```
wdp config deploy add [options] {DEPLOY}
```
Add a new deploy to be managed with this WDP CLI. Also, this command sets
the added deploy as current deploy.

#### Options

- `-t`, `--terrabox TERRABOX_REPO`: git clone url of Terrabox repository. This option
is mandatory.
- `-s`, `--storage STORAGE_REPO`: git clone url of `storage repository`. This option
is mandatory.
- `k`, `--k8s K8S CONTEXT`: kubectl context with access to the kubernetes cluster
where you want to deploy WDP. This option is mandatory.

#### Examples

- Add a deploy with name `example`.
```
wdp config deploy add example -t https://github.com/wizzie-io/terra-box.git -s git@github.com:wizzie-io/storage-example.git -k example
```

### delete

#### Synopsis

```
wdp config deploy [options] {DEPLOY}
```
Delete a deploy from WDP CLI. 

{% include note.html content="This operation doesn't delete the WDP infrastructure, only
deletes CLI configuration. If you want to destroy WDP infrastructure, see 
[destroy](wdp-cli_0.3.0_deploy.html#destroy) command" %}

#### Options

- `-y`, `--yes`: use this option to avoid ask for confirmation.
- `r`,`--remote`: also delete entire remote storage.

#### Examples

- Delete `example` deploy.
```
wdp config deploy delete example
```
- Delete `example` deploy without ask for confirmation and deleting also remote storage.
```
wdp config deploy delete -r -y example
```

### current

#### Synopsis

```
wdp config deploy current
```
Show current deploy that it's being managed by CLI. `wdp deploy` commands affect
to this current deploy. 

#### Options

No options.

#### Examples

- Get current deploy
```
wdp config deploy current
```

### list

#### Synopsis

```
wdp config deploy list
```
Show a list with all deploys that are being managed by this WDP CLI.

#### Options

No options.

#### Examples

- Display deploy list
```
wdp config deploy list
``` 

### use

#### Synopsis

```
wdp config deploy use DEPLOY
```
Set `DEPLOY` as current deploy. `wdp deploy` commands will affect to this deploy.

#### Options

No options.

#### Examples
- Set `example` deploy as current deploy. 
```
wdp config deploy use example
```
