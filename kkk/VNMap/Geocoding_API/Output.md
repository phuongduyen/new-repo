# Place Output

The [\reverse](Reverse.md) and [\search](Search.md)
API calls produce very similar output which is explained in this section.
There is one section for each format which is selectable via the `format`
parameter.

## Formats

### JSON

The JSON format returns an array of places (for search) or
a single place (for reverse) of the following format:

```
  {
   "place_id":"412760",
   "licence":"Data © OpenStreetMap contributors, ODbL 1.0. https:\/\/osm.org\/copyright",
   "osm_type":"way",
   "osm_id":"601883997",
   "lat":"20.9819550153573",
   "lon":"105.710451278915",
   "place_rank":"26",
   "category":"highway",
   "type":"residential",
   "importance":"0.1",
   "addresstype":"road",
   "name":null,
   "display_name":"Hoài Đức, Huyện Hoài Đức, Hà Nội, Việt Nam",
   "address":{
      "town":"Hoài Đức",
      "county":"Huyện Hoài Đức",
      "city":"Hà Nội",
      "country":"Việt Nam",
      "country_code":"vn"
   },
   "boundingbox":[
      "20.9809777",
      "20.9820774",
      "105.7091447",
      "105.7105539"
   ]
  },
```

The possible fields are:

 * `place_id` - reference to the Nominatim internal database ID (see notes below)
 * `osm_type`, `osm_id` - reference to the OSM object
 * `boundingbox` - area of corner coordinates
 * `lat`, `lon` - latitude and longitude of the centroid of the object
 * `display_name` - full comma-separated address
 * `class`, `type` - key and value of the main OSM tag
 * `importance` - computed importance rank
 * `icon` - link to class icon (if available)
 * `address` - dictionary of address details (only with `addressdetails=1`)
 * `extratags` - dictionary with additional useful tags like website or maxspeed
   (only with `extratags=1`)
 * `namedetails` - dictionary with full list of available names including ref etc.
 * `geojson`, `svg`, `geotext`, `geokml` - full geometry
   (only with the appropriate `polygon_*` parameter)

### JSONv2

This is the same as the JSON format with two changes:

 * `class` renamed to `category`
 * additional field `place_rank` with the search rank of the object

### GeoJSON

