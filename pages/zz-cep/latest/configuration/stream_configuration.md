---
title: ZZ-Cep latest
version: latest
permalink: /zz-cep_latest_stream_configuration.html
toc: true
serviceImage: zz-cep/logo.svg
---

The stream configuration is the execution plan of the cep. Cep uses the stream configuration to build the Siddhi processing model using Kafka. You can configure how the cep gets the stream conf to do it you need to use the [Bootstrappers](/zz-cep_{{page.version}}_bootstrapper.html).

Stream configuration has two main sections:

### Streams

This section is used to define the streams attributes and types.

```json
{
    "streams": [
        {
            "streamName": "streaminput",
            "attributes": [
                {
                    "name": "timestamp",
                    "type": "long"
                }
            ]
        }
    ]
}
```

This example defines a stream called `streaminput` with a one field called `timestamp` of type `long`.
The supported attributes types are:
* boolean
* long
* string
* float
* double
* object

You can add the number of streams that you want. You must define an stream if you will use it on the next section.


### Rules

The `rules` section is made by processing rules:

```json
{
    "rules": [
        {
            "id": 1,
            "streams": {
                "in": [
                    {
                        "streamName": "streaminput",
                        "kafkaTopic": "kafkainput"
                    }
                ],
                "out": [
                    {
                        "streamName": "streamoutput",
                        "kafkaTopic": "kafkaoutput"
                    }
                ]
            },
            "executionPlan": "from streaminput select * insert into streamoutput",
            "options": {"filterOutputNullDimension": true}
        },
        {
            "id": 2,
            "streams": {
                "in": [
                    {
                        "streamName": "streaminput",
                        "kafkaTopic": "kafkainput"
                    }
                ],
                "out": [
                    {
                        "streamName": "streamoutput",
                        "kafkaTopic": "kafkaoutput"
                    }
                ]
            },
            "executionPlan": "from streaminput select * insert into streamoutput"
        }
    ]
}
```

The rules objects are made by: `id`, `streams` and `executionPlan`

* `id`: The name of the rule.
* `streams`: a map that contains the `in` and `out` relations between Siddhi and Kafka.
* `executionPlan`: the SiddhiQL query that will be used to process this rule.
* `options`: An optional map field to modify the rule behaviour.

The current `options` available value is:

* `"filterOutputNullDimension"`: This value can be set to `true` if the rule should filter out the null values at the output when the event is sended to Kafka.

The configuration for `streams` is:

```json
 "streams": {
                "in": [
                    {
                        "streamName": "streaminput",
                        "kafkaTopic": "kafkainput"
                        "dimMapper": {"fieldNameToConvertTo": "originalKafkaFieldName"}
                    }
                ],
                "out": [
                    {
                        "streamName": "streamoutput",
                        "kafkaTopic": "kafkaoutput",
                        "dimMapper": {"fieldNameToConvertTo": "originalKafkaFieldName"}
                    }
                ]
            }
```

* `streamName`: The stream name. This must match the stream name used at the execution plan.
* `kafkaTopic`: The topic name where the data will be readed/written.
* `dimMapper`: As Siddhi does not support field names with characters as `.`,`-`or `_`.
This value may be used to rename the field names to ones supported by Siddhi and then reconvert them at the output. This field is optional.


So, the full stream configuration should be:

```json
{
    "streams": [
        {
            "streamName": "streaminput",
            "attributes": [
                {
                    "name": "timestamp",
                    "type": "long"
                }
            ]
        }
    ],
    "rules": [
        {
            "id": "1",
            "streams": {
                "in": [
                    {
                        "streamName": "streaminput",
                        "kafkaTopic": "kafkainput"
                    }
                ],
                "out": [
                    {
                        "streamName": "streamoutput",
                        "kafkaTopic": "kafkaoutput"
                    }
                ]
            },
            "executionPlan": "from streaminput select * insert into streamoutput"
        },
        {
            "id": "2",
            "streams": {
                "in": [
                    {
                        "streamName": "streaminput",
                        "kafkaTopic": "kafkainput"
                    }
                ],
                "out": [
                    {
                        "streamName": "streamoutput",
                        "kafkaTopic": "kafkaoutput"
                    }
                ]
            },
            "executionPlan": "from streaminput select * insert into streamoutput"
        }
    ]
}
```

Important notes: you must define an stream if you use it at one rule. If you want to update and overwrite a rule, you must send the same rule id with a different execution plan or streams section. If you send
a rule equals to the existing one, the existing rule will not be overwritten.
The execution plans you can use are defined at [SiddhiQL Documentation](https://wso2.github.io/siddhi/documentation/siddhi-4.0/#query){:target="_blank"}
