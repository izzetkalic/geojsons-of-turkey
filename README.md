*[English](README.md), [Türkçe](README.tr.md)*

# Administrative Borders of Turkey

This project has been created to resolve the lack of geographic coordinates of Turkey that can be used in data analysis and put together the data in a single source. Data has been collected from [OpenStreetMap](https://www.openstreetmap.org/) with [overpass-turbo](http://overpass-api.de/) and cleanised with [Pentaho Data Integration](https://www.hitachivantara.com/en-us/products/data-management-analytics/pentaho-platform/pentaho-data-integration.html). You can find previously created repositories in the below;

* [borders-of-turkey](https://github.com/uyasarkocal/borders-of-turkey)
* [Turkey-Maps-GeoJSON](https://github.com/alpers/Turkey-Maps-GeoJSON)
* [tr-geojson](https://github.com/cihadturhan/tr-geojson)

## Data Acquisition

### OpenStreetMap - Overpass Api

[Overpass Turbo](http://overpass-turbo.eu/) is a tool to make it easy to query of OSM data provided by OpenStreetMap through the browser. It has an [API](https://wiki.openstreetmap.org/wiki/Overpass_API) for developers and [there](https://github.com/mvexel/overpass-api-python-wrapper) is a project in order to use in Python. 


According to [OpenStreetMap](https://wiki.openstreetmap.org/wiki/Key:admin_level) documentation `admin_level` described as;

> The admin_level key describes the administrative level of an object within a government hierarchy. A lower level means higher in the hierarchy.

The description of administrative borders of Turkey are;

* admin_level=2 : Country border
* admin_level=4 : Province border
* admin_level=6 : Town border
* admin_level=8 : Neighbourhood border

**The data are very limited for [admin_level=8](geojsons/turkey-admin-level-8.geojson). [Google Maps API](https://developers.google.com/maps/documentation?hl=tr) or [Yandex Map API](https://tech.yandex.com.tr/maps/) can be used to complete the data.**

In order to collect layers with these border codes the below code should be run.
```
[out:json];
(area["name"="Türkiye"] -> .a;);
(rel(area)["admin_level"="<CHANGEIT>"];);
out geom;
```

In order to see all layers together, the below code should be run.
```
[out:json];
(area["name"="Türkiye"] -> .a;);
(rel(area)["type"="boundary"]["boundary"="administrative"];);
out geom;
```

According to [Overpass Turbo](https://wiki.openstreetmap.org/wiki/ES:Overpass_turbo/GeoJSON) documentation **osmtogeojson** library is used to convert raw OSM data to `geojson`. The data in `geojsons` folder are created with this library.

> Overpass turbo uses the [osmtogeojson](https://github.com/tyrasd/osmtogeojson) library to convert the raw OSM data output it gets from the Overpass API to GeoJSON.

### TKGM (General Directorate of Land Registry and Cadastre)

[Parsel Sorgu](https://parselsorgu.tkgm.gov.tr/) can be used to collect geographic coordinates. `tkgm_postman_collection.json` has been created to query this api. Import it into [Postman](https://www.postman.com/) and run it hierarchically. But [neighbourhood](geojsons/istanbul-neighbourhood-tkgm.geojson) level data is not as good as OpenStreetMap data and query limit is very low but you know the [trick](http://spys.me/proxy.txt).

### Pentaho and Flows

In order to use flows download [Pentaho PDI](https://sourceforge.net/projects/pentaho/files/Pentaho%209.0/) and open `.ktr` files where you can find in the `pentaho-flows` folder. Please, do not hesitate to open an issue or send email to me if you have any problem during installation. It can also be created these flows with any other **ETL** tool.

* **GeoJSON from TKGM.ktr**: It is created to automate HTTP queries in the `tkgm_postman_collection.json` file. It contains city codes of Istanbul in the format that the TKGM needs. In the end of this flow *geojson* file wil be produced.

* **Remove Admin Center.ktr**: It is created to remove center points of `.geojson` files. The difference can be seen below;

    * [Neighborhood borders of İstanbul with center points](geojsons/istanbul-admin-level-8.geojson)
    * [Neighborhood borders of İstanbul without center points](geojsons/istanbul-admin-level-8-without-centers.geojson)

## Examples

* A heatmap can be created using `R` [library](https://www.r-graph-gallery.com/327-chloropleth-map-from-geojson-with-ggplot2.html) with these geographic coordinates.

<center><img src="images/heatmap.png" width="500"></center>

* Bus routes can be mapped using the coordinates provided by [İstanbul Metropolitan Municipality](https://data.ibb.gov.tr/). You can find the project [here](https://medium.com/swlh/visualizing-istanbul-bus-traffic-with-python-and-keplergl-a84895788825) which uses [KeplerGL](https://kepler.gl/).

<center><img src="images/way.png" width="500"></center>