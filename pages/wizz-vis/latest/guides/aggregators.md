---
title: Aggregators
permalink: /wizz-vis_latest_aggregators.html
version: latest
toc: true
---

Aggregators are metrics that can be aggregated. They are often stored as numbers (integers or floats) but can also be stored as complex objects like HyperLogLog sketches or approximate histogram sketches.

## Aggregator Types

Wizz-Vis supports all of these types of aggregators:

* longSum
* doubleSum
* hyperUnique
* approxHistogramFold
* thetaSketch
* stringFirstFold
* stringLastFold

### longSum / doubleSum

Computes the sum of values as a 64-bit, signed integer and floating point respectively.

### hyperUnique

Uses `HyperLogLog` to compute the estimated cardinality of a dimension.

The HyperLogLog algorithm generates decimal estimates with some error. At Wizz-Vis, we round the result but, even with rounding, the cardinality is still an estimate.

### approxHistogramFold

Represent the frequency distribution of a metric. It can be configured the number of buckets to be represented and also the breaks of that buckets.

[More Info](http://druid.io/docs/latest/development/extensions-core/approximate-histograms.html){:target="_blank"}

**Note:** The `approxHistogramFold` aggregator can be used only with the Histogram widget, that is fully customized to get all the performance out of this aggregator.
{: .notice--warning}


### thetaSketch

A theta sketch object can be thought of as a Set data structure. At query time, sketches are read and aggregated (set unioned) together. In the end, by default, you receive the estimate of the number of unique entries in the sketch object. Also, you can use post aggregators to do union, intersection or difference on sketch columns in the same row.

[More Info](http://druid.io/docs/latest/development/extensions-core/datasketches-aggregators){:target="_blank"}

### stringFirstFold / stringLastFold

Computes the metric value with the minimum/maximum timestamp or null if no row exist.

## Post Aggregators

Various aggregators can be combined between them and make operations, resulting in a new metric.

The available operators are:

* \+
* \-
* \*
* /

Post aggregators also can be used with thetaSketch aggregators. In that case, the operators allowed are:

* UNION
* INTERSECT
* NOT

[More Info](http://druid.io/docs/latest/querying/post-aggregations.html){:target="_blank"}

## Filtered Aggregators

A filtered aggregator wraps any given aggregator, but only aggregates the values for which the given dimension filter matches.

{% include warning.html content="If only the filtered results are required, consider putting the filter on the query itself, which will be much faster since it does not require scanning all the data." %}
