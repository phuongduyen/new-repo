# Reverse Geocoding

Reverse geocoding generates an address from a latitude and longitude or from
an OSM object.

## Parameters

The main format of the reverse API is

```
http://map.itrithuc.vn/geocode/reverse?<query>
```

There are two ways how the requested location can be specified:

* `lat=<value>` `lon=<value>`

    A geographic location to generate an address for. The coordiantes must be
    in WGS84 format.

* `osm_type=[N|W|R]` `osm_id=<value>`

    A specific OSM node(N), way(W) or relation(R) to return an address for.

In both cases exactly one object is returned. The two input paramters cannot
be used at the same time. Both accept the additional optional parameters listed
below.

### Output format

* `format=[xml|json|jsonv2|geojson|geocodejson]`

See [Place Output Formats](Output.md) for details on each format. (Default: html)

* `json_callback=<string>`

Wrap json output in a callback function ([JSONP](https://en.wikipedia.org/wiki/JSONP)) i.e. `<string>(<json>)`.
Only has an effect for JSON output formats.

### Output details

* `addressdetails=[0|1]`

Include a breakdown of the address into elements. (Default: 1)


* `extratags=[0|1]`

Include additional information in the result if available,
e.g. wikipedia link, opening hours. (Default: 0)


* `namedetails=[0|1]`

Include a list of alternative names in the results. These may include
language variants, references, operator and brand. (Default: 0)


### Language of results

* `accept-language=<browser language string>`

Preferred language order for showing search results, overrides the value
specified in the "Accept-Language" HTTP header.
Either use a standard RFC2616 accept-language string or a simple
comma-separated list of language codes.

### Result limitation

* `zoom=[0-18]`

Level of detail required for the address. Default: 18. This is a number that corresponds
roughly to the zoom level used in map frameworks like Leaflet.js, Openlayers etc.
In terms of address details the zoom levels are as follows:

 zoom | address detail
 -----|---------------
  3   | country
  5   | state
  8   | county
  10  | city
  14  | suburb
  16  | street
  18  | building


### Polygon output

* `polygon_geojson=1`
* `polygon_kml=1`
* `polygon_svg=1`
* `polygon_text=1`

Output geometry of results as a GeoJSON, KML, SVG or WKT. Only one of these
options can be used at a time. (Default: 0)

* `polygon_threshold=0.0`

Simplify the output geometry before returning. The parameter is the
tolerance in degrees with which the geometry may differ from the original
geometry. Topology is preserved in the result. (Default: 0.0)

### Other

* `email=<valid email address>`

If you are making large numbers of request please include an appropriate email
address to identify your requests. See Nominatim's [Usage Policy](https://operations.osmfoundation.org/policies/nominatim/) for more details.


* `debug=[0|1]`

Output assorted developer debug information. Data on internals of Nominatim's
"Search Loop" logic, and SQL queries. The output is (rough) HTML format.
This overrides the specified machine readable format. (Default: 0)


## Examples

* [http://map.itrithuc.vn/geocode/reverse.php?lat=20.981956742832327&lon=105.71044921875001&format=xml&addressdetails=1](http://map.itrithuc.vn/geocode/reverse.php?lat=20.981956742832327&lon=105.71044921875001&format=xml&addressdetails=1)

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

##### Example with `format=jsonv2`

* [http://map.itrithuc.vn/geocode/reverse.php?lat=20.981956742832327&lon=105.71044921875001&format=jsonv2&addressdetails=1](http://map.itrithuc.vn/geocode/reverse.php?lat=20.981956742832327&lon=105.71044921875001&format=jsonv2&addressdetails=1)

```json
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
}
```
