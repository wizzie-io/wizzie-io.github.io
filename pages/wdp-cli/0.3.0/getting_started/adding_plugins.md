---
title: Adding Plugins
version: 0.3.0
permalink: /wdp-cli_0.3.0_adding_plugins.html
toc: true
---

On this guide, we are going to add a [plugin]() to a WDP installation and do 
some basic operations with it. 

### Prerequisites

- A preinstalled WDP. See [deploying WDP] (wdp-cli_0.3.0_deploying_wdp.html)
getting started to learn how to deploy WDP. 
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installed
and with context configured to have admin access to this kubernetes cluster.
- WDP-CLI installed

We recommend to read documentation about [Prelude and Terra-box](prelude_index.html) 
before follow this tutorial to learn some important concepts.

### Adding a new plugin

We already have a basic WDP deploy, but we want to add some extra functionality. 
On this tutorial, we are going to add [mobility]() plugin. To do that, we need
the plugin repository url in git format. With this, you can use the following
command:

```
wdp deploy plugin add mobility -r https://github.com/wizzie-io/wdp-plugin-mobility.git -v 0.2.0
```

This command adds mobility plugin to global infrastructure. However, mobility is a
multitenant plugin, and you have to add to each user component you want to use this plugin. 
For example, if you want to add the plugin to the `example` user component, you have to
execute:
```
wdp deploy plugin add mobility -c example
```

More information about this command in the [CLI Reference](wdp-cli_0.3.0_deploy.html#add).

### Editing vars

Currently, mobility plugin doesn't have mandatory vars to be configured, 
but if you want to configure some var for this plugin, you could do this using 
[`wdp edit plugin`](wdp-cli_0.3.0_deploy.html#edit) command.

### Applying changes

Finally, you must apply configured changes with `wdp deploy apply` command. First, 
you must apply on global with this command:
```
wdp deploy apply "add mobility to global"
```

And then, you can add to user components that you want:
```
wdp deploy apply -c example "add mobility to example user component"
```

More information about this commmand in the [CLI Reference](wdp-cli_0.3.0_deploy.html#apply)