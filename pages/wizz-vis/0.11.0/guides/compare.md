---
title: Compare
permalink: /wizz-vis_0.11.0_compare.html
version: 0.11.0
---

{% include image.html file="wizz-vis/compare_1.png" alt="Compare Feature" max-width=350 %}
{% include image.html file="wizz-vis/compare_2.png" alt="Compare Feature" max-width=350 %}

Several widgets have the ability to be compared within a past interval. The referenced interval is configured through the `options` attribute in the way of `amount` `range` ago.

```json
{
  "compare": {
    "range": "minutes" | "hours" | "days" | "weeks" | "months",
    "amount": 2
  }
}
```

For instance, if we have a widget configured to represent a metric for today (Monday) and we want to compare that metric with the Monday from past week, we have to configure the `compare` option with `range: week` and `amount: 1`.

Also, we can use `previous_period` as `range` value, without the needed of complete the `amount` value.
