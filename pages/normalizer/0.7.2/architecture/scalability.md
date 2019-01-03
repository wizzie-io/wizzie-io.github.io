---
title: Normalizer Scalability
version: 0.7.2
permalink: /normalizer_0.7.2_scalability.html
toc: false
serviceImage: normalizer/logo.svg
---


Scaling this service is very easy. The only thing you must to do is start a new instance with the same `application.id` (and the same kafka cluster). 
Doing this, both instances will do load balancing using kafka topic partitions so each instance will consume from different kafka topic partitions.

Ensure your input topics have more partitions than the number of Normalizer instances.
 