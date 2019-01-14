---
title: Prelude
permalink: /wdp_1.0.0_wizzie_prelude_index.html
datatable: false
toc: true
---

Wizzie Prelude is a Ruby based library that allows to work with [Terra-Box](wdp_1.0.0_terra-box_index.html) and simplifies working with it. Furthermore, it allows to automate and manage WDP deployments in a collaborative way by using [GitOps](https://www.weave.works/blog/gitops-operations-by-pull-request){:target="_blank"} principles.

## Configuration

The heart of Prelude is placed in the `.wdp` folder. It is created by default into the `HOME` directory. You can set that `HOME` directory as an environment variable. For example:

```
export HOME=/home/user
```

Inside `.wdp` you can find a **config.yml** file. This file contains configuration parameters for Prelude. Some of the most important configurations are:

* terraform - The path of the terraform executable file. Default value is `terraform` which means the executable file is in your system PATH.
* log_level - It determines the highest severity level of logs. The default value is `info`.
* log_output - Logs file path.

Example:

```
terraform: "terraform"
log_level: info
log_output: "/home/user/.wdp/prelude.log"
...
```

## Working with deploys

For Prelude, a WDP is a **deploy**. You can manage as many deploys as you want.

Inside the `.wdp` folder we can find the `deploys` folder where Prelude stores the configuration of all the deploys it manages.

Let's see how Prelude works with deploys.

### Anatomy of a deploy

A **deploy represents a single WDP** and let us to work with it by reducing much of it complexity by the use of a single abstract element.

All the deploys are placed into `.wdp/deploys/`. For example:

```
          |-- config.yml
          |  
.wdp -----|-- deploys--|-- deploys.yml
                       |
                       |-- deploy1
                       |
                       |-- deploy2
                       .             
                       .             
                       .             
```

A deploy is composed by the following elements:

* **Terra-Box**: Terra-Box git repository that is cloned in the local system.

* **Vars**: Git repository where [Terra-Box templates](wdp_1.0.0_terra-box_index.html#templates) are stored. It is a clone of the branch **vars** of the **Storage repo**.

* **State**: Git repository where the [state](https://www.terraform.io/docs/state){:target="_blank"} (.tfstate file) of the deploy is saved after any Terraform operation. It is a clone of the branch **state** of the **Storage repo**.

* **Plugins**: Folder in which Prelude stores all plugins added to the deploy.

* **Workdir**: Folder where Terra-Box modules are preprocessed by Prelude before of performing any Terraform operation.

All this elements are folders we can find inside any deploy folder. During the **deploy initialization**, Prelude does some tasks as create the deploy space into `.wdp/deploys/`, create the deploy folder tree, clone the Terra-Box git repository and the Storage repository for `vars` and `state` and configure the deploy the first time.

As a result, a deploy folder tree like this is created:

```
          |-- config.yml
          |  
.wdp -----|-- deploys--|-- deploys.yml
                       |
                       |-- deploy1
                       |
                       |-- deploy2 --|-- vars (git repository)
                       .             |
                       .             |-- state (git repository)
                       .             |
                                     |-- terra-box (git repository)
                                     |
                                     |-- plugins
                                     |
                                     |-- .workdir
```

## Terraform operations

One of the main purposes of Prelude is to abstract us of the operations with terraform and Terra-Box. This operations include the execution of terraform commands on a WDP deploy.

Prelude wraps some of the most common terraform features. You can do the following terraform actions on a deploy:

* init
* plan
* apply_plan
* apply
* destroy

This wrapper of terraform uses the modules defined in Terra-Box, the modules defined in Plugins, the Vars and the State of the deploy.

## Vars & State

### Storage repo

Prelude allows to work with WDP deploys in a collaborative way. The **Storage repo** is the key to reach this goal. It is a git repository for storing and sharing the **configurations** and the **state** of a deploy.

Prelude uses this repository to store the following information:

* The values of the input variables configured on Terra-Box.
* The latest state of the WDP deploy.
* Meta-information about the deploy like Terra-Box version, flavour, platform and so on.

Using the Storage repo brings to us the following advantages:

* Backup the deploy configurations, templates and state in a remote repository.
* Available deploy change log. Each change in the deploys is registered as a git commit.
* It makes possible team management of the deploys.
* Configuration rollback.

### Vars - Working with Terra-Box templates

Prelude manages the Terra-Box input variables. Each deploy creates a `vars` folder to work with input variables. It clones the Storage repo in the folder `.wdp/deploys/my_deploy/vars` and set the branch `vars`

Inside Vars repo you can find the following elements:

- **global folder**: It contains several Terra-Box templates (tfvars files) of global components input vars, including config vars, flavour vars and static vars.

- **user folder**: WDP is a multi-tenant platform. Then, it could have replicated components for different users with different configurations (e.g: [normalizer](normalizer_0.7.3_index.html), [enricher](enricher_0.6.1_index.html) and [cep](zz-cep_0.5.1_index.html)). Each user component is separately managed inside this folder and has it owns Terra-Box vars files (user/user1/\*.tfvars, user/user2/\*.tfvars, ...).

- **metadata.yml**: Deploy settings. E.g: WDP version, platform, flavour, available user components or enabled plugins. Prelude uses this information to get the appropriate Terra-Box templates and to offer methods to work with them.

`vars` repo folder tree:

```
...|-- deploys.yml
   |
   |-- deploy1
   |
   |-- deploy2 --|-- vars (git repository) ---------|-- metadata.yml
   .             |                                  |
   .             |                                  |-- global --|-- *.tfvars
   .             |                                  |
                 |                                  |-- user --|-- user1 --|-- *.tfvars
                 |                                             |
                 |                                             |-- user2 --|-- *.tfvars
                 |                                             .
                 |                                             .
                 |-- state (git repository)                    .
                 |
                 |-- terra-box (git repository)
                 .
                 .
                 .
```

{% include warning.html content="Some input vars may contain sensitive information like server addresses, user names or passwords. You should not use public git repositories for the storage repo." %}

### State

The state is used by Terraform to map real world resources of our deploy. We always need to have the latest state of a deploy to perform new terraform actions on it. If we lost the state of a deploy then we would lost the control of that deploy. For this reason, this information is also saved in a git repository.

Furthermore, by using git to store the state of a deploy, we can track all changes done or to share the state with another operators.

Prelude manages the deploy state in the same way as it does with vars. It clones the Storage repo in the folder `.wdp/deploys/my_deploy/state` and set the branch `state`

Inside State repo usually you can find the following elements:

- **global folder**: It contains a terraform state file (state.tfstate) with the latest state of the global components.

- **user folder**: Each user is separately managed inside this folder by using different terraform state files (user/user1/state.tfstate, user/user2/state.tfstate, ...).

`state` repo folder tree:

```
...|-- deploys.yml
   |
   |-- deploy1
   |
   |-- deploy2 --|-- vars (git repository)
   .             |
   .             |-- state (git repository) --------|-- global --|-- state.tfstate
   .             |                                  |
                 |                                  |-- user --|-- user1 --|-- state.tfstate
                 |                                             |
                 |                                             |-- user2 --|-- state.tfstate
                 |                                             .
                 |                                             .
                 |                                             .
                 |-- terra-box (git repository)
                 .
                 .
                 .
```

### Deploy customization

There are many other aspects of a deploy that can be configured with Prelude. This settings are saved in the file **metadata.yml** located in the Vars repository. Let's see an example of the metadata.yml file of a deploy:

```
---
wdp_version: 1.0.0
platform: onpremise
flavour: standard
plugins:
  reputation:
    repo: git@github.com:wizzie-io/wdp-plugin-reputation.git
    version: 0.2.0
  mobility:
    repo: git@github.com:wizzie-io/wdp-plugin-mobility.git
    version: 0.2.0
user_components:
  user1:
    flavour: standard
    plugins:
    - reputation
  user2:
    flavour: small
    plugins:
    - reputation
    - mobility
  user3:
    flavour: small
    plugins: []
```

In this file we can find the following deploy information:

* WDP version - The WDP version determines the version of Terra-Box that is used in the deploy. You can switch between different WDP version. By default, a deploy uses the latest available WDP version at the time it is created.

* Platform - Prelude allows you to configure a WDP to be deployed in different cloud providers or environments. So the platform is the type of infrastructure in which a Kubernetes cluster is provided. E.g: GCP, AWS, onpremise

* Flavour - The flavour of a WDP deploy represents a specific configuration about the required resources of the deploy components (cpu, memory, replicas, storage size, ...). The flavours are defined in Terra-Box for global and user components. You can use different flavours for global components and user components.

* Plugins - List of plugins enabled for the deploy and its version.

* User components - List of user components created in the deploy. Each user component is configured with a flavour and could use any of the enabled plugins.


## Plugins management

A plugin is an optional extension of Terra-Box which might add new components of modify some of the existent ones (E.g: reputation, mobility). A plugin is defined in a git repository on a similar way to Terra-Box.

When a plugin is added to a deploy, Prelude clones it git repository into the `plugins` folder. At the time a plugin is added to a deploy, all its global modules are added to the Working directory and its vars are added to the `vars` global folder.

Moreover, once a plugin is added to a deploy, it can be also added to a user component. Then, its user modules and vars are taken into account.

`plugins` folder tree:

```
...|-- deploys.yml
   |
   |-- deploy1
   |
   |-- deploy2 --|-- vars (git repository)
   .             |
   .             |-- state (git repository)
   .             |
                 |-- terra-box (git repository)
                 |
                 |-- .workdir
                 |
                 |-- plugins ---|-- plugin1 -----|-- main
                 |              |                |
                 |              |-- plugin2      |-- modules
                 |              |                |
                 |              .                |-- templates
                 |              .                        
                 |              .                                                           
                 .
                 .
                 .
```

## User components management

Terra-Box defines global and user components. With Prelude you can work with both of them.

For one thing, global components are automatically managed when a deploy is created and configured. For another thing, user components are designed to be multi-tenant. This means that you could create such as many user components as you need. For that reason, user components must be created on-demand.

When a user component is created, a new space is created for it in the deploy folder tree


```
...|-- deploys.yml
   |
   |-- deploy1
   |
   |-- deploy2 --|-- vars (git repository)  --------|-- global --|-- *.tfvars
   .             |                                  |
                 |                                  |-- user --|-- myUser --|-- *.tfvars
                 |                                             .
                 |                                             .
                 |                                             .
   .             |
   .             |-- state (git repository) --------|-- global --|-- state.tfstate
   .             |                                  |
                 |                                  |-- user --|-- myUser --|-- state.tfstate
                 |                                             .
                 |                                             .
                 |                                             .
                 |-- terra-box (git repository)
                 .
                 .
                 .
```
