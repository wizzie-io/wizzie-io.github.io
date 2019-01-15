---
title: Terra-Box
permalink: /wdp_1.0.X_terra-box_index.html
datatable: false
toc: true
serviceImage: terra-box/logo.png
---

Terra-Box is a [HCL](https://github.com/hashicorp/hcl){:target="_blank"} based project that defines the full WDP infrastructure for deploying it on Kubernetes.

This page describes how the project is organized and explains a few of concepts that you should understand to work with WDP deployments.

## Terra-Box Definition Tree (TDT) {#tdt}

WDP is composed by several microservices and components which work together. On Terra-Box, a set of components is defined by a "Terra-Box Definition Tree" (TDT). It is a way of organizing the Terraform files which defines how a component is deployed on Kubernetes. A TDT is organized on the following folders:

* modules
* main
* templates

{% include image.html file="terra-box/Terra-BoxTDT.png" alt="Terra-Box TDT" max-width=500 %}

### modules

This is the place where the WDP components are defined. Each component definition is created as a Terraform module and it can be imported and used by a higher level module (defined at "main").

For instance, Wizz-Vis is a WDP component defined in `modules`. It has two Terraform files:

* `main.tf` where the kubernetes resources which deploys Wizz-Vis are defined. This module creates several kubernetes [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment){:target="_blank"}, a [service](https://kubernetes.io/docs/concepts/services-networking/service/){:target="_blank"} and a [secret](https://kubernetes.io/docs/concepts/configuration/secret){:target="_blank"}.

* `vars.tf` where the module [Input Variables](https://learn.hashicorp.com/terraform/getting-started/variables){:target="_blank"} (commonly simply called as **vars**) are declared. Module vars let us to parametrize configuration details of the component. E.g.: The admin user password.

### main {#mainmodules}

In order to deploy many WDP components at the same time, two **top level** modules are defined:

* **global** - It includes these components which are considered as part of the WDP core (global components). This components are shared for all organizations and users of that WDP deployment.
* **user** - It includes all those multi-tenant components which have an instance for each organization or user (user components).

In `main` modules (global and user) we can find a `.tf` file for each component defined in `modules` we want to include in. Each `.tf` file imports the module and configures related input variables.

Although there are several Terraform files in a `main` module, all of them are considered as the same main module.

For example, the `global` module includes some components like kafka, zookeeper or druid. All those components are deployed as part of the same main module.

### templates {#templates}

The previous parts of a TDT are used to define WDP components and group them. The `template` folder is used to set input variables **required by the main modules** though.

There are three kind of input variables templates:
* **config** - It includes those input variables which should be configured by the WDP administrator before to "apply" it. We can find a var file (.tfvars) for each main module (global.tfvars and user.tfvars).
* **flavour** - The flavour vars usually include configurations about WDP resources such as cpu, memory or replicas for each component. It can be many flavours which pre-configures this vars. Flavour vars files are organized in `global` and `user`.
* **static** - The static vars include configurations which depend of the WDP version like the components docker image. We can find a var file (.tfvars) for each main module (global.tfvars and user.tfvars).

Both `flavour` and `static` vars are pre-configured and we should only make changes in `config` vars. However, if we want to customize some `flavour` or `static` var we must add it to the a `config` var file and to modify it there.

So, it is possible to find a var that had been defined several times in different kind of template files. In this case, the following priority rule is taken into account to know which var value is considered:

```
              Var template type

            +  CONFIG
            |
            |
Priority    |  FLAVOUR
            |
            |
            -  STATIC
```

Vars defined in **config** templates have the highest priority. It is followed by those vars defined in **flavour** templates. Finally, vars defined in **static** templates have the lowest priority.

For example: A var called `kafka_replicas` is defined in a flavour template. If it is also defined in a config template, this value will be the value to consider.

## Terra-Box Project structure

Terra-box is organized in two main folders:

* **Base**: It is a TDT for deploying the base components of WDP i.e. the core elements of WDP. Some of them are Kafka, Zookeeper, Druid, and so on.

* **Platforms**: It includes many TDT where components and configurations for adding support to deploy WDP in many cloud providers (AWS, GCP, Azure, ...) or on-premise environments are defined. Here we can find new components and modifications of `Base` components too.

{% include image.html file="terra-box/Terra-BoxStructure.png" alt="Terra-Box Structure" max-width=300 %}

## Plugins

Aside from the components defined in `Base` and `Platform`, other components can be used. This optional components, which are called Plugins, don't belong to Terra-Box but are used with it.

In a few words, a plugin is a TDT that adds new components to a WDP deploy or make changes in the components created by Terra-Box or by another plugin.

## Putting all together

In order to deploy a complete WDP we need to process all the involved components defined in the different [TDT](#tdt) (Base, Platform and Plugins). The [main](#mainmodules) modules and its related [templates](#templates) of all TDT are processed at the same level by `terraform`.

{% include image.html file="terra-box/Terra-BoxProcess.png" alt="Terra-Box Process" max-width=500 %}

However, as we have seen, main modules and templates are not centralize. Thus, to apply `terraform` commands, a pre-processing is needed. This processing is made by [Prelude](wdp_1.0.X_wizzie_prelude_index.html).
