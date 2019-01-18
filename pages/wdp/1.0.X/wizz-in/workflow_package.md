---
title: Workflow Package Management
permalink: /wdp_1.0.X_workflow_package_management.html
toc: true
---

## Overview

This section show the [Workflow Package](./wdp_1.0.X_workflow_package.html) Management. Allowing to download, upload and delete the Workflow Packages by organization.

At bottom of organization main page, we can see this:

{% include image.html file="./wizz-in/wizz-in-workflow-management.png" url="./images/wizz-in/wizz-in-workflow-management.png" alt="" caption="Wizz-In Workflow Package management" %}

## Operations

Using the interface, you can do the different operations:

### UPLOAD

Using the `+`, you can select a [workflow_package.tar.gz](./wdp_1.0.X_workflow_package.html) and upload to the platform.

{% include note.html content="The Workflow Package add/update the different WDP stack resources to this organization. Doesn't remove!" %}

The `UPLOAD` return a message where you can see which stack resource are created/updated or if something have errors.

{% include image.html file="./wizz-in/wizz-in-workflow-management-upload.png" url="./images/wizz-in/wizz-in-workflow-management.png" alt="" caption="Wizz-In Workflow upload example" %}

### DOWNLOAD

Generate a new workflow_package.tar.gz with the current stack resources status to this organization. and download it.

### DELETE

Remove all current stack resources to this organization.

{% include warning.html content="When you `DELETE` the current workflow, you remove all stack resource to this organization. Maybe, remove live data, indexers, configurations!" %}
