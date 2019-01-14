---
title: Normalizer latest
version: latest
permalink: /normalizer_latest_index.html
toc: false
---
{% include image.html file="normalizer/logo.svg" alt="Normalizer" %}

Normalizer is a stream processing engine based on Kafka Streams. Normalizer does maps (stateless and statefull), flatmaps and filters by us. You only need to define a JSON stream where you specify the process logic and how the message are transformed.
It allows you to normalize data streams from different sources to convert them to the same data schema!
Normalizer offers us: scalability, fault tolerance, back-pressure, KV states (RocksDB) and full Kafka integration ... [Try it now!!](/normalizer_{{page.version}}_base_tutorial.html)

It reads json messages from Apache Kafka and writes them back normalized to Kafka.
 
Some of its abilities are:

* Add fields.
* Select or simplify fields.
* Replace values.
* Join two or more values.
* Select max/min from an array of values.
* Classify numeric values
* Split one value into multiple dimensions
* Convert time formats.
* Convert field types.
* Calculate arithmetic values
* Rename fields.
* Filtering
* Transform one message into several
* Easy scalable (automatic discovering and load balancing).
* Check docs for more!

Normalizer is the one of the components of Wizzie Workflow used by:

* **[Wizzie Data Platform: WDP](https://wizzie.io/what-is-wizzie/#platform){:target="_blank"}**
* **[Wizzie Community Stack: WCS](https://github.com/wizzie-io/community-stack){:target="_blank"}**

## Contribute
If you have any idea for an improvement or found a bug, please open an issue. But, if you prefer, you can clone this repo and submit a pull request, helping us make Normalizer a better product.

## License
Normalizer is distributed under Apache 2.0 License.
