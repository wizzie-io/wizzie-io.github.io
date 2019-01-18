---
title: Workflow Package
permalink: /wdp_1.0.X_workflow_package.html
toc: true
---

## Overview

The Workflow Package allows to define all data stack pipeline processing using configuration files. You can export/import the pacakges by organization to configure different use-cases.

## Workflow Package Structure

```
workflow_package.tar.gz
├── kafka
│   ├── topics_group_1.json
│   ├── topics_group_2.json
│   └── topics_group_3.json
├── druid
│   ├── datasource_name_1.json
│   ├── datasource_name_2.json
│   ├── datasource_name_3.json
│   └── datasource_name_4.json
└── streams
    ├── enricher.json
    ├── zz-cep.json
    └── normalizer.json
```

### Kafka

On this folder, you must create a file per each group of Kafka topics. The content of each file is something like this:

```json
[
  {
    "name": "topic_name_1",
    "partitions": 1,
    "replicas": 1,
    "config": {}
  },
  {
    "name": "topic_name_2",
    "partitions": 1,
    "replicas": 1,
    "config": {}
  }
]
```

You can check [Kafka Topic Spec](./wdp_1.0.X_batuta_kafka.html)


### Druid

On this folder, you must create a file per each streaming indexer, using the data source name as the filename.

*Format: `${datasource_name}.json`*

The content of the file is a [Streaming Indexer spec](./wdp_1.0.X_indexer_index.html#stream-indexing). It is defining what and how the data is indexed.

### Streams

This folder has the three main stream-plans (normalizer, enricher, zz-cep). Inside these files you define the stream-plan configuration to each service, using their definitions:

* [normalizer definition](./normalizer_0.7.3_stream_configuration.html)
* [enricher definition](./enricher_0.6.1_stream_configuration.html)
* [zz-cep definition](./zz-cep_0.5.1_stream_configuration.html)

## Working with Workflow Packages

There are two main methods to work with Workflow Packages:

1. [Batuta endpoints](./wdp_1.0.X_batuta_workflow.html)
2. [Wizz-In Workflow Management](./wdp_1.0.X_workflow_package_management.html)
