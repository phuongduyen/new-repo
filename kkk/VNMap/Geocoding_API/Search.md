# Search queries

The search API allows to look up a location from a textual description.
Nominatim supports structured as well as free-form search queries.

## Parameters

The search API has the following two formats:

```
   http://map.itrithuc.vn/geocode/search/<query>?<params>
```

This format only accepts a free-form query string where the
parts of the query are separated by slashes.

```
   http://map.itrithuc.vn/geocode/search?<params>
```

In this form, the query may be given through two different sets of parameters:

* `q=<query>`

    Free-form query string to search for.
    Free-form queries are processed first left-to-right and then right-to-left if that fails. So you may search for
    [cau giay, ha noi](//map.itrithuc.vn/nominatim/search?q=cau+giay, ha+noi) as well as for
    [ha noi, cau giay](//map.itrithuc.vn/nominatim/search?q=ha+noi,cau+giay).
    Commas are optional, but improve performance by reducing the complexity of the search.


* `street=<housenumber> <streetname>`
* `city=<city>`
* `county=<county>`
* `state=<state>`
* `country=<country>`
* `postalcode=<postalcode>`

    Alternative query string format split into several parameters for structured requests.
    Structured requests are faster but are less robust against alternative
    OSM tagging schemas. **Do not combine with** `q=<query>` **parameter**.

All three query forms accept the additional paramters listed below.

### Output format

* `format=[html|xml|json|jsonv2|geojson|geocodejson]`

See [Place Output Formats](Output.md) for details on each format. (Default: html)

### Output details

* `addressdetails=[0|1]`

Include a breakdown of the address into elements. (Default: 0)


* `extratags=[0|1]`

Include additional information in the result if available,
e.g. wikipedia link, opening hours. (Default: 0)


* `namedetails=[0|1]`

Include a list of alternative names in the results. These may include
language variants, references, operator and brand. (Default: 0)


### Language of results

* `accept-language=<browser language string>`

Preferred language order for showing search results, overrides the value
specified in the ["Accept-Language" HTTP header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Language).
Either use a standard RFC2616 accept-language string or a simple
comma-separated list of language codes.

### Result limitation

* `countrycodes=<countrycode>[,<countrycode>][,<countrycode>]...`

Limit search results to one or more countries. `<countrycode>` must be the
ISO 3166-1alpha2 code, e.g. `gb` for the United Kingdom, `de` for Germany.


* `exclude_place_ids=<place_id,[place_id],[place_id]`

If you do not want certain OSM objects to appear in the search
result, give a comma separated list of the `place_id`s you want to skip.
This can be used to broaden search results. For example, if a previous
query only returned a few results, then including those here would cause
the search to return other, less accurate, matches (if possible).


* `limit=<integer>`

Limit the number of returned results. (Default: 10, Maximum: 50)


* `viewbox=<x1>,<y1>,<x2>,<y2>`

The preferred area to find search results. Any two corner points of the box
are accepted in any order as long as they span a real box.


* `bounded=[0|1]`

When a viewbox is given, restrict the result to items contained with that
viewbox (see above). When `viewbox` and `bounded=1` are given, an amenity
only search is allowed. In this case, give the special keyword for the
amenity in square brackets, e.g. `[pub]`. (Default: 0)


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

* `dedupe=[0|1]`

Sometimes you have several objects in OSM identifying the same place or
object in reality. The simplest case is a street being split in many
different OSM ways due to different characteristics. Nominatim will
attempt to detect such duplicates and only return one match unless
this parameter is set to 0. (Default: 1)



* `debug=[0|1]`

Output assorted developer debug information. Data on internals of Nominatim's
"Search Loop" logic, and SQL queries. The output is (rough) HTML format.
This overrides the specified machine readable format. (Default: 0)



## Examples


##### XML with polygon points

* [http://map.itrithuc.vn/geocode/search?q=ha+noi,cau+giay&format=xml&polygon=1&addressdetails=1](http://map.itrithuc.vn/geocode/search?q=ha+noi,cau+giay&format=xml&polygon=1&addressdetails=1)
* [http://map.itrithuc.vn/geocode/search/vn/ha%20noi/cau%20giay?format=xml&polygon=1&addressdetails=1](http://map.itrithuc.vn/geocode/search/vn/ha%20noi/cau%20giay?format=xml&polygon=1&addressdetails=1)
* [http://map.itrithuc.vn/geocode/search/cau%20giay,%20ha%20noi?format=xml&polygon=1&addressdetails=1](http://map.itrithuc.vn/geocode/search/cau%20giay,%20ha%20noi?format=xml&polygon=1&addressdetails=1)

```xml
<?xml version="1.0" encoding="UTF-8"?>
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

##### JSON with SVG polygon and address details

[http://map.itrithuc.vn/geocode/search/cau%20giay,%20ha%20noi?format=json&addressdetails=1&polygon_svg=1](http://map.itrithuc.vn/geocode/search/cau%20giay,%20ha%20noi?format=json&addressdetails=1&polygon_svg=1)

```json
[
   {
      "place_id":"154538",
      "licence":"Data © OpenStreetMap contributors, ODbL 1.0. https:\/\/osm.org\/copyright",
      "osm_type":"node",
      "osm_id":"4965075429",
      "boundingbox":[
         "21.0128849",
         "21.0528849",
         "105.7714641",
         "105.8114641"
      ],
      "lat":"21.0328849",
      "lon":"105.7914641",
      "display_name":"Cầu Giấy, Hà Nội, 122000, Việt Nam",
      "class":"place",
      "type":"suburb",
      "importance":0.25,
      "icon":"\/nominatim\/images\/mapicons\/poi_place_village.p.20.png",
      "address":{
         "suburb":"Cầu Giấy",
         "city":"Hà Nội",
         "postcode":"122000",
         "country":"Việt Nam",
         "country_code":"vn"
      },
      "svg":"cx=\"105.791464099999999\" cy=\"-21.032884899999999\""
   }
]
```