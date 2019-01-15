---
title: Thresholds
permalink: /wizz-vis_0.11.0_thresholds.html
version: 0.11.0
---

{% include image.html file="wizz-vis/thresholds_1.png" alt="Threshold Feature" max-width=350 %}
{% include image.html file="wizz-vis/thresholds_2.png" alt="Threshold Feature" max-width=350 %}

Several widgets have the ability to represent threshold lines through the `options` attributes.

Those thresholds have to be configured setting a color and a label as description.

```json
{
  "thresholds": [
    { "value": 200000000, "color": "red", "label": "Threshold 1" },
    { "value": 100000000, "color": "#000000", "label": "Threshold 2" }
  ]
}
```
