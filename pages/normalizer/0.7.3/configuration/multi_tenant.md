---
title: Normalizer Multi-Tenant
version: 0.7.3
permalink: /normalizer_0.7.3_multi_tenant.html
toc: true
serviceImage: normalizer/logo.svg
---

The Normalize service has a multi tenant mode, on this mode it prefix the `application.id` automatically on all the Kafka topic, except the bootstrapper and metric topic.

On this mode when you define a stream definition, for example:

```json
{
  "inputs":{
    "topic1":["stream1", "stream2"]
  },
  "streams":{
    "stream1":{
      "funcs":[
        {
          "name":"myFilter",
          "className":"io.wizzie.normalizer.funcs.impl.FieldFilter",
          "properties": {
              "dimension":"FILTER-DIMENSION",
              "value":"FILTER-VALUE"
          }
        },
        {
          "name":"myFilterKey",
          "className":"io.wizzie.normalizer.funcs.impl.FieldFilter",
          "properties": {
            "dimension":"__KEY",
            "value":"FILTER-kEY"
          }
        }
      ],
      "sinks":[
        {"topic":"output"}
      ]
    }
  }
}
```

You define the topic `topic1` but really you read from Kafka topic `${APP_ID}_topic1` and when you produce to the `output` topic, you really send data to the topic `${APP_ID}_output`. On this mode we define the `APP_ID == TENANT_ID`. To enable this mode you can configure the property `multi.id` to `true`.
