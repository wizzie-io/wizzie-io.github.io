---
title: Drill Down
permalink: /wizz-vis_0.11.0_drill_down.html
version: 0.11.0
---

{% include image.html file="wizz-vis/drill_down.png" alt="Drill Down Feature" max-width=350 %}

We can create drill downs at each widget. The drill down has to be set in the `options` attribute. The drill down can contains more than one link, external resources and dashboard links.

```json
{
  "links": [
    {
      "url": "https://wizzie.io",
      "name": "Wizzie Web",
      "type": "absolute"
    },
    {
      "url": "/dashboards/1",
      "name": "Clients detail",
      "type": "dashboard"
    }
  ]
}
```
