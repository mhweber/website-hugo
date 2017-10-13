---
title: Playing with Python Rasterstats
author: Marc Weber
date: '2017-10-13'
slug: playing-with-python-rasterstats
categories:
  - Python
tags:
  - Python
  - rasterstats
  - zonal statistics
subtitle: ''
---

This is code to get PctAg and PctUrb using NLCD 2006 for US Counties. First we'll import some libraries.


```python
import pandas as pd
import numpy as np
import geopandas as gp
```

Bring in US Counties using US Census counties shapefile


```python
counties = gp.GeoDataFrame.from_file('L:/Priv/CORFiles/Geospatial_Library/Data/RESOURCE/POLITICAL/BOUNDARIES/NATIONAL/Counties_Census_2010.shp')
list(counties)
counties.STATE_NAME.unique()
counties = counties[(counties.STATE_NAME != 'Hawaii') & (counties.STATE_NAME != 'Alaska')]
counties = counties[['FIPS','NAME','geometry']]
counties.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>FIPS</th>
      <th>NAME</th>
      <th>geometry</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>27077</td>
      <td>Lake of the Woods</td>
      <td>POLYGON ((-95.34283127277658 48.546679319076, ...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>53019</td>
      <td>Ferry</td>
      <td>POLYGON ((-118.8516288013387 47.94956368481996...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>53065</td>
      <td>Stevens</td>
      <td>POLYGON ((-117.438831576286 48.04411548512263,...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>53047</td>
      <td>Okanogan</td>
      <td>POLYGON ((-118.972093862835 47.93915200536639,...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>53051</td>
      <td>Pend Oreille</td>
      <td>POLYGON ((-117.4385804303028 48.99991850672649...</td>
    </tr>
  </tbody>
</table>
</div>




```python
counties.crs
```




    {'init': u'epsg:4326'}




```python
import rasterio
test = rasterio.open(nlcd)
test.meta
```




    {'count': 1,
     'crs': CRS({'init': u'epsg:5070'}),
     'driver': u'GTiff',
     'dtype': 'uint8',
     'height': 114503,
     'nodata': 255.0,
     'transform': Affine(30.0, 0.0, -2470965.0,
           0.0, -30.0, 3621375.0),
     'width': 163008}



Put counties in same projection as nlcd raster


```python
counties = counties.to_crs(epsg=5070) 
```


```python
from rasterstats import raster_stats 
nlcd = 'L:/Priv/CORFiles/Geospatial_Library/Data/Project/StreamCat/LandscapeRasters/QAComplete/nlcd2006.tif'
county_stats = zonal_stats(counties, nlcd, stats="count majority minority unique", geojson_out=True, 
                            nodata_value=0, categorical=True)
```

Check some results


```python
county_stats[0]['properties']
```




    {11: 1362310L,
     21: 42144L,
     22: 5143L,
     23: 731L,
     24: 250L,
     31: 1483L,
     41: 76356L,
     42: 38248L,
     43: 109L,
     52: 20046L,
     71: 67010L,
     81: 132403L,
     82: 183559L,
     90: 2166970L,
     95: 988350L,
     u'FIPS': u'27077',
     u'NAME': u'Lake of the Woods',
     'count': 5085112,
     'majority': 90.0,
     'minority': 43.0,
     'unique': 15}




```python
[x['properties'] for x in county_stats if x['properties']['NAME'] == "Multnomah"]
```




    [{11: 55949L,
      21: 61237L,
      22: 161213L,
      23: 165285L,
      24: 80374L,
      31: 12215L,
      41: 30543L,
      42: 465915L,
      43: 90782L,
      52: 65540L,
      71: 22643L,
      81: 41366L,
      82: 78362L,
      90: 31820L,
      95: 22234L,
      u'FIPS': u'41051',
      u'NAME': u'Multnomah',
      'count': 1385478,
      'majority': 42.0,
      'minority': 31.0,
      'unique': 15}]



Now we need to pull out %Ag an %Urb for each county - pull out into lists and then stack into a pandas data frame to write out


```python
FIPS = [x['properties']['FIPS'] for x in county_stats]
Hay = [x['properties'][81] if x['properties'].has_key(81) else 0 for x in county_stats]
Crops = [x['properties'][82] if x['properties'].has_key(82) else 0 for x in county_stats]
DevO = [x['properties'][21] if x['properties'].has_key(21) else 0 for x in county_stats]
DevL = [x['properties'][22] if x['properties'].has_key(22) else 0 for x in county_stats]
DevM = [x['properties'][23] if x['properties'].has_key(23) else 0 for x in county_stats]
DevH = [x['properties'][24] if x['properties'].has_key(24) else 0 for x in county_stats]
Total = [x['properties']['count'] for x in county_stats]
```


```python
from operator import truediv
Ag =  [sum(x) for x in zip(Hay, Crops)]
PctAg =  map(truediv, Ag, Total)
PctAg = [x * 100 for x in PctAg]
Urb = [sum(x) for x in zip(DevO, DevL, DevM, DevH)]
PctUrb =  map(truediv, Urb, Total)
PctUrb = [x * 100 for x in PctUrb]
```


```python
Results = pd.DataFrame(np.column_stack([FIPS, PctAg, PctUrb]), 
                               columns=['FIPS', 'PctAg', 'PctUrb'])
Results.head()
Results.to_csv('H:/WorkingData/CountyPctAgPctUrb.csv', sep = ',', index=False)
```

geo_interface is what we're making use of in zonal_stats 'geojson_out' parameter just for clarity


```python
counties.__geo_interface__['features'][0]
```




    {'bbox': (-95.34283127277658,
      48.368212434671534,
      -94.43063445677862,
      49.371730136640736),
     'geometry': {'coordinates': (((-95.34283127277658, 48.546679319076),
        (-95.34105289190684, 48.71517195733587),
        (-95.09435905148669, 48.71735751795556),
        (-95.09491035007436, 48.91176243313237),
        (-95.13382124476209, 48.89448474990026),
        (-95.21957848050616, 48.87944650348885),
        (-95.29026017093044, 48.902949581747855),
        (-95.31417172404038, 48.93207199957641),
        (-95.30375729897271, 48.94593890485217),
        (-95.32091645456259, 48.96097699585145),
        (-95.32323587682019, 48.97895631299366),
        (-95.31012059635258, 48.993395445689),
        (-95.27665710362751, 48.99999118779381),
        (-95.15774989320504, 48.9999959019614),
        (-95.15186733731112, 49.371730136640736),
        (-94.83203924782775, 49.33080592976444),
        (-94.68124996659202, 48.87716132370133),
        (-94.69443202246646, 48.777615510389126),
        (-94.57031275583246, 48.71367627110933),
        (-94.43063445677862, 48.71078529488466),
        (-94.43169006769017, 48.368212434671534),
        (-95.21178803364391, 48.36900472565064),
        (-95.21983978008106, 48.54435777285279),
        (-95.34283127277658, 48.546679319076)),),
      'type': 'Polygon'},
     'id': '0',
     'properties': {u'FIPS': u'27077', u'NAME': u'Lake of the Woods'},
     'type': 'Feature'}




```python

```
