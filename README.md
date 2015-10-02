# Oct 2 CaroDB workshop

#### Location:
UC Berkeley

#### Led by:

1. Andy Eschbacker (Map Scientist)
2. Mamata Akella (Senior Cartographer)

## Overview:

Follow [this tutorial](https://gist.github.com/makella/7fb0de5cc84f33839614)

### Resources:

1. [CartoDB Map Academy](http://academy.cartodb.com)
2. [Tutorials](http://docs.cartodb.com/tutorials)
3. [Documentation](http://docs.cartodb.com)
4. [Other Workshops](http://cartodb.github.io/training)
5. [Earth Data (Natural earth)](http://naturalearth.com)
6. [global_24hr (real-time fire data)](https://firms.modaps.eosdis.nasa.gov/active_fire/shapes/zips/Global_24h.zip)

## Workshop Map

### Download Data

- go to Natural Earth and download medium > physical > land
- copy link Location
- into cartoDB dataset
- repeat with medium > physical > ocean
- repeat with medium > physical > graticules > 15
- repeat with global_24hr

### Create Map

- in Map View order data as Ocean > Graticules > Land > global_24hr (bottom up)

### Link Data to Map

-map with Robinson Projection from [here](http://epsg.io/54030.sql)

-must add SQL query to 'hidden' layer in map called 'spatial_ref_sys'

```
# in global_24hr

SELECT spatial_ref_sys
INSERT into spatial_ref_sys (srid, auth_name, auth_srid, proj4text, srtext) values ( 54030, 'ESRI', 54030, '+proj=robin +lon_0=0 +x_0=0 +y_0=0 +datum=WGS84 +units=m +no_defs ', 'PROJCS["World_Robinson",GEOGCS["GCS_WGS_1984",DATUM["WGS_1984",SPHEROID["WGS_1984",6378137,298.257223563]],PRIMEM["Greenwich",0],UNIT["Degree",0.017453292519943295]],PROJECTION["Robinson"],PARAMETER["False_Easting",0],PARAMETER["False_Northing",0],PARAMETER["Central_Meridian",0],UNIT["Meter",1],AUTHORITY["EPSG","54030"]]');

```

-now within land, ocean, and graticules

```
SELECT
  ST_Transform(the_geom, 54030)
AS        
  the_geom_webmercator
FROM
  ne_50m_land
```

-finally enable interactivity and such

```
SELECT
  ST_Transform(the_geom, 54030)
AS      
  the_geom_webmercator,
  cartodb_id,
  confidence,
  brightness
FROM
  global_24h
WHERE
  confidence>=50
ORDER BY
  brightness ASC
```

### Map styling

```
# ocean

#ne_50m_ocean {
  polygon-fill: #9fc4dd;
  polygon-opacity: 0.7;
}

# land

#ne_50m_land{
  polygon-fill: #ede1d8;
  polygon-opacity: 1;
}

# graticules_15

#ne_50m_graticules_15{
  line-color: #FFFFFF;
  line-width: 0.5;
  line-opacity: 0.7;
}

```

#### Stylize Fire Data

-click on Wizard

*or*

-Add Element

-Add Title

etc

### Finishing

-**Publish** map and embed into URL or whatever
