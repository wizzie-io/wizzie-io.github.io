---
title: Enricher 0.6.1
version: 0.6.1
permalink: /enricher_0.6.1_scalability.html
toc: false
serviceImage: enricher/logo.svg
---


Scaling this service is very easy. The only thing you must to do is start a new instance with the same `application.id` (and the same kafka cluster). 
Doing this, both instances will do load balancing using kafka topic partitions so each instance will consume from different kafka topic partitions.

Ensure your input topics have more partitions than the number of Enricher instances.
 