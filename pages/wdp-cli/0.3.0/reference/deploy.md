---
title: Deploy
version: 0.3.0
permalink: /wdp-cli_0.3.0_deploy.html
toc: false
---
{% include toch3.html %}

`Deploy` command allows you to make deployments and manage WDP components lifecycle and
configuration. See [Terra-box](terra-box_index.html) and [Prelude](wizzie_prelude_index.html) documentation to learn basic
concepts of WDP deploy management. 

{% include note.html content="`wdp deploy` command works with the WDP deploy configured
as current on the CLI configuration. You can manage this configuration with
the [config](wdp-cli_0.3.0_config.html) command." %}

## Version management

### version

#### Synopsis

```
wdp deploy version
```
Show selected version of WDP for this deploy. This is not the WDP CLI version.

#### Options

No options.

#### Examples

- Get deploy WDP version
```
wdp deploy version
```

### list-versions

#### Synopsis
```
wdp deploy list-versions
```
Show available WDP versions to configure for this deploy. 

#### Options

No options.

#### Examples

- List WDP available versions
```
wdp deploy list-versions
```

### use-version

#### Synopsis
```
wdp deploy use-version {VERSION}
```

Changes the current deploy to a specified WDP version. You can select one of the 
available supported versions, but in addition you could choose a terra-box branch or commit hash. 

{% include warning.html content="Change WDP version of a already applied WDP
could generate incompatibility problem. Please, check how to [update WDP]() before
change version if you already have a working WDP" %}

{% include note.html content="Change WDP version affects to global and to user components. However, 
you must apply this changes to global and per user component individually. If you apply the version
change to global, user component versions won't change automatically." %}

#### Options

No options.

#### Examples

- Set WDP deploy to use `1.0.0` WDP version
```
wdp deploy use-version 1.0.0
```

## Flavour management

### flavour

#### Synopsis

```
wdp deploy flavour [options]
```
Show selected [flavour]() of WDP for this deploy.

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to get the flavour for this specified 
[user component](). If this option is not provided, flavour for global is displayed.

#### Examples

- Get deploy flavour for global.
```
wdp deploy flavour
```
- Get deploy flavour for user component `example`.
```
wdp deploy flavour -c example
```

### list-flavours

#### Synopsis

```
wdp deploy list-flavours [options]
```
Show available WDP flavours to configure for this deploy. 

{% include note.html content="Available flavours may change for different
WDP versions." %}

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to get available flavours for this specified 
[user component](). If this option is not provided, flavours for global are displayed.

#### Examples

- List available flavours for global.
```
wdp deploy list-flavours
```
- List available flavours for user component `example`.
```
wdp deploy list-flavours -c example
```

### use-flavour

#### Synopsis

```
wdp deploy use-flavour [options] {FLAVOUR}
```
Changes wdp flavour to a specified flavour. Global and each user component could
have different flavours, so must be configured and applied separately.

{% include note.html content="When selecting a flavour, WDP CLI will try to set
this flavour for base, platform, and plugins. If platform or plugins don't support
this flavour, the standard flavour will be used for this specified platform or plugin." %}

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to set flavour for this specified 
[user component](). If this option is not provided, global flavour is set.

#### Examples

- Use flavour `small` for global.
```
wdp deploy use-flavour small
```
- Use flavour `standard` for user component `example`.
```
wdp deploy use-flavour standard -c example
```

## Platform management

### platform

#### Synopsis

```
wdp deploy platform
```
Show selected [platform]() for this deploy.

#### Options

No options.

#### Examples

- Get deploy platform
```
wdp deploy platform
```

### list-platforms

#### Synopsis

```
wdp deploy list-platforms
```
Show available WDP platforms to configure for this deploy. 

{% include note.html content="Available platforms may change for different
WDP versions." %}

#### Options

No options.

#### Examples

- List available platforms.
```
wdp deploy list-platforms
```

### use-platform

#### Synopsis

```
wdp deploy use-platform {PLATFORM}
```
Sets wdp platform to a specified one. 

{% include warning.html content="Change WDP platform of a already applied WDP
could generate incompatibility problems. You only should set the platform before apply your deploy." %}

#### Options

No options.

#### Examples

- Use `onpremise` platform for current deploy.
```
wdp deploy use-platform onpremise
```

## Plugin management

### add

#### Synopsis
```
wdp deploy plugin add [options] {PLUGIN}
```
Adds a plugin to current WDP deploy. Plugin name must be the referenced on it's
`README` (see it in plugin repository).

{% include important.html content="When you add a plugin to global, user modules of this
plugin are not added to each user component. You must to add the plugin to the specified
user components you want. However, to add plugins to user component, this plugin must have
been added to global previously." %}

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to add the plugin for this specified 
[user component](). If this option is not provided, adds the plugin for global.
- `r`, `--repo GIT_REPOSITORY`: git repository url of the plugin to clone it. Only specify this 
option when adding plugin to global. User components will use the same repository. 
- `v`, `--version VERSION`: plugin version to be added. Only specify this option when 
adding plugin to global. User components will use the same version. If no version is specified, 
master branch of the plugin will be used.

#### Examples

- Add `mobility` plugin to global with version `0.1.0`.
```
wdp deploy plugin add mobility -r git@github.com:wizzie-io/wdp-plugin-mobility.git -v 0.1.0
```
- Add `mobility` plugin to user component `example` (Plugin has to be added to global previously).
```
wdp deploy plugin add mobility -c example
```

### delete

#### Synopsis

```
wdp deploy plugin delete [options] {PLUGIN}
```
Delete a plugin from current WDP deploy. 

{% include important.html content="You cannot delete a plugin from global until
all user components have this plugin deleted. Also, delete a plugin with this command don't
change anything in your infrastructure. You must do an apply to reflect changes." %}

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to delete the plugin for this specified 
[user component](). If this option is not provided, plugin is deleted from global.
- `-y`,`--yes`: use this option to avoid confirmation. 

#### Examples

- Delete `mobility` plugin from global (the plugin must be deleted from all user components).
```
wdp deploy plugin delete mobility
```
- Delete `mobility` plugin from global without wait for confirmation.
```
wdp deploy plugin delete mobility -y
```
- Delete `mobility` plugin from user component `example`.
```
wdp deploy plugin delete mobility -c example
```

### list

#### Synopsis

```
wdp deploy plugin list [options]
```
Show a list with added plugins to current deploy.

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to list plugins for this specified 
[user component](). If this option is not provided, plugins added to global are displayed.

#### Examples

- List plugins from global.
```
wdp deploy plugin list
```
- List plugins from user component `example`.
```
wdp deploy plugin list -c example
```

### version

#### Synopsis

```
wdp deploy plugin version {PLUGIN}
```
Show the specified plugin version.

#### Options

No options.

#### Examples

- Show `mobility` plugin version
```
wdp deploy plugin version mobility
```
 
### list-versions

#### Synopsis

```
wdp deploy plugin list-versions {PLUGIN}
```
Show available versions for a specified WDP plugin. 

#### Options

No options.

#### Examples

- List `mobility` plugin available versions
```
wdp deploy plugin list-versions mobility
```

### use-version

#### Synopsis

```
wdp deploy plugin use-version [options] {PLUGIN}
```
Changes the plugin to a specified version. You can select one of the available
supporte deversions, but in addition you could choose a plugin branch or commit hash.

{% include warning.html content="Change plugin version of a already applied WDP could
generate incompatibility problems. Please, check the plugin documentation before change
version if you already have a working WDP with the plugin." %}

{% include note.html content="Change plugin version affects to global and to user 
components. However, you must apply this changes to global and per user component 
individually. If you apply the version change to global, user component versions won't be
applied automatically." %}

#### Options

- `-v`, `--version VERSION`: Version to use for the plugin. This option is mandatory. 

#### Examples

- Set `mobility` plugin version to `1.0.0`
```
wdp deploy plugin use-version mobility -v 1.0.0
```

## User component management

### add

#### Synopsis

```
wdp deploy component add {USER_COMPONENT}
```

Create a new [user component](). 

{% include note.html content="You must configure `app_id` variable of the user 
component with the `consumer_id` provided by [wizz-in]() for the organization before
apply it." %}

#### Options

No options.

#### Examples

- Create the user component `example`
```
wdp deploy component add example
```

### delete

#### Synopsis

```
wdp deploy component delete [options] {USER_COMPONENT}
```
Delete an user component from current WDP deploy.

{% include warning.html content="This command only deletes the user component
at infrastructure level. Only specify services of the user component will be deleted, but
no kafka topics, druid indexing tasks or wizz-in organizations. Also, you must apply this
changes to make the changes to WDP." %}

#### Options

- `-y`, `--yes`: use this option to avoid confirmation.
- `r`,`--remote`: delete this user component also from remote git storage.

#### Examples

- Delete the user component `example`.
```
wdp deploy component delete example.
```
- Delete the user component `example` also from remote without ask for confirmation.
```
wdp deploy component delete example -r -y
```

### list

#### Synopsis

```
wdp deploy component list
```
Show a list of user components from current deploy.

#### Options

No options.

#### Examples

- List user components from current deploy.
```
wdp deploy component list
```

### plugin add

#### Synopsis

```
wdp deploy component [options] plugin add {PLUGIN}
```
Similar to [`wdp deploy plugin add`](#add) with different syntax, but only
for user components. 

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to add plugin to this specified 
[user component](). This option is mandatory. Use [this command](#add) if you want
to add the plugin to global.

#### Examples:

- Add `mobility` plugin to user compoment `example` (Plugin has to be added to global previously).
```
wdp deploy component -c example plugin add mobility
```

### plugin delete

#### Synopsis

```
wdp deploy component plugin delete [options] {PLUGIN}
```
Similar to [`wdp deploy plugin delete`](#delete) with different syntax, but only
for user components.

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to delete plugin from this specified 
[user component](). This option is mandatory. Use [this command](#delete) if you want
to delete the plugin from global.

#### Examples

- Delete `mobility` plugin from user component `example`. 
```
wdp deploy component -c example plugin delete mobility
```

### plugin list

```
wdp deploy component [options] plugin list
```
Similar to [`wdp deploy plugin list`](#list) with different syntax, but only for
user components.

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to show plugin list from this specified 
[user component](). This option is mandatory. Use [this command](#list) if you want
to list global plugins.

#### Examples

- List plugins for user component `example`.
```
wdp deploy component -c example plugin list 
```

## Vars management

### show

#### Synopsis
```
wdp deploy show {base | platform | plugin} [options]
```
Show content of vars files for base, platform and plugins. Similar to execute a cat
to the vars file.

In the case of plugins, you must specify the plugin name to see vars for this
specified plugin. 

#### Options
- `-c`, `--component USER_COMPONENT`: use this option to show vars for this specified 
[user component](). If this option is not provided, global vars are displayed.

- `-t`, `--type TYPE`: use this option to show vars of an specified [type](). 
Type can be `config`, `flavour`, and `static`. By default, `config` vars are displayed. 

#### Examples

- Show base vars of type `config` for global:
```
wdp deploy show base
```
- Show platform vars of type `flavour` for the user component `example`:
```
wdp deploy show platform -t flavour -c example
```
- Show plugin `mobility` vars of type `static` for global:
```
wdp deploy show plugin mobility -t static
```

### edit

#### Synopsis
```
wdp deploy edit {base | platform | plugin} [options]
```
Open an editor to modify vars for base, platform, and plugins. You can choose
the editor that you prefer configuring it with `EDITOR` env variable. 

{% include important.html content="Selected `EDITOR` must be a synchronous command. WDP CLI
waits for the command to exit for saving edited vars so if the command is asynchronous, WDP CLI
will save immediately vars and no changes will be performed. For example, to use 
[Atom](https://atom.io/) to edit vars, you must configure `EDITOR=\"atom -w\"` to 
execute it in synchronous mode." %}

In the case of plugins, you must specify the plugin name to edit vars for this specified plugin.

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to edit vars for this specified 
[user component](). If this option is not provided, global vars are edited.

#### Examples

- Edit base vars for global:
```
wdp deploy edit base
```
- Edit platform vars for the user component `example`:
```
wdp deploy edit platform -c example
```
- Edit plugin `mobility` vars for user component `example`:
```
wdp deploy edit plugin mobility -c example
```

### get

#### Synopsis
```
wdp deploy get {base | platform | plugin} {VAR} [options]
```
Get the value of an specified variable of base, platform, or plugin. The given
variable could be defined in `config`, `flavour` or
`static` vars files. If the variable is defined in various vars files, 
the most priority one is returned.

In the case of plugins, you must specify the option `-p` with the plugin name 
to get the var for this specified plugin.

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to get vars for this specified 
[user component](). If this option is not provided, global vars are returned.
- `-p`, `--plugin PLUGIN`: only supported and required for plugin subcommand. Use
this option to get vars for this specified [plugin]().

#### Examples

- Get `druid_bucket` base var for global:
```
wdp deploy get base druid_bucket
```
- Get `app_id` base var for the user component `example`:
```
wdp deploy get base app_id -c example
```
- Get `mobility_replicas` vars from `mobility` plugin for user component `example`:
```
wdp deploy get plugin mobility_replicas -p mobility -c example
```

### set

#### Synopsis
```
wdp deploy set {base | platform | plugin} [options] {VAR} {VALUE} 
```
Set the value of an specified variable of base, platform, or plugin. The variable
is always save in the `config` var file. 

In the case of plugins, you must specify the option `-p` with the plugin name 
to set the var for this specified plugin.

{% include note.html content="When you set a variable, entire vars file is written and 
you loose the previous format and comments. This command is only recommended for 
scripting use. To set variables manually, see `edit` command" %}

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to set vars for this specified 
[user component](). If this option is not provided, global vars are configured.
- `-p`, `--plugin PLUGIN`: only supported and required for plugin subcommand. Use
this option to set vars for this specified [plugin]().

#### Examples

- Set `druid_bucket` base var to `example-bucket` for global:
```
wdp deploy set base druid_bucket example-bucket
```
- Set `app_id` base var to `abcd-abcd-abcd` for the user component `example`:
```
wdp deploy set base app_id abcd-abcd-abcd -c example
```
- Set `mobility_replicas`  vars to `2` of `mobility` plugin vars for user component `example`:
```
wdp deploy set plugin mobility_replicas 2 -p mobility -c example
```

### refresh

#### Synopsis

```
wdp deploy refresh
```
Retrieve changes on variables and state from [storage repository](). It's similar to
a [`git pull`]() command. This includes changes from global and all user components.
This command is automatically executed when apply (or destroy) the deploy.

{% include note.html content="If you are working in a colaborative way with a deploy,
execute this command before change variables to ensure you are working with last
vars version" %}

#### Options

No options.

#### Examples
- Update vars and state from remote storage
```
wdp deploy refresh
```

## Apply configuration

### init

#### Synopsis

```
wdp deploy init [options]
```
Performs a [`terraform init`](https://www.terraform.io/docs/commands/init.html) 
with the current deploy configuration, and initializes
variables. This command is executed automatically when changes other deploy configurations.

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to initializes the specified
user component. If this option is not provided, global is initialized.

#### Examples

- Initialize global for current deploy
```
wdp deploy init
```
- Initialize user component `example` for current deploy
```
wdp deploy init -c example
```

### apply

#### Synopsis

```
wdp deploy apply [options] {COMMIT_MESSAGE}
```
Apply the configuration defined in the current deploy to the WDP infrastructure. 
First, the command executes a [`terraform plan`](https://www.terraform.io/docs/commands/plan.html), showing the execution plan and then ask you to execute a 
[`terraform apply`](https://www.terraform.io/docs/commands/apply.html) with this plan. 

Also, this command saves on the [storage repository]() the changes on variables and the 
new state of the WDP infrastructure as a new commit with a specified `COMMIT_MESSAGE`.

{% include note.html content="Global and each user component have separated states and apply
only affects to the selected one. If you want to apply changes to all, you have to do it
separately" %}

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to apply configuration to 
the specified user component. If this option is not provided, 
global configuration is applied.
- `y`, `--yes`: skip plan confirmation and apply changes without ask user. 

#### Examples

- Apply current deploy configuration to global.
```
wdp deploy apply "example message"
```
- Apply current deploy configuration to global without ask for confirmation.
```
wdp deploy apply -y "example message"
```
- Apply current deploy configuration for user component `example`.
```
wdp deploy apply -c example
```

### destroy

#### Synopsis

```
wdp deploy destroy [options] {COMMIT_MESSAGE}
```
Destroy the WDP infraestructure, executing a 
[`terraform destroy`](https://www.terraform.io/docs/commands/destroy.html)
command. Also saves vars and state in the [storage repository]() as a new commit 
with a `COMMIT_MESSAGE`.

{% include warning.html content="Destroy wdp infrastructure can produce data loosing." %}

{% include note.html content="Global and each user component have separated states and destroy
only affects to the selected one. If you want to destroy changes to all, you have to do it
separately. Also, if you destroy global, user components are not destroyed, although they can't
work properly" %}

{% include warning.html content="Don't use this command to try to delete a plugin.
To do that, delete the plugin from the deploy and then, apply changes. Use this commands
deletes entire infrastructure for global or for the specified user component." %}

#### Options

- `-c`, `--component USER_COMPONENT`: use this option to destroy the deployment for 
the specified user component. If this option is not provided, 
global infrastructure is destroyed.
- `y`, `--yes`: skip confirmation and perfroms the destroy without ask user. 

#### Examples

- Destroy global infrastructure.
```
wdp deploy destroy "Example message"
```
- Destroy global infrastructure without ask for confirmation.
```
wdp deploy destroy -y "Example message"
```
- Destroy infrastructure for user component `example`.
```
wdp deploy destroy -c example
```
