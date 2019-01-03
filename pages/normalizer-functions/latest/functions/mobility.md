---
title: Normalizer Functions Mobility
version: latest
permalink: /normalizer-functions_latest_mobility.html
toc: true
---

### DistanceStoreMapper

This function calculates distance and speed from latitude and longitude points. It stores the last event received data in a cache in order to calculate these values.
This is a function of StoreMapper type. More info at: [StoreMapper](https://wizzie.io/normalizer/funcs/store-mapper-functions.html)

The Store Mappers are the same way to work that the Mappers 1 event to 1 event, but on this case the Store Mappers have a cache on you can compute value based on a previous events. You must add a store to the function using the key stores, this key is a JSON Array with the store name.

* `latitudeField`: the name of the latitude field. This must be in degrees (ex: 0,1234)
* `longitudeField`: the name of the longitude field. This must be in degrees (ex: 0,1234)
* `computeDistance`: a boolean optional field that set if distance must be calculated. This is `true` by default.
* `computeSpeed`: a boolean optional field that set if speed must be calculated. This is `false` by default.
* `timestamp`: a field that contains the name of the timestamp field.
* `distanceField`: a field that contains the name that will be used for the distance calculated.
* `speedField`: a field that contains the name that will be used for the speed calculated.
* `keys`: field that contains the keys that will be used to match the data. Example: device identifier, user identifier. This field define the keys used to relate one event with another. For example if you define `gateway` and `interface` as keys, the speed and distance will be calculated between events with the same values at interface and gateway. If you don't define this field, every incoming event will be related with the last event received.
* `firstTimeView`: a boolean optional field that set if distance and speed must be set to 0 when the first event arrives.
* `bypass`: a boolean optional field that set if the output should be the input message when the speed or distance can not be calculated. By default this is false.

`Important note:` you must define a store that will be used to save the events data when received. The json below shows an example of use:

```json
{
  "inputs":{
    "topic1":["stream1"]
  },
  "streams":{
    "stream1":{
      "funcs":[
        {
          "name":"distanceSpeedCounterMapper",
          "className":"io.wizzie.ks.normalizer.funcs.mobility.DistanceStoreMapper",
          "properties": {
            "latitudeField": "lat",
            "longitudeField": "long",
            "computeDistance": true,
            "computeSpeed": true,
            "timestamp": "time",
            "distanceField": "distance",
            "speedField": "speed",
            "keys": ["gateway", "interface"],
            "firstTimeView": true
          },
          "stores":["position-store"]
        }
      ],
      "sinks":[
        {"topic":"output"}
      ]
    }
  }
}
```

As you can see, the `stores` key sets `position-store` as the store that will be used to save events data. You must set it with an unique name not used by other functions.

If you send:

```json
{"time":1521431365, "gateway":"gatewayA", "interface": "interfaceA", "lat":37.3873540, "long":5.993170}
```

and:

```json
{"time":1521432365, "gateway":"gatewayA", "interface": "interfaceA", "lat":37.3873640, "long":5.991170}
```

you will receive as output:

```json
{"distance":176.69,"time":1521432365,"interface":"interfaceA","gateway":"gatewayA","lat":37.387364,"long":5.99117,"speed":176.69}
```

If you send the first event that belongs to the `keys` defined (at the example they are: `gateway` and `interface`) and you have the firstTimeValue set to `true`. You will receive:

First event sended:

```json
{"time":1521432365, "gateway":"gatewayB", "interface": "interfaceB", "lat":37.3873640, "long":5.991170}
```

Event received:
```json
{"distance":0,"time":1521432365,"interface":"interfaceB","gateway":"gatewayB","lat":37.387364,"long":5.99117,"speed":0}
```

Important note: if the `DistanceStoreMapper` function receives an event with a timestamp less than the last timestamp received, the distance and the speed can not be calculated and its output will be null if bypass option is false or the input message if bypass option is true.

### GeoTaggingMapper

This function checks if event coordinates match with some rectangle, circle or polygon and adds new dimensions in the message.

#### Common Fields

* `latitudeDimension`: dimension that indicates the latitude (need to be a number).
* `longitudeDimension`: dimension that indicates the longitude (need to be a number).
* `bounds`: Array with the bounds.

You can use three bound types: `rectangle`, `radius` and `polygon`.

The function will try to do match with all bounds and adds the `tagging` dimensions to the input message.

#### Rectangle

The bound type `rectangle` creates a rectangle area.

**Properties:**

* `minCoords`: Top-left point corner composed by [latitude, longitude] pair.
* `maxCoords`: Bottom-right point corner composed by [latitude, longitude] pair.
* `tagging`: Dimensions to add when a point does match inside rectangle area.

```json
{
  "name":"geoTaggingMapper",
  "className":"io.wizzie.normalizer.funcs.mobility.GeoTaggingMapper",
  "properties":{
    "latitudeDimension": "latitude",
    "longitudeDimension": "longitude",
    "bounds":
    [
        {"type": "rectangle", "minCoords":[2.00, 3.00], "maxCoords":[6.00, 7.00], "tagging":{"match": "rectangle"}}
    ]
  }
}
```
<div style="text-align: center">
    <img src="{{ '/assets/images/geotagging/rectangle.svg' | relative_url }}" class="img" alt="Rectangle Type">
</div>

##### Example

If you send the next message:

```json
{"timestamp": 123456789, "latitude": 4.0, "longitude": 5.0}
```

You will get:

```json
{"timestamp": 123456789, "latitude": 4.0, "longitude": 5.0, "match": "rectangle"}
```

#### Radius

The bound type `radius` creates a circle area.

**Properties:**

* `center`: Center of the circle to define composed by [latitude, longitude] pair.
* `radius`: The radius of circle composed by a number or distance (In kilometers).
* `tagging`: Dimensions to add when a point does match inside circle area.

```json
{
  "name":"geoTaggingMapper",
  "className":"io.wizzie.normalizer.funcs.mobility.GeoTaggingMapper",
  "properties":{
    "latitudeDimension": "latitude",
    "longitudeDimension": "longitude",
    "bounds":
    [
        {"type": "radius", "center":[5.00, 5.00], "radius":2, "tagging":{"match": "radius"}},
        {"type": "radius", "center":[2.00, 2.00], "radius":"1km", "tagging":{"match": "radius"}}
    ]
  }
}
```

<div style="text-align: center">
    <img src="{{ '/assets/images/geotagging/radius.svg' | relative_url }}" class="img" alt="Radius Type">
</div>

The distance is calculated by [Haversine formula](https://en.wikipedia.org/wiki/Haversine_formula). The Haversine formula determines the great-circle distance between two points on a sphere given their longitudes and latitudes. The minimum distance to set in radius parameter is 0.001Km.

##### Example

If you send the next message:

```json
{"timestamp": 123456789, "latitude": 5.0, "longitude": 5.0}
```

You will get:

```json
{"timestamp": 123456789, "latitude": 5.0, "longitude": 5.0, "match": "radius"}
```

#### Polygon

The bound type `polygon` allows you create a custom area defining the points of path. We recommend to define connexed areas.


**Properties:**

* `path`: Array of [latitude, longitude] pairs where you define your custom area. The first pair indicates the beginning of path.
* `tagging`: Dimensions to add when a point does match inside polygon area.


```json
{
  "name":"geoTaggingMapper",
  "className":"io.wizzie.normalizer.funcs.mobility.GeoTaggingMapper",
  "properties":{
    "latitudeDimension": "latitude",
    "longitudeDimension": "longitude",
    "bounds":
    [
        {"type": "polygon", "path": [[3.0, 1.0], [1.0, 5.0], [8.0, 6.0]], "tagging": {"match": "polygon"}}
    ]
  }
}
```

<div style="text-align: center">
    <img src="{{ '/assets/images/geotagging/polygon.svg' | relative_url }}" class="img" alt="Polygon Type">
</div>

##### Example

If you send the next message:

```json
{"timestamp": 123456789, "latitude": 3.0, "longitude": 4.0}
```

You will get:

```json
{"timestamp": 123456789, "latitude": 3.0, "longitude": 4.0, "match": "polygon"}
```

#### Superposition

When you define multiple bounds you can do superposition to match multiple areas.

```json
{
  "name":"geoTaggingMapper",
  "className":"io.wizzie.normalizer.funcs.mobility.GeoTaggingMapper",
  "properties":{
    "latitudeDimension": "latitude",
    "longitudeDimension": "longitude",
    "bounds":
    [
        {"type": "polygon", "path": [[2.0, 1.0], [4.0, 5.0], [6.0, 1.0]], "tagging": {"matchPolygon1": true}},
        {"type": "polygon", "path": [[2.0, 7.0], [4.0, 3.0], [6.0, 7.0]], "tagging": {"matchPolygon2": "true"}},
        {"type": "radius", "center":[6.00, 7.00], "radius":2, "tagging":{"matchRadius": true}},
        {"type": "rectangle", "minCoords":[4.00, 2.00], "maxCoords":[8.00, 6.00], "tagging":{"matchRectangle": true}}
    ]
  }
}
```

<div style="text-align: center">
    <img src="{{ '/assets/images/geotagging/superposition.svg' | relative_url }}" class="img" alt="Superposition Type">
</div>


##### Example

If you send the next messages:

Test point A

```json
{"timestamp": 123456789, "latitude": 4.0, "longitude": 4.0}
```

Test point B

```json
{"timestamp": 123456789, "latitude": 5.0, "longitude": 5.5}
```

Test point C

```json
{"timestamp": 123456789, "latitude": 4.0, "longitude": 6.5}
```

You will get:

Test point A

```json
{"timestamp": 123456789, "latitude": 4.0, "longitude": 4.0, "matchPolygon1": true, "matchPolygon2": true}
```

Test point B

```json
{"timestamp": 123456789, "latitude": 5.0, "longitude": 5.5, "matchPolygon2": true, "matchRectangle": true, "matchRadius": true}
```

Test point C

```json
{"timestamp": 123456789, "latitude": 5.0, "longitude": 6.5, "matchPolygon1": true, "matchRadius": true}
```

As you can see in `test point A` result, the points that match with bound border points is not considered a match point .