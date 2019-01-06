# Place details

Lookup details about a single place by id. The default output is HTML for debugging search logic and results.

## Parameters

The details API supports the following two request formats:

```
  http://map.itrithuc.vn/geocode/details?osmtype=[N|W|R]&osmid=<value>&class=<value>
```

`osmtype` and `osmid` are required parameter. The type is one of node (N), way (W)
or relation (R). The id must be a number. The `class` parameter is optional and
allows to distinguish between entries, when the corresponding OSM object has more
than one main tag. For example, when a place is tagged with `tourism=hotel` and
`amenity=restaurant`, there will be two place entries in Nominatim, one for a
restaurant, one for a hotel. You need to specify `class=tourism` or `class=amentity`
to get exactly the one you want. If there are multiple places in the database
but the `class` parameter is left out, then one of the places will be chosen
at random and displayed.

```
  http://map.itrithuc.vn/geocode/details?placeid=<value>
```

Placeids are assigned sequentially during Nominatim data import. The id for a place is different between Nominatim installation (servers) and changes when data gets reimported. Therefore it can't be used as permanent id and shouldn't be used in bug reports.


Additional optional parameters are explained below.

### Output format

* `format=[html|json]`

See [Place Output Formats](Output.md) for details on each format. (Default: html)

* `json_callback=<string>`

Wrap json output in a callback function (JSONP) i.e. `<string>(<json>)`.
Only has an effect for JSON output formats.

* `pretty=[0|1]`

For JSON output will add indentation to make it more human-readable. (Default: 0)


### Output details

* `addressdetails=[0|1]`

Include a breakdown of the address into elements. (Default for JSON: 0, for HTML: 1)

* `keywords=[0|1]`

Include a list of name keywords and address keywords (word ids). (Default: 0)

* `linkedplaces=[0|1]`

Include details of places higher in the address hierarchy. E.g. for a street this is usually the city, state, postal code, country. (Default: 1)

* `hierarchy=[0|1]`

Include details of places lower in the address hierarchy. E.g. for a city this usually a list of streets, suburbs, rivers. (Default for JSON: 0, for HTML: 1)

* `group_hierarchy=[0|1]`

For JSON output will group the places by type. (Default: 0)

* `polygon_geojson=[0|1]`

Include geometry of result. (Default for JSON: 0, for HTML: 1)

### Language of results

* `accept-language=<browser language string>`

Preferred language order for showing result, overrides the value
specified in the "Accept-Language" HTTP header.
Either use a standard RFC2616 accept-language string or a simple
comma-separated list of language codes.


## Examples

##### HTML

[http://map.itrithuc.vn/geocode/details.php?osmtype=N&osmid=405440](http://map.itrithuc.vn/geocode/details.php?osmtype=N&osmid=405440)

##### JSON

[http://map.itrithuc.vn/geocode/details.php?osmtype=N&osmid=405440&format=json](http://map.itrithuc.vn/geocode/details.php?osmtype=N&osmid=405440&format=json)


```json
{
   "place_id":2225402,
   "parent_place_id":245404,
   "osm_type":"N",
   "osm_id":405440,
   "category":"amenity",
   "type":"school",
   "admin_level":"15",
   "localname":"phòng giáo dục bảo lộc",
   "names":{
      "name":"phòng giáo dục bảo lộc"
   },
   "addresstags":{
      "district":"Thành phố Bảo Lộc",
      "full":"01 đinh tiên hoàng",
      "housenumber":"01",
      "province":"Lâm Đồng",
      "street":"đinh tiên hoàng",
      "subdistrict":"Phường 2"
   },
   "housenumber":"01",
   "calculated_postcode":null,
   "country_code":"vn",
   "indexed_date":"2018-12-13T17:12:17+00:00",
   "importance":0,
   "calculated_importance":0,
   "extratags":{
      "description":""
   },
   "calculated_wikipedia":null,
   "icon":"\/nominatim\/images\/mapicons\/education_school.n.32.png",
   "rank_address":30,
   "rank_search":30,
   "isarea":false,
   "centroid":{
      "type":"Point",
      "coordinates":[
         107.808866,
         11.5526609
      ]
   },
   "geometry":{
      "type":"Point",
      "coordinates":[
         107.808866,
         11.5526609
      ]
   }
}
```