This format follows the [RFC7946](http://geojson.org). Every feature includes
a bounding box (`bbox`).

The feature list has the following fields:

 * `place_id` - reference to the Nominatim internal database ID (see notes below)
 * `osm_type`, `osm_id` - reference to the OSM object
 * `category`, `type` - key and value of the main OSM tag
 * `display_name` - full comma-separated address
 * `place_rank` - class search rank
 * `importance` - computed importance rank
 * `icon` - link to class icon (if available)
 * `address` - dictionary of address details (only with `addressdetails=1`)
 * `extratags` - dictionary with additional useful tags like website or maxspeed
   (only with `extratags=1`)
 * `namedetails` - dictionary with full list of available names including ref etc.

Use `polygon_geojson` to output the full geometry of the object instead
of the centroid.

### GeocodeJSON

The GeocodeJSON format follows the
[GeocodeJSON spec 0.1.0](https://github.com/geocoders/geocodejson-spec).
The following feature attributes are implemented:

 * `osm_type`, `osm_id` - reference to the OSM object (unofficial extension)
 * `type` - value of the main tag of the object (e.g. residential, restaurant, ...)
 * `label` - full comma-separated address
 * `name` - localised name of the place
 * `housenumber`, `street`, `locality`, `postcode`, `city`,
   `district`, `county`, `state`, `country` -
   provided when it can be determined from the address
   (see [this issue](https://github.com/openstreetmap/Nominatim/issues/1080) for
   current limitations on the correctness of the address) and `addressdetails=1`
   was given
 * `admin` - list of localised names of administrative boundaries (only with `addressdetails=1`)

Use `polygon_geojson` to output the full geometry of the object instead
of the centroid.

### XML

The XML response returns one or more place objects in slightly different
formats depending on the API call.

#### Reverse

```
<reversegeocode timestamp="Sat, 15 Dec 18 14:13:11 +0700" attribution="Data © OpenStreetMap contributors, ODbL 1.0. http://www.openstreetmap.org/copyright" querystring="lat=20.981956742832327&amp;lon=105.71044921875001&amp;format=xml&amp;addressdetails=1">
    <result place_id="412760" osm_type="way" osm_id="601883997" lat="20.9819550153573" lon="105.710451278915" boundingbox="20.9809777,20.9820774,105.7091447,105.7105539">Hoài Đức, Huyện Hoài Đức, Hà Nội, Việt Nam</result>
    <addressparts>
        <town>Hoài Đức</town>
        <county>Huyện Hoài Đức</county>
        <city>Hà Nội</city>
        <country>Việt Nam</country>
        <country_code>vn</country_code>
    </addressparts>
</reversegeocode>
```

The attributes of the outer `reversegeocode` element return generic information
about the query, including the time when the response was sent (in UTC),
attribution to OSM and the original querystring.

The place information can be found in the `result` element. The attributes of that element contain:

 * `place_id` - reference to the Nominatim internal database ID (see notes below)
 * `osm_type`, `osm_id` - reference to the OSM object
 * `ref` - content of `ref` tag if it exists
 * `lat`, `lon` - latitude and longitude of the centroid of the object
 * `boundingbox` - comma-separated list of corner coordinates

The full address address of the result can be found in the content of the
`result` element as a comma-separated list.

Additional information requested with `addressdetails=1`, `extratags=1` and
`namedetails=1` can be found in extra elements.

#### Search

```
<searchresults timestamp="Sat, 15 Dec 18 14:19:04 +0700" attribution="Data © OpenStreetMap contributors, ODbL 1.0. http://www.openstreetmap.org/copyright" querystring="cau giay, ha noi" polygon="true" exclude_place_ids="154538" more_url="/nominatim/search.php?q=cau+giay%2C+ha+noi&amp;addressdetails=1&amp;polygon=1&amp;exclude_place_ids=154538&amp;format=xml">
    <place place_id="154538" osm_type="node" osm_id="4965075429" place_rank="20" boundingbox="21.0128849,21.0528849,105.7714641,105.8114641" polygonpoints="[[105.7914641,21.0528849],[105.79271991039,21.052845434569],[105.79397076467,21.052727194026],[105.79521172629,21.052530645015],[105.79643789774,21.052256563223],[105.79764443989,21.051906030326],[105.79882659105,21.051480429718],[105.79997968583,21.050981441049],[105.80109917348,21.050411033601],[105.8021806359,21.04977145851],[105.80321980505,21.049065239887],[105.80421257979,21.048295164856],[105.80515504212,21.047464272548],[105.80604347255,21.046575842119],[105.80687436486,21.045633379795],[105.80764443989,21.044640605046],[105.80835065851,21.0436014359],[105.8089902336,21.042519973482],[105.80956064105,21.041400485831],[105.81005962972,21.040247391054],[105.81048523033,21.039065239887],[105.81083576322,21.037858697743],[105.81110984501,21.036632526292],[105.81130639403,21.035391564671],[105.81142463457,21.034140710391],[105.8114641,21.0328849],[105.81142463457,21.031629089609],[105.81130639403,21.030378235329],[105.81110984501,21.029137273708],[105.81083576322,21.027911102257],[105.81048523033,21.026704560112],[105.81005962972,21.025522408946],[105.80956064105,21.024369314169],[105.8089902336,21.023249826518],[105.80835065851,21.0221683641],[105.80764443989,21.021129194954],[105.80687436486,21.020136420205],[105.80604347255,21.019193957881],[105.80515504212,21.018305527452],[105.80421257979,21.017474635144],[105.80321980505,21.016704560113],[105.8021806359,21.01599834149],[105.80109917348,21.015358766399],[105.79997968583,21.014788358951],[105.79882659105,21.014289370282],[105.79764443989,21.013863769674],[105.79643789774,21.013513236777],[105.79521172629,21.013239154985],[105.79397076467,21.013042605974],[105.79271991039,21.012924365431],[105.7914641,21.0128849],[105.79020828961,21.012924365431],[105.78895743533,21.013042605974],[105.78771647371,21.013239154985],[105.78649030226,21.013513236777],[105.78528376011,21.013863769674],[105.78410160895,21.014289370282],[105.78294851417,21.014788358951],[105.78182902652,21.015358766399],[105.7807475641,21.01599834149],[105.77970839495,21.016704560113],[105.77871562021,21.017474635144],[105.77777315788,21.018305527452],[105.77688472745,21.019193957881],[105.77605383514,21.020136420205],[105.77528376011,21.021129194954],[105.77457754149,21.0221683641],[105.7739379664,21.023249826518],[105.77336755895,21.024369314169],[105.77286857028,21.025522408946],[105.77244296967,21.026704560112],[105.77209243678,21.027911102257],[105.77181835499,21.029137273708],[105.77162180597,21.030378235329],[105.77150356543,21.031629089609],[105.7714641,21.0328849],[105.77150356543,21.034140710391],[105.77162180597,21.035391564671],[105.77181835499,21.036632526292],[105.77209243678,21.037858697743],[105.77244296967,21.039065239887],[105.77286857028,21.040247391054],[105.77336755895,21.041400485831],[105.7739379664,21.042519973482],[105.77457754149,21.0436014359],[105.77528376011,21.044640605046],[105.77605383514,21.045633379795],[105.77688472745,21.046575842119],[105.77777315788,21.047464272548],[105.77871562021,21.048295164856],[105.77970839495,21.049065239887],[105.7807475641,21.04977145851],[105.78182902652,21.050411033601],[105.78294851417,21.050981441049],[105.78410160895,21.051480429718],[105.78528376011,21.051906030326],[105.78649030226,21.052256563223],[105.78771647371,21.052530645015],[105.78895743533,21.052727194026],[105.79020828961,21.052845434569],[105.7914641,21.0528849]]" lat="21.0328849" lon="105.7914641" display_name="Cầu Giấy, Hà Nội, 122000, Việt Nam" class="place" type="suburb" importance="0.25" icon="/nominatim/images/mapicons/poi_place_village.p.20.png">
        <suburb>Cầu Giấy</suburb>
        <city>Hà Nội</city>
        <postcode>122000</postcode>
        <country>Việt Nam</country>
        <country_code>vn</country_code>
    </place>
</searchresults>
```

The attributes of the outer `searchresults` element return
generic information about the query:

 * `timestamp` - UTC time when the response was sent
 * `attribution` - OSM licensing information
 * `querystring` - original query
 * `polygon` - true when extra geometry information was requested
 * `exclude_place_ids` - IDs of places that should be ignored in a follow-up request
 * `more_url` - search call that will yield additional results for the query
   just sent

The place information can be found in the `place` elements, of which there may
be more than one. The attributes of that element contain:

 * `place_id` - reference to the Nominatim internal database ID (see notes below)
 * `osm_type`, `osm_id` - reference to the OSM object
 * `ref` - content of `ref` tag if it exists
 * `lat`, `lon` - latitude and longitude of the centroid of the object
 * `boundingbox` - comma-separated list of corner coordinates
 * `place_rank` - class search rank
 * `display_name` - full comma-separated address
 * `class`, `type` - key and value of the main OSM tag
 * `importance` - computed importance rank
 * `icon` - link to class icon (if available)

When `addressdetails=1` is requested, the localised address parts appear
as subelements with the type of the address part.

Additional information requested with `extratags=1` and `namedetails=1` can
be found in extra elements as sub-element of each place.


## Notes on field values

### place_id is not a persistent id

The `place_id` is created when a Nominatim database gets installed. A
single place will have a different value on another server or even when
the same data gets re-imported. It's thus not useful to treat it as
permanent for later use.

The combination `osm_type`+`osm_id` is slighly better but remember in
OpenStreetMap mappers can delete, split, recreate places (and those
get a new `osm_id`), there is no link between those old and new id.
Places can also change their meaning without changing their `osm_id`,
e.g. when a restaurant is retagged as supermarket. For a more in-depth
discussion see [Permanent ID](https://wiki.openstreetmap.org/wiki/Permanent_ID).

Nominatim merges some places (e.g. center node of a city with the boundary
relation) so `osm_type`+`osm_id`+`class_name` would be more unique.

### boundingbox

Comma separated list of min latitude, max latitude, min longitude, max longitude.
The whole planet would be `-90,90,-180,180`.
