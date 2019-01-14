---
title: ZZ-Cep latest
version: latest
permalink: /zz-cep_latest_docker_configuration.html
toc: true
serviceImage: zz-cep/logo.svg
---

## How to get CEP image
You can pull the docker image from our registry:
```
$ docker pull wizzieio/zz-cep:latest
```

## How to use this image

Cep requires a running Kafka broker before it starts.
### Link Cep to Kafka broker container

#### Zookeeper

First, start a zookeeper container by executing:

```
$ docker run --rm --name zookeeper-svc --net=host wurstmeister/zookeeper
```

You can found more information about `wurstmeister/zookeeper` image [here](https://hub.docker.com/r/wurstmeister/zookeeper){:target="_blank"}

#### Kafka
Now, start a kafka broker container by executing:

```
$ docker run --rm --name kafka-broker --net=host -e KAFKA_ADVERTISED_HOST_NAME=localhost -e KAFKA_ZOOKEEPER_CONNECT=localhost:2181 -e KAFKA_ADVERTISED_PORT=9092 wurstmeister/kafka:0.10.2.1
```
You can found more information about `wurstmeister/kafka` image [here](https://hub.docker.com/r/wurstmeister/kafka){:target="_blank"}

#### Start Cep

Once kafka broker is up, we can start a Cep container and link it to the kafka broker, and configuring the APPLICATION_ID environment variable with our custom app name:

```
$ docker run --rm --name my-cep --net=host -e APPLICATION_ID=my-cep-app -e KAFKA_BOOTSTRAP_SERVER=localhost:9092 wizzieio/zz-cep:latest
```
Now you can follow the [base tutorial](/zz-cep_{{page.version}}_base_tutorial.html) to test Cep!

### Using environment variables in cep configuration

You can configure the docker image using these environment properties:

| Env Property   |      Description      |  Default Value |
|----------|---------------|-------|
| `APPLICATION_ID` |  This id is used to identify a group of cep instances | null |
| `KAFKA_BOOTSTRAP_SERVER` |  Kafka servers list | null |
| `METRIC_ENABLE` | Enable the metrics |  true  |
| `METRIC_INTERVAL`|The interval time to report metrics (milliseconds) | 60000 |

You can found more information about base configuration [here](/zz-cep_{{page.version}}_base_configuration.html)
