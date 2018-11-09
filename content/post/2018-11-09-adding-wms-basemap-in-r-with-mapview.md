---
title: Adding WMS basemap in R with mapview
tags: ['WMS','mapview','R','leaflet','sf']
categories: ['R']
date: 2018-11-09
---

Recently I wanted to be able to add a custom basemap of [NHDPlus](https://www.epa.gov/waterdata/nhdplus-national-hydrography-dataset-plus) features to [mapview](https://github.com/r-spatial/mapview) [leaflet](https://rstudio.github.io/leaflet/) maps. It's not straitforward out of the box in `mapview`, but I found the helpful tips to do it [here](https://atriplex.info/blog/index.php/2017/06/08/adding-wms-basemaps-to-a-mapview-map/) and [here](https://github.com/r-spatial/mapview/issues/39#issuecomment-271252820).

Below I add the WMS service from [EPA Waters](https://watersgeo.epa.gov/arcgis/services/NHDPlus_NP21/NHDSnapshot_NP21/MapServer/WmsServer?) along with USGS StreamGage stations in Benton County, OR using the [dataRetrieval package](https://github.com/USGS-R/dataRetrieval).

```r
library(dataRetrieval)
library(mapview)
library(sf)
library(leaflet)
Stations <- readNWISdata(stateCd="Oregon", countyCd="Benton")
# DataRetrival returns objects as 'attributes' - things like the url used, site metadata, site info, etc - just use attributes(Durham_Stations) to examine
siteInfo <- attr(Stations , "siteInfo")
stations_sf = st_as_sf(siteInfo, coords = c("dec_lon_va", "dec_lat_va"), crs = 4269,agr = "constant")

m <- mapview(stations_sf)
m@map = m@map %>% addWMSTiles(group = 'NHDPlus',
                              "https://watersgeo.epa.gov/arcgis/services/NHDPlus_NP21/NHDSnapshot_NP21/MapServer/WmsServer?",
                              layers  = 4,
                              options = WMSTileOptions(format = "image/png", transparent = TRUE),
                              attribution = "") %>% mapview:::mapViewLayersControl(names = c("NHDPlus"))
m
```
