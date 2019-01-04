---
title: Enricher 0.6.1
version: 0.6.1
permalink: /enricher_0.6.1_bootstrapper.html
toc: true
---

The bootstrapper is the mechanism that is used to load stream config into enricher. You can configure the boostrapper class on the config file, using `bootstrapper.classname` property. Currently, the enricher has two bootstrappers:

* [FileBootstrapper](/enricher_{{page.version}}_bootstrapper.html#filebootstrapper)
* [KafkaBootstrapper](/enricher_{{page.version}}_bootstrapper.html#kafkabootstrapper)

## Bootstrappers

### KafkaBootstrapper

`io.wizzie.bootstrapper.bootstrappers.impl.KafkaBootstrapper`

This bootstrapper read the stream config from Kafka, so you can change the stream topology without restart the service. The bootstrapper is reading the topic `__enricher_bootstrap` using a kafka consumer instance with a random `group.id`.

| Property     | Description     |
| :------------- | :-------------  |
| `bootstrap.kafka.topics`      | Topics that are used to read the bootstrapper configuration      |
| `application.id`      | The app id to identify the client configuration      |
| `bootstrap.servers`      | The kafka broker to read the bootstrapper configuration      |

#### StreamerKafkaConfig

The StreamerKafkaConfig tool allows us to send new stream config to the enricher and read the current stream config.

You can use the script [streamer-kafka.sh](https://github.com/wizzie-io/enricher/blob/master/bin/streamer-kafka.sh) to use this tool. The tool has two modes:

###### Read Mode

The read mode allows us to read the current stream configuration to specific enricher instance.

```bash
bin/streamer-kafka.sh $BOOTSTRAP_KAFKA_SERVER $APPLICATION_ID
```

###### Write Mode

The write mode allows us to send new stream configuration to specific enricher instance.

```bash
bin/streamer-kafka.sh $BOOTSTRAP_KAFKA_SERVER $APPLICATION_ID $STREAM_CONFIG_FILE
```

### FileBootstrapper

`io.wizzie.bootstrapper.bootstrappers.impl.FileBootstrapper`

This bootstrapper read the stream config from local file system, and build a KS topology using this file. You need to add the properties on the configuration file.

| Property     | Description     |
| :------------- | :-------------  |
| `file.bootstrapper.path`      | Stream config file path      |

Library: [config-bootstrapper](https://github.com/wizzie-io/config-bootstrapper)
