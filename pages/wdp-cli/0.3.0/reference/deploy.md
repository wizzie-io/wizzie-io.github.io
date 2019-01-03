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

## Vars management

### show

#### Synopsis
```
wdp deploy show {base | platform | plugin}
```
Show content of vars files for base, platform and plugins. Similar to execute a cat
to the vars file.

In the case of plugins you must specify the plugin name to see vars for this
specified plugin. 

#### Options
- `-c`, `--component USER_COMPONENT`: use this option to show vars for this specified 
[user component](). If this option is not provided, global vars are displayed.

- `-t`, `--type TYPE`: use this option to show vars of an specified [type](). 
Type can be `config`, `flavour` and `static`. By default, `config` vars are displayed. 

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
wdp deploy edit {base | platform | plugin}
```
Open an editor to modify vars for base, platform and plugins. You can choose
the editor that you prefer configuring it with `EDITOR` env variable. 

{% include important.html content="Selected `EDITOR` must be a synchronous command. WDP CLI
waits for command to exit for saving edited vars so if the command is asynchronous, WDP CLI
will save inmmediately vars and no changes will be performed. For example, to use 
[Atom](https://atom.io/) to edit vars, you must configure `EDITOR=\"atom -w\"` to 
execute it in synchronous mode." %}

In the case of plugins you must specify the plugin name to edit vars for this specified plugin.

#### Options

#### Examples

### get

### set

##
