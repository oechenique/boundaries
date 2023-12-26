# Uploading Geospatial Files with Python Commands: A Step-by-Step Guide

Different techniques for loading layers, which represent geospatial data, into GeoDataFrames. Here some related <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/libraries.md" target="_blank">libraries</a>.

## TABLE OF CONTENTS
1. [Shapefile](#shapefile)
2. [GeoJSON](#geojson)
3. [KML](#kml)
4. [WFS (Web Feature Service)](#wfs)
5. [GIS Web Service](#gws)
6. [H3 (UBER H3 Hexagon)](#h3)
7. [Other File Formats](#other)


## Shapefile (.shp) <a name="shapefile"></a>

In the accompanying notebook, you can find the Python implementation of the process. Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/shapefile.ipynb" target="_blank">shapefile notebook</a> for a detailed example.

```python
import geopandas as gpd

# Path to the shapefile
shp_path = 'path/to/file.shp'

# Load the shapefile into a GeoDataFrame
gdf_shapefile = gpd.read_file(shp_path)

# Display the GeoDataFrame
print(gdf_shapefile.head())
```

## GeoJSON (.geojson) <a name="geojson"></a>

In the accompanying notebook, you can find the Python implementation of the process. Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/geojson.ipynb" target="_blank">geojson notebook</a> for a detailed example.

```python
import geopandas as gpd

# Path to the GeoJSON file
geojson_path = 'path/to/file.geojson'

# Load the GeoJSON into a GeoDataFrame
gdf_geojson = gpd.read_file(geojson_path)

# Display the GeoDataFrame
print(gdf_geojson.head())
```

## KML (.kml) <a name="kml"></a>

In the accompanying notebook, you can find the Python implementation of the process. Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/kml.zip" target="_blank">kml notebook</a> for a detailed example. 

Note: If you can see the notebook, same code here: <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/kml.md" target="_blank">kml md</a>.

```python
import geopandas as gpd

# Path to the KML file
kml_path = 'path/to/file.kml'

# Load the KML into a GeoDataFrame
gdf_kml = gpd.read_file(kml_path)

# Display the GeoDataFrame
print(gdf_kml.head())
```

## Web Feature Server (WFS) <a name="wfs"></a>

In the accompanying notebook, you can find the Python implementation of the process. Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/wms.ipynb" target="_blank">wfs notebook</a> for a detailed example.

```python
from owslib.wfs import WebFeatureService
import json
import geopandas as gpd

# WFS (Web Feature Service) URL
wfs_url = 'https://your-web-service-url/geoserver/ows?'
version = '1.1.0'

# Create a WFS object
wfs = WebFeatureService(url=wfs_url, version=version)

# Get feature data from WFS using specified parameters
typename = 'your-layer-name'
srsname = 'EPSG:4326'
output_format = 'application/json'
response = wfs.getfeature(typename=typename, srsname=srsname, outputFormat=output_format)

# Read the response data
data = response.read()

# Load data into a dictionary
data_dict = json.loads(data)

# Save data to a temporary GeoJSON file
with open('temp.geojson', 'w') as geojson_file:
    json.dump(data_dict, geojson_file)

# Read data from the GeoJSON file into a GeoDataFrame
gdf = gpd.read_file('temp.geojson')

# Set a Coordinate Reference System (CRS) for the GeoDataFrame
gdf = gdf.set_crs(epsg=4326, allow_override=True)
```

## GIS Web Service <a name="gws"></a>

In the accompanying notebook, you can find the Python implementation of the process. Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/web_service.ipynb" target="_blank">gis web services notebook</a> for a detailed example.

```python
import osmnx as ox
import geopandas as gpd

# Specify the name of the city or area of interest
city = "Name of the City or Area of Interest"

# Download OSM data using Overpass API
tags = {"amenity": "bar"}
gdf = ox.geometries_from_place(city, tags)

# Filter and create a GeoPandas DataFrame with relevant data
gdf = gdf[gdf['amenity'] == 'bar']

gdf = gdf.reset_index()

# Filter only Point geometries
gdf = gdf[gdf['geometry'].geom_type == 'Point']

# Display the resulting DataFrame
print(gdf)
```

## H3 (UBER H3 Hexagon) <a name="h3"></a>

In the accompanying notebook, you can find the Python implementation of the process. Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/h3.ipynb" target="_blank">h3 notebook</a> for a detailed example.

```python
import h3
import geopandas as gpd
from shapely.geometry import Polygon

# Specify the resolution for H3 hexagons
h3_resolution = 9

# Create a GeoDataFrame to hold the H3 data
gdf_h3 = gpd.GeoDataFrame()

# Define the bounding box of the area of interest
bbox = Polygon([(min_longitude, min_latitude), (min_longitude, max_latitude),
                (max_longitude, max_latitude), (max_longitude, min_latitude)])

# Get the H3 hexagons covering the bounding box
hexagons = list(h3.polyfill(bbox.exterior.coords, res=h3_resolution))

# Create a GeoDataFrame with the hexagons
gdf_h3['geometry'] = gpd.GeoSeries([Polygon(h3.h3_to_geo_boundary(hexagon)) for hexagon in hexagons])
gdf_h3['hex_id'] = hexagons

# Display the resulting GeoDataFrame
print(gdf_h3)
```

## Other File Formats <a name="other"></a>

In the accompanying notebook, you can find the Python implementation of the process. Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/others.md" target="_blank">others md</a> for a detailed example.
