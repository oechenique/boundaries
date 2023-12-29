# Managing Geospatial Data with Python: A Guide to Loading and Manipulating GeoDataFrames

Explore different techniques for handling geospatial data using Python and GeoPandas. Additionally, check out some related [libraries](https://github.com/oechenique/boundaries/blob/main/Notebook/libraries.md).

## TABLE OF CONTENTS
1. [Shapefile](#shapefile)
2. [GeoJSON](#geojson)
3. [KML](#kml)
4. [WFS (Web Feature Service)](#wfs)
5. [GIS Web Service](#gws)
6. [H3 (UBER H3 Hexagon)](#h3)
7. [3D Visualization](#3d-visualization)
8. [Other File Formats](#other)

## Loading Shapefiles (.shp) <a name="shapefile"></a>

Refer to the [shapefile notebook](https://github.com/oechenique/boundaries/blob/main/Notebook/shapefile.ipynb) for detailed examples and Python code.

```python
import geopandas as gpd

# Path to the shapefile
shp_path = 'path/to/file.shp'

# Load the shapefile into a GeoDataFrame
gdf_shapefile = gpd.read_file(shp_path)

# Display the GeoDataFrame
print(gdf_shapefile.head())
```

## Loading GeoJSON (.geojson) <a name="geojson"></a>

Check out the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/geojson.ipynb" target="_blank">geojson notebook</a> for detailed examples and Python code.

```python
import geopandas as gpd

# Path to the GeoJSON file
geojson_path = 'path/to/file.geojson'

# Load the GeoJSON into a GeoDataFrame
gdf_geojson = gpd.read_file(geojson_path)

# Display the GeoDataFrame
print(gdf_geojson.head())
```

## Loading KML (.kml) <a name="kml"></a>

Explore the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/kml.zip" target="_blank">kml notebook</a> for a detailed KML loading example.

Note: If you can display the notebook, same code here: <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/kml.md" target="_blank">kml md</a>.

```python
import geopandas as gpd

# Path to the KML file
kml_path = 'path/to/file.kml'

# Load the KML into a GeoDataFrame
gdf_kml = gpd.read_file(kml_path)

# Display the GeoDataFrame
print(gdf_kml.head())
```

## Accessing Web Feature Services (WFS) <a name="wfs"></a>

Refer to the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/wms.ipynb" target="_blank">wfs notebook</a> for detailed examples and Python code.

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

## Using GIS Web Services <a name="gws"></a>

Explore the [gis web services notebook](https://github.com/oechenique/boundaries/blob/main/Notebook/web_service.ipynb) for examples on accessing GIS Web Services such as OpenStreetMap and Google Maps, and learn how to retrieve relevant geospatial information using Python.

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

## Creating H3 Hexagons <a name="h3"></a>

Refer to the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/h3.ipynb" target="_blank">h3 notebook</a> for detailed examples and Python code.

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

## 3D Visualization of Geospatial Data <a name="3d-visualization"></a>

Explore the [3D visualization notebook](https://github.com/oechenique/boundaries/blob/main/Notebook/3d_visualization.ipynb) to learn how to visualize geospatial data with a focus on the Z-axis. This notebook demonstrates techniques for creating stunning 3D visualizations using Python.

```python
# Your Python code for 3D visualization goes here
# ...
```

## Handling Other File Formats <a name="other"></a>

Explore the <a href="https://github.com/oechenique/boundaries/blob/main/Notebook/others.md" target="_blank">others md</a> for detailed examples and Python code.
