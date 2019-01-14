---
title: Enricher 0.6.1
version: 0.6.1
permalink: /enricher_0.6.1_custom_joiners.html
toc: true
serviceImage: enricher/logo.svg
---

On this section, we are trying to explain how to develop your own [joiners](/enricher_{{page.version}}_joiners.html).

All the joiners classes implement the [Joiner](https://github.com/wizzie-io/enricher/blob/master/service/src/main/java/io/wizzie/enricher/enrichment/join/Joiner.java){:target="_blank"} interface.

If you want to build your own joiners, you only need to implement some of different abstract classes and add de JAR into the `lib` folder inside the enricher distribution.

### BaseJoiner

The base abstract class that you need to implement your own BaseJoiner is [BaseJoiner](https://github.com/wizzie-io/enricher/blob/master/service/src/main/java/io/wizzie/enricher/enrichment/join/BaseJoiner.java){:target="_blank"} class.

Using this class you can develop your joiners like:
* [StreamPreferredJoiner](https://github.com/wizzie-io/enricher/blob/master/service/src/main/java/io/wizzie/enricher/enrichment/join/impl/StreamPreferredJoiner.java){:target="_blank"}
* [TablePreferredJoiner](https://github.com/wizzie-io/enricher/blob/master/service/src/main/java/io/wizzie/enricher/enrichment/join/impl/TablePreferredJoiner.java){:target="_blank"}

The BaseJoiner allow us join both stream and table with same key. If the stream message cannot be joined then any message is sent.

The abstract class has one method:

```java
    public Map<String, Object> join(Map<String, Object> stream, Map<String, Object> table) {
        // Join logic that return a new Map
    }
```

On each `join` call the enricher gives you a `table` and a `stream` that is the deserialized json. You must return a Map object with the new content.

### QueryableJoiner

The base abstract class that you need to implement your own QueryableJoiner is [QueryableJoiner](https://github.com/wizzie-io/enricher/blob/master/service/src/main/java/io/wizzie/enricher/enrichment/join/QueryableJoiner.java){:target="_blank"} class.

The QueryableJoiner works like BaseJoiner. The difference is that a QueryableJoiner will notify to `__enricher_queryable` topic, if received message cannot be join it with other with same key.

The abstract class has same method that [BaseJoiner](#basejoiner)

### QueryableBackJoiner

The base abstract class that you need to implement your own QueryableJoiner is [QueryableBackJoiner](https://github.com/wizzie-io/enricher/blob/master/service/src/main/java/io/wizzie/enricher/enrichment/join/QueryableBackJoiner.java){:target="_blank"} class.

The QueryableBackJoiner works like QueryableJoiner. The difference is that a QueryableBackJoiner will notify to `__enricher_queryable` topic and will resend unjoined messages to source topic, if received message cannot be join it with other with same key.

The abstract class has same method that [BaseJoiner](#basejoiner)
