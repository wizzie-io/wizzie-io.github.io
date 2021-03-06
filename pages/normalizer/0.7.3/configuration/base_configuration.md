---
title: Normalizer Base Configuration
version: 0.7.3
permalink: /normalizer_0.7.3_base_configuration.html
toc: false
serviceImage: normalizer/logo.svg
---

The configuration file is a JSON format file where you specific the general properties to configure the normalizer instance. This file is different that stream config file that define the KS topology.

Example configuration file:

```json
{
  "application.id": "ks-normalizer-app-id",
  "bootstrap.servers": "localhost:9092",
  "num.stream.threads": 1,
  "bootstrapper.classname": "io.wizzie.normalizer.builder.bootstrap.KafkaBootstrapper",
  "metric.enable": true,
  "metric.listeners": ["io.wizzie.metrics.listeners.ConsoleMetricListener"],
  "metric.interval": 60000,
  "multi.id": false
}
```

| Property     | Description     |  Default Value|
| :------------- | :-------------  |   :-------------:   |
| `application.id`      | This id is used to identify a group of normalizer instances. Normally this id is used to identify different clients.      |  - |
| `bootstrap.servers`      | A list of host/port pairs to use for establishing the initial connection to the Kafka cluster. The client will make use of all servers irrespective of which servers are specified here for bootstrapping—this list only impacts the initial hosts used to discover the full set of servers. This list should be in the form `host1:port1,host2:port2`      | - |
| `num.stream.threads`      | The number of threads to execute stream processing.      | 1 |
| `bootstrapper.classname`      | The bootstrapper class reference. More info: [Bootstrapper](/normalizer_{{page.version}}_bootstrapper.html)       | - |
| `metric.enable`      | Enable metrics system.      | false |
| `metric.listeners`      | Array with metrics listeners. More info: [Metrics](/normalizer_{{page.version}}_metrics.html)      | ["io.wizzie.metrics.listeners.ConsoleMetricListener"] |
| `metric.interval`      | Metric report interval (milliseconds)      |  60000 |
| `multi.id`      | This property is used when you have multiple normalizer instances with differences `application.id` and the normalizer uses the same topic names. More Info [Multi Tenant](/normalizer_{{page.version}}_multi_tenant)      |  false |
| `metric.enable`      | Enable metrics system.      | false |
| `max.kafka.output.topics` | Max number of kafka topics that the normalizer can create to send data using the stream config | Integer.MAX_VALUE |

**Note:** If you want to configure specific [Kafka Streams properties](http://kafka.apache.org/documentation#streamsconfigs){:target="_blank"}, you can add these properties to this config file. The properties `key.serde` and `value.serde` will be overrwritten by normalizer.
