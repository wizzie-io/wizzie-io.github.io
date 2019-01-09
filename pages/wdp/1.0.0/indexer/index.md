---
title: Indexer
permalink: /wdp_1.0.0_indexer_index.html
toc: true
---

The WDP Indexer works with streaming and batch data, and it is based on [Druid](http://druid.io).

## Stream Indexing

The Stream Indexing is based on [Kafka Indexing Service](http://druid.io/docs/latest/development/extensions-core/kafka-ingestion.html). WDP helps to build Druid Task Spec using a simplify spec version's that is called <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.kafka_supervisor_simplify_spec}}">KafkaSupervisorSimplifySpec</a>.

```json
{
  "dimensions": [
    "dim1", "dim2"
  ],
  "dimensionExclusions": [],
  "metrics": [
    {
      "type":"count",
      "name":"messages"
    }
  ],
  "spatialDimensions": [
    {
      "dimName": "coordinates",
      "dims": [
        "dim_lat", "dim_long"
      ]
    }
  ],
  "queryGranularity": "MINUTE",
  "kafkaTopic": "kafka-example-topic",
  "config": {
    "kafka": {
      "resetOffsetAutomatically": true,
      "useEarliestOffset": false,
      "skipOffsetGaps": true
    },
    "rejectPeriod": {
      "lateMessageRejectionPeriod": "P7D",
      "earlyMessageRejectionPeriod": "P1D"
    }
  },
  "taskCount": 1,
  "taskReplicas": 1,
  "context": {}
}
```

### Dimensions

The dimensions are indexed like `STRING`. You use dimensions to aggregate data over them using [metrics](./wdp_1.0.0_indexer_index.html#metrics).

<table>
  <colgroup>
    <col width="30%" />
    <col width="70%" />
  </colgroup>
  <thead>
    <tr class="header">
      <th>Field</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td markdown="span">dimensions</td>
      <td markdown="span">The message fields's that you want to index to generate <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.druid_rows}}">rows</a></td>
    </tr>
    <tr>
      <td markdown="span">dimensionExclusions</td>
      <td markdown="span">The message field's that you want to exclude. All other fields will be indexed</td>
    </tr>
    <tr>
      <td markdown="span">spatialDimensions</td>
      <td markdown="span">The spatial dimensions index coordinates data. You need to indicate latitude coordinate and longitude coordinate</td>
    </tr>
  </tbody>
</table>

{% include note.html content="You can't use the `dimension` and `dimensionExclusions` at the same time." %}

### Metrics

The `metrics` indicate the fields to aggregate and the way to do it. You can define all [Druid Indexing Aggregators](http://druid.io/docs/latest/querying/aggregations.html) on this section.

{% include important.html content="You can't overlap dimensions and metrics name's." %}

### Granularity

The granularity field determines how data gets bucketed across the time dimension. Supported granularity strings are: `all`, `none`, `second`, `minute`, `fifteen_minute`, `thirty_minute`, `hour`, `day`, `week`, `month`, `quarter` and `year`.

{% include note.html content="This property defines the minimun granularity that you can query the data after indexing. You can works with higher granularites than that when query the data." %}

### Configs

#### Kafka

<table>
  <colgroup>
    <col width="30%" />
    <col width="60%" />
    <col width="10%" />
  </colgroup>
  <thead>
    <tr class="header">
      <th>Field</th>
      <th>Description</th>
      <th>Default</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td markdown="span">kafkaTopic</td>
      <td markdown="span">The kafka topic where the task consumes the stream data.</td>
      <td markdown="span">-</td>
    </tr>
    <tr>
      <td markdown="span">resetOffsetAutomatically</td>
      <td markdown="span">Whether to reset the consumer offset if the next offset that it is trying to fetch is less than the earliest available offset for that particular partition. The consumer offset will be reset to either the earliest or latest offset depending on useEarliestOffset.</td>
      <td markdown="span">true</td>
    </tr>
    <tr>
      <td markdown="span">useEarliestOffset</td>
      <td markdown="span">If a supervisor is managing a dataSource for the first time, it will obtain a set of starting offsets from Kafka. This flag determines whether it retrieves the earliest or latest offsets in Kafka. This flag determines whether it retrieves the earliest or latest offsets in Kafka. Under normal circumstances, subsequent tasks will start from where the previous segments ended so this flag will only be used on first run.</td>
      <td markdown="span">false</td>
    </tr>
    <tr>
      <td markdown="span">skipOffsetGaps</td>
      <td markdown="span">Whether or not to allow gaps of missing offsets in the Kafka stream.</td>
      <td markdown="span">true</td>
    </tr>
  </tbody>
</table>

#### RejectPeriods

The reject periods use the [ISO8601 Period](https://en.wikipedia.org/wiki/ISO_8601#Durations) format.

<table>
  <colgroup>
    <col width="30%" />
    <col width="60%" />
    <col width="10%" />
  </colgroup>
  <thead>
    <tr class="header">
      <th>Field</th>
      <th>Description</th>
      <th>Default</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td markdown="span">lateMessageRejectionPeriod</td>
      <td markdown="span">Configure tasks to reject messages with timestamps earlier than this period before the task was created; for example if this is set to PT1H and the supervisor creates a task at 2016-01-01T12:00Z, messages with timestamps earlier than 2016-01-01T11:00Z will be dropped.</td>
      <td markdown="span">P7D</td>
    </tr>
    <tr>
      <td markdown="span">earlyMessageRejectionPeriod</td>
      <td markdown="span">Configure tasks to reject messages with timestamps later than this period after the task reached its taskDuration; `taskDuration=PT1H`</td>
      <td markdown="span">P1D</td>
    </tr>
  </tbody>
</table>

#### Context

The context is used to indicate specific Druid Context, this is used to overwrite default configurations.

### Task Scale & High-Availability

<table>
  <colgroup>
    <col width="30%" />
    <col width="60%" />
    <col width="10%" />
  </colgroup>
  <thead>
    <tr class="header">
      <th>Field</th>
      <th>Description</th>
      <th>Default</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td markdown="span">taskCount</td>
      <td markdown="span">The maximum number of reading tasks in a replica set. This means that the maximum number of reading tasks will be `taskCount * replicas` and the total number of tasks (reading + publishing) will be higher than this. See 'Capacity Planning' below for more details. The number of reading tasks will be less than taskCount if `taskCount > {numKafkaPartitions}`.</td>
      <td markdown="span">1</td>
    </tr>
    <tr>
      <td markdown="span">earlyMessageRejectionPeriod</td>
      <td markdown="span">he number of replica sets, where 1 means a single set of tasks (no replication). Replica tasks will always be assigned to different middlemanager to provide resiliency against node failure.</td>
      <td markdown="span">1</td>
    </tr>
  </tbody>
</table>



## Batch Indexing

The Batch Indexing is based on [Native Batch Ingestion](http://druid.io/docs/latest/ingestion/native_tasks.html). WDP helps to build Druid Task Spec using a simplify spec version's that is called <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.batch_index_simplify_spec}}">BatchIndexSimplifySpec</a>.

```json
{
  "firehose": <firehose_spec>,
  "dimensions": [
    "dim1", "dim2", "dim3"
  ],
  "dimensionExclusions": [],
  "metrics": [
    {
      "type":"count",
      "name":"messages"
    }
  ],
  "spatialDimensions": [
    {
      "dimName": "coordinates",
      "dims": [
        "dim_lat", "dim_long"
      ]
    }
  ],
  "queryGranularity": "MINUTE",
  "intervals": [
    "2017/2019"
  ],
  "context": {}
}
```

### Firehose

On this section, you can use any [Druid Firehose](http://druid.io/docs/latest/ingestion/firehose.html), including extensions firehose:

* [AWS S3](http://druid.io/docs/latest/development/extensions-core/s3.html)
* [Google Storage](http://druid.io/docs/latest/development/extensions-contrib/google.html)
* [Azure Blob Store](http://druid.io/docs/latest/development/extensions-contrib/azure.html))

### Dimensions

The dimensions are indexed like `STRING`. You use dimensions to aggregate data over them using [metrics](./wdp_1.0.0_indexer_index.html#metrics).

<table>
  <colgroup>
    <col width="30%" />
    <col width="70%" />
  </colgroup>
  <thead>
    <tr class="header">
      <th>Field</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td markdown="span">dimensions</td>
      <td markdown="span">The message fields's that you want to index to generate <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.druid_rows}}">rows</a></td>
    </tr>
    <tr>
      <td markdown="span">dimensionExclusions</td>
      <td markdown="span">The message field's that you want to exclude. All other fields will be indexed</td>
    </tr>
    <tr>
      <td markdown="span">spatialDimensions</td>
      <td markdown="span">The spatial dimensions index coordinates data. You need to indicate latitude coordinate and longitude coordinate</td>
    </tr>
  </tbody>
</table>

{% include note.html content="You can't use the `dimension` and `dimensionExclusions` at the same time." %}

### Metrics

The `metrics` indicate the fields to aggregate and the way to do it. You can define all [Druid Indexing Aggregators](http://druid.io/docs/latest/querying/aggregations.html) on this section.

{% include important.html content="You can't overlap dimensions and metrics name's." %}

### Granularity

The granularity field determines how data gets bucketed across the time dimension. Supported granularity strings are: `all`, `none`, `second`, `minute`, `fifteen_minute`, `thirty_minute`, `hour`, `day`, `week`, `month`, `quarter` and `year`.

{% include note.html content="This property defines the minimun granularity that you can query the data after indexing. You can works with higher granularites than that when query the data." %}

### Invervals

A JSON Object representing [ISO-8601 Intervals](https://en.wikipedia.org/wiki/ISO_8601#Time_intervals). This defines the time ranges to index data.

### Context

The context is used to indicate specific Druid Context, this is used to overwrite default configurations.

### Examples

#### S3 Indexing

```json
{
  "firehose": {
    "type": "static-s3",
    "uris": [
      "s3://bucket/file.gz"
    ]
  },
  "dimensions": [
    "dim1"
  ],
  "metrics": [
    {
      "type": "count",
      "name": "count"
    }
  ],
  "queryGranularity": "MINUTE",
  "intervals": [
    "2013/2019"
  ],
  "context": {
    "druid.s3.accessKey": "XXXXXX",
    "druid.indexer.fork.property.druid.s3.accessKey": "XXXXXX",
    "druid.s3.secretKey": "YYYYY",
    "druid.indexer.fork.property.druid.s3.secretKey": "YYYYY"
  }
}
```

#### Remote HTTP Indexing

```json
{
  "firehose": {
    "type"    : "http",
    "uris"  : ["http://example.com/uri1", "http://example2.com/uri2"]
  },
  "dimensions": [
    "dim1"
  ],
  "metrics": [
    {
      "type": "count",
      "name": "count"
    }
  ],
  "queryGranularity": "MINUTE",
  "intervals": [
    "2013/2019"
  ],
  "context": {
  }
}
```


#### Local File Indexing

```json
{
  "firehose": {
        "type" : "local",
        "baseDir" : "examples/indexing/",
        "filter" : "file_data.json"
  },
  "dimensions": [
    "dim1"
  ],
  "metrics": [
    {
      "type": "count",
      "name": "count"
    }
  ],
  "queryGranularity": "MINUTE",
  "intervals": [
    "2013/2019"
  ],
  "context": {
  }
}
```

{% include note.html content="You need to copy the files inside the middlemanager" %}
