---
title: Normalizer Functions Aerohive
version: latest
permalink: /normalizer-functions_latest_aerohive.html
toc: true
serviceImage: normalizer-functions/logo.svg
---

### AerohiveApSelector

This function allows to select the AP with max/min rssi signal from AP array in aerohive client messages.

##### Fields

* `apsDimension`: Dimensions that contains AP list.
* `apMaxAffinity`: Select the AP with max/min rssi signal. (Default: true)

As an example:

```json
{
          "name": "aerohiveSelector",
          "className": "io.wizzie.normalizer.funcs.aerohive.AerohiveApSelector",
          "properties": {
            "apsDimension":"deviceObservers",
            "apMaxAffinity": true
          }
        }
```

If you send as input message like that:

```json
{
  "clientMac": "00222D3E0AAA",
  "ipv4": null,
  "seenEpoch": 1539865802184,
  "lat": 37.354499,
  "lng": -6.054273,
  "deviceObservers": [
    {
      "apMac": "9C5D1217D740",
      "rssi": -81,
      "locationIdHierarchy": [
        287092794040468,
        287092794040467,
        287092794040285,
        287092794040283
      ]
    },
    {
      "apMac": "9C5D12604F80",
      "rssi": -80,
      "locationIdHierarchy": [
        287092794040468,
        287092794040467,
        287092794040285,
        287092794040283
      ]
    },
    {
      "apMac": "9C5D120A6B40",
      "rssi": -88,
      "locationIdHierarchy": [
        287092794040468,
        287092794040467,
        287092794040285,
        287092794040283
      ]
    }
  ]
}
```

You will receive as output:

```json
{
  "clientMac": "00222D3E0AAA",
  "ipv4": null,
  "seenEpoch": 1539865802184,
  "lat": 37.354499,
  "lng": -6.054273,
  "apMac": "9C5D12604F80",
  "rssi": -80,
  "locationIdHierarchy": [
    287092794040468,
    287092794040467,
    287092794040285,
    287092794040283
  ]
}
```
