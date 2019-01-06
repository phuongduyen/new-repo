### A simple example
[http://map.itrithuc.vn/routing/route?point=20.9090191,106.1449562&point=21.0292095,105.85247](http://map.itrithuc.vn/routing/route?point=20.9090191,106.1449562&point=21.0292095,105.85247)

The URL path of route API [http://map.itrithuc.vn/routing/route](http://map.itrithuc.vn/routing/route)

## Parameters

All official parameters are shown in the following table

Parameter   | Default | Description
:-----------|:--------|:-----------
point       | -       | Specify multiple points for which the route should be calculated. The order is important. Specify at least two points.
locale      | en      | The locale of the resulting turn instructions. E.g. `pt_PT` for Portuguese or `de` for German
instructions| true    | If instruction should be calculated and returned
vehicle     | car     | The vehicle for which the route should be calculated. Other vehicles are foot, bike, motorcycle, hike, ...
elevation   | false   | If `true` a third dimension - the elevation - is included in the polyline or in the GeoJson. IMPORTANT: If enabled you have to use a modified version of the decoding method or set points_encoded to `false`. See the points_encoded attribute for more details. Additionally a request can fail if the vehicle does not support elevation. See the features object for every vehicle.
points_encoded   | true    | If `false` the coordinates in `point` and `snapped_waypoints` are returned as array using the order [lon,lat,elevation] for every point. If `true` the coordinates will be encoded as string leading to less bandwith usage. You'll need a special handling for the decoding of this string on the client-side. We provide open source code in [Java](https://github.com/graphhopper/graphhopper/blob/d70b63660ac5200b03c38ba3406b8f93976628a6/web/src/main/java/com/graphhopper/http/WebHelper.java#L43) and [JavaScript](https://github.com/graphhopper/graphhopper/blob/d70b63660ac5200b03c38ba3406b8f93976628a6/web/src/main/webapp/js/ghrequest.js#L139). It is especially important to use no 3rd party client if you set `elevation=true`!
debug            | false   | If true, the output will be formated.
calc_points      | true    | If the points for the route should be calculated at all printing out only distance and time.
type             | json    | Specifies the resulting format of the route, for `json` the content type will be application/json. Other possible format options: <br> `gpx`, the content type will be application/gpx+xml, see below for more parameters.
point_hint       | -       | Optional parameter. Specifies a hint for each `point` parameter to prefer a certain street for the closest location lookup. E.g. if there is an address or house with two or more neighboring streets you can control for which street the closest location is looked up.
details          | -       | Optional parameter. You can request additional details for the route: `average_speed`, `street_name`, `edge_id`, and `time`. The returned format for one details is `[fromRef, toRef, value]`. The `ref` references the points of the response.

### GPX

Create a GPX output via `type=gpx` and use the following additional parameters

Parameter     | Default | Description
:-------------|:--------|:-----------
gpx.track     |	true    | Include <trk> tag in gpx result. Only applicable if type=gpx is specified.
gpx.route     | true    | Include <rte> tag in gpx result. Only applicable if type=gpx is specified.
gpx.waypoints | false   | Include <wpt> tag in gpx result. Only applicable if type=gpx is specified.

### Hybrid

If you enabled hybrid mode in the config you can use most of the features from flexible mode
and still benefit from a speed up.

Parameter        | Default    | Description
:----------------|:-----------|:-----------
ch.disable       | `false`    | Set to `true` in order to use the hybrid mode for all weightings that are enabled
lm.active_landmarks| 4        | Not recommended to change this

### Flexible

Unlock certain flexible features via `ch.disable=true` per request or disable CH on the server-side in the
configuration file via `prepare.ch.weightings=no`

Parameter        | Default    | Description
:----------------|:-----------|:-----------
ch.disable       | `false`    | Use this parameter in combination with one or more parameters of this table
weighting        | `fastest`  | Which kind of 'best' route calculation you need. Other option is `shortest` (e.g. for `vehicle=foot` or `bike`), `short_fastest` if time and distance is expensive (e.g. for `vehicle=truck`) and `curvature` (only for `vehicle=motorcycle`)
edge_traversal   |`false`     | Use `true` if you want to consider turn restrictions for bike and motor vehicles. Keep in mind that the response time is roughly 2 times slower.
algorithm        |`astarbi`   | The algorithm to calculate the route. Other options are `dijkstra`, `astar`, `astarbi`, `alternative_route` and `round_trip`
block_area       | -          | Block road access via a point with the format `latitude,longitude` or an area defined by a circle `lat,lon,radius` or a rectangle `lat1,lon1,lat2,lon2`. Separate multiple areas with a semicolon `;`.
heading          | NaN        | Favour a heading direction for a certain point. Specify either one heading for the start point or as many as there are points. In this case headings are associated by their order to the specific points. Headings are given as north based clockwise angle between 0 and 360 degree. This parameter also influences the tour generated with `algorithm=round_trip` and forces the initial direction.
heading_penalty  | 120        | Penalty for omitting a specified heading. The penalty corresponds to the accepted time delay in seconds in comparison to the route without a heading.
pass_through     | `false`    | If `true` u-turns are avoided at via-points with regard to the `heading_penalty`.
round_trip.distance                 | 10000 | If `algorithm=round_trip` this parameter configures approximative length of the resulting round trip
round_trip.seed                     | 0     | If `algorithm=round_trip` this parameter introduces randomness if e.g. the first try wasn't good.
alternative_route.max_paths         | 2     | If `algorithm=alternative_route` this parameter sets the number of maximum paths which should be calculated. Increasing can lead to worse alternatives.
alternative_route.max_weight_factor | 1.4   | If `algorithm=alternative_route` this parameter sets the factor by which the alternatives routes can be longer than the optimal route. Increasing can lead to worse alternatives.
alternative_route.max_share_factor  | 0.6   | If `algorithm=alternative_route` this parameter specifies how much alternatives routes can have maximum in common with the optimal route. Increasing can lead to worse alternatives.

## Example output for the case `type=json`

Keep in mind that attributes which are not documented here can be removed in the future - 
you should not rely on them! The JSON result contains the following structure:

JSON path/attribute        | Description
:--------------------------|:------------
paths                      | An array of possible paths
paths[0].distance          | The total distance of the route, in meter
paths[0].time              | The total time of the route, in ms
paths[0].ascend            | The total ascend (uphill) of the route, in meter
paths[0].descend           | The total descend (downhill) of the route, in meter
paths[0].points            | This value contains the coordinates of the path. If `points_encoded=true` or no `points_encoded` specified an encoded string will be returned, otherwise an array with order [lon,lat,elevation] is returned. See the parameter `points_encoded` for more information.
paths[0].points_encoded    | Is true if the points are encoded, if not paths[0].points contains the geo json of the path (then order is lon,lat,elevation), which is easier to handle but consumes more bandwidth compared to encoded version
paths[0].bbox              | The bounding box of the route, format: <br> minLon, minLat, maxLon, maxLat
paths[0].snapped_waypoints | This value contains the snapped input points. If `points_encoded=true` or no `points_encoded` parameter was specified then an encoded string will be returned, otherwise an array is returned. See the parameter `points_encoded` for more information.
paths[0].instructions      | Contains information about the instructions for this route. The last instruction is always the Finish instruction and takes 0ms and 0meter. Keep in mind that instructions are currently under active development and can sometimes contain misleading information, so, make sure you always show an image of the map at the same time when navigating your users!
paths[0].instructions[0].text                 | A description what the user has to do in order to follow the route. The language depends on the locale parameter.
paths[0].instructions[0].street_name          | The name of the street to turn onto in order to follow the route.
paths[0].instructions[0].distance             | The distance for this instruction, in meter
paths[0].instructions[0].time                 | The duration for this instruction, in ms
paths[0].instructions[0].interval             | An array containing the first and the last index (relative to paths[0].points) of the points for this instruction. This is useful to know for which part of the route the instructions are valid.
paths[0].instructions[0].sign                 | A number which specifies the sign to show e.g. 2 for a right turn.<br>KEEP_LEFT=-7<br>TURN_SHARP_LEFT = -3<br>TURN_LEFT = -2<br>TURN_SLIGHT_LEFT = -1<br>CONTINUE_ON_STREET = 0<br>TURN_SLIGHT_RIGHT = 1<br>TURN_RIGHT = 2<br>TURN_SHARP_RIGHT = 3<br>FINISH = 4<br>REACHED_VIA = 5<br>USE_ROUNDABOUT = 6<br>KEEP_RIGHT=7<br>implement some default for all other
paths[0].instructions[0].annotation_text      | [optional] A text describing the instruction in more detail, e.g. like surface of the way, warnings or involved costs
paths[0].instructions[0].annotation_importance| [optional] 0 stands for INFO, 1 for warning, 2 for costs, 3 for costs and warning
paths[0].instructions[0].exit_number          | [optional] Only available for USE_ROUNDABOUT instructions. The count of exits at which the route leaves the roundabout.
paths[0].instructions[0].exited               | [optional] Only available for USE_ROUNDABOUT instructions. True if the roundabout should be exited. False if a via point or end is placed in the roundabout, thus, the roundabout should not be exited due to this instruction.
paths[0].instructions[0].turn_angle           | [optional] Only available for USE_ROUNDABOUT instructions. The radian of the route within the roundabout: `0 < r < 2*PI` for clockwise and `-2PI < r < 0` for counterclockwise transit. `NaN` if the direction of rotation is undefined.

```json
{
   "hints":{
      "visited_nodes.average":"200.0",
      "visited_nodes.sum":"200"
   },
   "info":{
      "copyrights":[
         "GraphHopper",
         "OpenStreetMap contributors"
      ],
      "took":12
   },
   "paths":[
      {
         "distance":41266.905,
         "weight":2270.042776,
         "time":2270012,
         "transfers":0,
         "points_encoded":true,
         "bbox":[
            105.851153,
            20.908935,
            106.154926,
            21.039716
         ],
         "points":"}fj_CgbaeS`AAr@Bt@Nt@^xKsB|AqHySmBqH[uAEaCAwCTBaCmAaE[qAjEaBn@g@DMgGvBoCdAeBf@yEbBa@LSBQAuE{NiBaHqBcHsDuL{FsSPWf@Mb@m@t@wB\\w@dBeDvFgKv@wAhBuDxKgStE{InDqGtA}B`AiDZoAxDuD^m@Zk@d@{AHi@Dg@?_AGm@OmCFgAd@cCpBwG^aCH}@L_NFyCJu@pG_`@r@sCNiATKLW?[CIMOQIQ?oAe@s\\{W{CsAmF}A_Cc@c@_@MOEKGWAg@Tw@nCmH@SNUZu@dK{WpLi\\hKqi@pAiGvGeV~FiQ|C{KdKa\\~FsQPe@rNiWriAoeBlAcC~GiOp@oArAyBzBsCx@{@fFqElBqBxB{CnOqVbCiDt@s@h@c@zk@eYnGaEjFaEjA_AhFcFpDgEhCkDvFsI|QsX|BuBlCwApAy@z@]rDiA|McGhWgK|QgIzH}CvOcGjKgE|DyAlDsBtD_D~AmBhA{B`C_E~K_QjDoG~G_KxD}EzB_ElBaFzEqNj@yAb@eBhPeg@j@yArA{D`BmFfIcVxJ_Zj@y@rCiKfAmI|@uHtBoLdAsKNgFf@wDZcDj@{BjC{HhAoDpBwEjA}CzE{Kx@aCb@_ClBoPr@yGnBeMfKwg@~DyPZ_Ap@cDpAcFdCyMnEq\\hEqU~AiJpAkJdCuS\\uBd@uB^qA|@oCr@cBz@cBnEcHhAoBrDcHtIgRxGiOfB}ElDmMnCkKdEsNzAsJf@wGJaH_@oI{@_Js@{GeLwaAuCoXa@eEgCaVwAsMs@wFu@_IyJqn@{D}ScCeO`MrEpH~DdDhBfCvAhD|Bj@z@b@vA`BxBbATpBKz@[bEiAnBfAtAjAnBrCbFtDnQxMaAfB",
         "instructions":[
            {
               "distance":368.126,
               "heading":174.81,
               "sign":0,
               "interval":[
                  0,
                  5
               ],
               "text":"Tiếp tục theo Lê Thái Tổ",
               "time":26503,
               "street_name":"Lê Thái Tổ"
            },
            {
               "distance":167.484,
               "sign":-2,
               "interval":[
                  5,
                  6
               ],
               "text":"Rẽ trái theo rẽ trái",
               "time":10048,
               "street_name":"Hàng Khay"
            },
            {
               "distance":545.78,
               "sign":-2,
               "interval":[
                  6,
                  8
               ],
               "text":"Rẽ trái theo rẽ trái",
               "time":39293,
               "street_name":"Phố Đinh Tiên Hoàng"
            },
            {
               "distance":205.899,
               "sign":7,
               "interval":[
                  8,
                  11
               ],
               "text":"Keep right theo keep right",
               "time":24707,
               "street_name":"Hàng Dầu"
            },
            {
               "distance":67.112,
               "sign":2,
               "interval":[
                  11,
                  12
               ],
               "text":"Rẽ phải theo rẽ phải",
               "time":4832,
               "street_name":"Hàng Thùng"
            },
            {
               "distance":155.104,
               "sign":-1,
               "interval":[
                  12,
                  14
               ],
               "text":"Rẽ trái nhẹ theo rẽ trái nhẹ",
               "time":11167,
               "street_name":"Hàng Thùng, ĐT1-1"
            },
            {
               "distance":124.373,
               "sign":2,
               "interval":[
                  14,
                  15
               ],
               "text":"Rẽ phải theo rẽ phải",
               "time":6888,
               "street_name":"Trần Quang Khải"
            },
            {
               "distance":42.165,
               "sign":-7,
               "interval":[
                  15,
                  17
               ],
               "text":"Keep left",
               "time":5059,
               "street_name":""
            },
            {
               "distance":308.161,
               "sign":-3,
               "interval":[
                  17,
                  20
               ],
               "text":"Rẽ trái ngay theo rẽ trái ngay",
               "time":17067,
               "street_name":"Trần Quang Khải"
            },
            {
               "distance":173.473,
               "sign":0,
               "interval":[
                  20,
                  24
               ],
               "text":"Tiếp tục theo Trần Quang Khải",
               "time":10360,
               "street_name":"Trần Quang Khải"
            },
            {
               "distance":1233.402,
               "sign":2,
               "interval":[
                  24,
                  29
               ],
               "text":"Rẽ phải theo rẽ phải",
               "time":68310,
               "street_name":"Cầu Chương Dương"
            },
            {
               "distance":39.328,
               "sign":2,
               "interval":[
                  29,
                  31
               ],
               "text":"Rẽ phải",
               "time":2178,
               "street_name":""
            },
            {
               "distance":1525.178,
               "sign":-7,
               "interval":[
                  31,
                  42
               ],
               "text":"Keep left theo keep left",
               "time":84456,
               "street_name":"Đê Xuân Quan - Long Biên, ĐT.195"
            },
            {
               "distance":140.316,
               "sign":-7,
               "interval":[
                  42,
                  44
               ],
               "text":"Keep left",
               "time":8418,
               "street_name":""
            },
            {
               "distance":169.34,
               "sign":7,
               "interval":[
                  44,
                  46
               ],
               "text":"Keep right theo keep right",
               "time":10160,
               "street_name":"Cổ Linh"
            },
            {
               "distance":1634.148,
               "sign":0,
               "interval":[
                  46,
                  63
               ],
               "text":"Tiếp tục theo Cổ Linh",
               "time":98043,
               "street_name":"Cổ Linh"
            },
            {
               "distance":39.992,
               "sign":-7,
               "interval":[
                  63,
                  64
               ],
               "text":"Keep left",
               "time":2399,
               "street_name":""
            },
            {
               "exit_number":3,
               "distance":1111.807,
               "sign":6,
               "exited":true,
               "turn_angle":-4.53,
               "interval":[
                  64,
                  76
               ],
               "text":"Tại vòng xoay, rẽ lối rẽ 3",
               "time":61573,
               "street_name":""
            },
            {
               "distance":31138.265,
               "sign":7,
               "interval":[
                  76,
                  214
               ],
               "text":"Keep right",
               "time":1602531,
               "street_name":""
            },
            {
               "distance":470.491,
               "sign":3,
               "interval":[
                  214,
                  216
               ],
               "text":"Rẽ phải ngay theo rẽ phải ngay",
               "time":33874,
               "street_name":"QL.38"
            },
            {
               "distance":739.153,
               "sign":0,
               "interval":[
                  216,
                  226
               ],
               "text":"Tiếp tục theo Quốc Lộ 38, QL.38",
               "time":38012,
               "street_name":"Quốc Lộ 38, QL.38"
            },
            {
               "distance":391.508,
               "sign":2,
               "interval":[
                  226,
                  230
               ],
               "text":"Rẽ phải theo rẽ phải",
               "time":46980,
               "street_name":"Quốc Lộ 38, QL.38"
            },
            {
               "distance":410.971,
               "sign":7,
               "interval":[
                  230,
                  231
               ],
               "text":"Keep right",
               "time":49315,
               "street_name":""
            },
            {
               "distance":65.33,
               "sign":2,
               "interval":[
                  231,
                  232
               ],
               "text":"Rẽ phải",
               "time":7839,
               "street_name":""
            },
            {
               "distance":0.0,
               "sign":4,
               "last_heading":304.0343244082201,
               "interval":[
                  232,
                  232
               ],
               "text":"Kết thúc!",
               "time":0,
               "street_name":""
            }
         ],
         "legs":[

         ],
         "details":{

         },
         "ascend":0.0,
         "descend":0.0,
         "snapped_waypoints":"}fj_CgbaeS`lVyjx@"
      }
   ]
}
```

## Area information

If you need to find out details about the area or need to ping the service use '/info'

[http://map.itrithuc.vn/routing/info](http://map.itrithuc.vn/routing/info)

### Example output:
```json
{
   "bbox":[
      101.991759,
      8.437479,
      109.430705,
      23.470613
   ],
   "supported_vehicles":[
      "car",
      "foot",
      "bike"
   ],
   "features":{
      "car":{
         "elevation":false
      },
      "foot":{
         "elevation":false
      },
      "bike":{
         "elevation":false
      }
   },
   "version":"0.12",
   "build_date":"2018-12-08T15:14:21Z",
   "import_date":"2018-12-08T15:37:46Z",
   "data_date":"2018-12-07T17:40:30Z",
   "prepare_ch_date":"",
   "prepare_date":""
}
```

JSON path/attribute | Description
:-------------------|:------------
version             | The GraphHopper version
bbox                | The maximum bounding box of the area, format: <br> minLon, minLat, maxLon, maxLat
features            | A json object per supported vehicles with name and supported features like elevation
build_date          | [optional] The GraphHopper build date
import_date         | [optional] The date time at which the OSM import was done
prepare_date        | [optional] The date time at which the preparation (contraction hierarchies) was done. If nothing was done this is empty
supported_vehicles  | [deprecated] An array of strings for all supported vehicles

### Error Output
```json
{
  "message": "Cannot find point 2: 2248.224673, 3.867187",
  "hints": [{"message": "something", ...}]
}
```

Sometimes a point can be "off the road" and you'll get 'cannot find point', this normally does not
indicate a bug in the routing engine and is expected to a certain degree if too far away.

JSON path/attribute    | Description
:----------------------|:------------
message                | Not intended to be displayed to the user as it is not translated
hints                  | An optional list of details regarding the error message e.g. `[{"message": "first error message in hints"}]`


### HTTP Error codes

HTTP error code | Reason
:---------------|:------------
500             | Internal server error. It is strongly recommended to send us the message and the link to it, as it is very likely a bug in our system.
501             | Only a special list of vehicles is supported
400             | Something was wrong in your request

## Isochrone

In addition to routing, the end point to obtain an isochrone is `/isochrone`.

[http://map.itrithuc.vn/routing/isochrone](http://map.itrithuc.vn/routing/isochrone)

All parameters are shown in the following table.

Parameter                   | Default | Description
:---------------------------|:--------|:-----------
vehicle                     | car     | The vehicle for which the route should be calculated. Other vehicles are foot, bike, motorcycle, hike, ...
buckets                     | 1       | Number by which to divide the given `time_limit` to create `buckets` nested isochrones of time intervals `time_limit/buckets`, `time_limit/(buckets - 1)`, ... , `time_limit`. Applies analogously to `distance_limit`.
reverse_flow                | false   | If false the flow goes from point to the polygon, if true the flow goes from the polygon inside to the point. Example usage for false: *How many potential customer can be reached within 30min travel time from your store* vs. true: *How many customers can reach your store within 30min travel time.* (optional, default to false)
point                       |         | Specify the start coordinate (required). A string organized as `latitude,longitude`.
result                      | polygon | Can be "pointlist" or "polygon".
time_limit                  | 600     | Specify which time the vehicle should travel. In seconds. (optional, default to 600)
distance_limit              | -1      | Specify which distance the vehicle should travel. In meter. (optional, default to -1)
