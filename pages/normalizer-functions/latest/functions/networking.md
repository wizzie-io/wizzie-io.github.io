---
title: Normalizer Functions Networking
version: latest
permalink: /normalizer-functions_latest_networking.html
toc: true
serviceImage: normalizer-functions/logo.svg
---

### NetMapper

This function checks if an IP address belongs to a group of networks. If it belongs to them, it will add the fields you defined at `match`. If it doesn't belong, it will add the fields you defined at `notMatch`.

##### Fields

* `ipAddressDimension`: dimension to inspect. This must be in CIDR-notation (W.X.Y.Z/A)
* `networks`: map containing networks and data to apply.
* `unknownNetworks`: an optional field that contains data to apply if the IP doesn't belong to any group.

Both fields `match` and `notMatch` are optional fields. If you don't define them, data will not be added when that case is match.

As an example:

```json
        {
          "name": "myNetMapper",
          "className": "io.wizzie.ks.normalizer.funcs.network.NetMapper",
          "properties": {
            "ipAddressDimension": "ip_src",
            "unknownNetworks": {"unknownNetwork": true, "map":{"dataUnknown": 3.0}},
            "networks": [
              {
                "networks": [
                  "10.0.0.0/24",
                  "192.168.1.0/24"
                ],
                "match": {
                  "network": "private",
                  "field2": {
                    "fieldMap": 3
                  }
                }
              },
              {
                "networks": [
                  "145.0.0.0/28"
                ],
                "match": {
                  "network2": "public"
                },
                "notMatch": {
                  "network2": "unknown"
                }
              },
              {
                "networks": [
                  "146.0.0.0/28"
                ],
                "notMatch": {
                  "network3": "notMatched"
                }
              }
            ]
          }
        }

```

If you send as input an IP that belongs to 10.0.0.0/24:

```json
{"ip_src":"10.0.0.1"}
```

You will receive as output:

```json
{"ip_src":"10.0.0.1", "network":"private","field2":{"fieldMap":3}, "network2":"unknown", "network3":"notMatched"}
```

if you send:

```json
{"ip_src":"145.0.0.1"}
```

You will receive as output:

```json
{"ip_src":"145.0.0.1", "network2":"public", "network3":"notMatched"}
```

If you send:

 ```json
 {"ip_src":"147.0.0.1"}
 ```

 You will receive as output:

  ```json
  {"ip_src":"147.0.0.1", "network3":"notMatched", "network2":"unknown", "unknownNetwork": true, "map":{"dataUnknown": 3.0}}
  ```
