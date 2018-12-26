---
title: Terra-Box
permalink: /terra-box_index.html
datatable: false
toc: true
---

Terra-Box is a [HCL](https://github.com/hashicorp/hcl) based project that defines the full WDP infrastructure for deploying it on Kubernetes.

This page describes how the project is organized and explains a few of concepts that you should understand to work with WDP deployments.

## Terra-Box Definition Tree (TDT)

As we pointed before, WDP is composed by several microservices or components which work together. On Terra-Box, a set of components is defined by a "Terra-Box Definition Tree" (TDT). It is a way of organizing the Terraform files which defines how a component is deployed on Kubernetes. A TDT is organized on the following folders:

* modules
* main
* template

### modules

This is the place where the WDP components are defined. Each component definition is created as a Terraform module and it can be imported and used by a higher level module (defined in "main").

For instance, Wizz-Vis is a WDP component defined in `modules`. It has two Terraform files:

* `main.tf` where the kubernetes resources which deploys Wizz-Vis are defined. This module creates several kubernetes [deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment), a [service](https://kubernetes.io/docs/concepts/services-networking/service/) and a [secret](https://kubernetes.io/docs/concepts/configuration/secret).

* `vars.tf` where the module [Input Variables](https://learn.hashicorp.com/terraform/getting-started/variables) (commonly simply called as **vars**) are declared. Module vars let us to parametrize configuration details of the component. E.g.: The password of the admin user.

Most of this components can be deployed on kubernetes in a isolated way but some of them might have dependencies.

### main

In order to deploy many WDP components at the same time, two "high level" modules are defined:

* **global** - It includes these components which are considered as part of the WDP core (global components). This components are shared for all organizations and users of that WDP deployment.
* **user** - It includes all those multi-tenant components which have an instance for each organization or user (user components).

In `main` modules (global and user) we can find a `.tf` file for each component defined in `modules` we want to include in. Each `.tf` file imports the module and configures related input variables.

Although there are several Terraform files in a `main` module, all of them are considered as the same main module.

For example, the `global` module includes some components like kafka, zookeeper or druid. All those components are deployed as part of the same main module.

### templates

The previous parts of a TDT are used to define WDP components and group them. The `template` folder is used to set input variables **required by the main modules** though.

There are three kind of input variables templates:
* **config** - It includes those input variables which should be configured by the WDP administrator before to "apply" it. We can find a var file (.tfvars) for each main module (global.tfvars and user.tfvars).
* **flavour** - The flavour vars usually include configurations about WDP resources such as cpu, memory or replicas for each component. It can be many flavours which pre-configures this vars. Flavour vars files are organized in `global` and `user`.
* **static** - The static vars include configurations which depend of the WDP version like the components docker image. We can find a var file (.tfvars) for each main module (global.tfvars and user.tfvars).

Both `flavour` and `static` vars are pre-configured and we should only set the `config` vars. However, if we want to customize some `flavour` or `static` var me should add it to the a `config` var file and to modify it there.

## Terra-Box Project structure

Terra-box is organized in two main folders:

* **Base**: It is a TDT for deploying the base components of WDP i.e. the core elements of WDP. Some of them are Kafka, Zookeeper, Druid, and so on.

* **Platforms**: It includes many TDT where components and configurations for adding support to deploy WDP in many cloud providers (AWS, GCP, Azure, ...) or on-premise environments are defined. Here we can find new components and modifications of `Base` components too.

## Plugins

Aside from the components defined in `Base` and `Platform`, other components can be used. This optional components, which are called Plugins, don't belong to Terra-Box but are used with it.

In a few words, a plugin is a TDT that adds new components to a WDP deploy or make changes in the components created by Terra-Box or by another plugin.
