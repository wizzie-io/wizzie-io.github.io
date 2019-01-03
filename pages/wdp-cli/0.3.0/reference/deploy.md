---
title: Deploy
version: 0.3.0
permalink: /wdp-cli_0.3.0_deploy.html
toc: false
---
{% include toch3.html %}

`Deploy` command allows you to make deployments and manage WDP components lifecycle and
configuration. See [Terra-box]() and [Prelude]() documentation to learn basic
concepts of WDP deploy management. 

## Version management

### version

TODO

### list-versions

TODO

### use-version

TODO

## Flavour management

### flavour

TODO

### list-flavours

TODO

### use-flavour

TODO

## Platform management

### platform

TODO

### list-platforms

TODO

### use-platform

TODO

## Plugin management

### add

TODO

### delete

TODO

### list

TODO

### version

TODO
 
### list-versions

TODO

### use-version

TODO

## User component management

### add

TODO

### delete

TODO

### list

TODO

### plugin add

TODO

### plugin delete

TODO

### plugin list

TODO

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

## Apply configuration

### init

TODO

### refresh

TODO

### apply

TODO

### destroy

TODO
