---
title: Overview
permalink: /wdp_1.0.0_prelude_index.html
datatable: false
toc: false
---

Wizzie Data Platform, also known as WDP, is a microservices based architecture built on the top of [Kubernetes](https://kubernetes.io). WDP is composed of several microservices or components which work together to provide a complete data analysis platform.

The WDP architecture and components interrelations are currently defined using infrastructure as code (IaC) approach. It allows us to automate the complete infrastructure deployment process in a repeatable and consistent way, enabling better testing and quality control, more efficient and predictable deployments, and decreased recovery times.

[Terraform](https://www.terraform.io/) is the chosen solution to reach this goal. The full WDP infrastructure is defined in a [HCL](https://github.com/hashicorp/hcl) project called **Terra-Box**.

However, Terra-Box is not the only element used for deploying WDP. The other cornerstone is a Wizzie component called **Prelude**.

Prelude is a ruby based library that allows to work with Terra-Box and simplifies working with it. Furthermore, it allows to automate and manage WDP deployments in a collaborative way by using [GitOps](https://www.weave.works/blog/gitops-operations-by-pull-request) principles. Prelude is built as a Ruby gem and to work with it we can use a compatible user interface like [WDP-CLI](https://wizzie-io.github.io/wdp-cli_0.3.0_index.html).

Terra-Box, Prelude library and the different user interfaces like WDP-CLI make up the Prelude Ecosystem.

Go ahead to the next section to know more about the Prelude Ecosystem components:
* [Terra-Box](terra-box_index.html)
* [Prelude](wizzie_prelude_index.html)
