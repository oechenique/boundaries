# Uploading Geospatial Files with Python Commands: A Step-by-Step Guide
Different techniques exist for loading layers, which represent geospatial data, into GeoDataFrames.

## TABLE OF CONTENTS
1. [Shapefile](#shapefile)
2. [GeoJSON](#geojson)
3. [GeoPackage (GPKG)](#gpkg)
4. [KMZ (Compressed KML)](#kmz)
5. [TopoJSON](#topojson)
6. [GML](#gml)
7. [KML](#kml)
8. [DXF](#dxf)
9. [Geoparquet](#parquet)
10. [WFS (Web Feature Service)](#wfs)
11. [GIS Web Service](#gws)


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

## GPKG (.gpkg) <a name="gpkg"></a>

```python
import geopandas as gpd

# Path to the GPKG file
gpkg_path = 'path/to/file.gpkg'

# Load the GPKG into a GeoDataFrame
gdf_gpkg = gpd.read_file(gpkg_path)

# Display the GeoDataFrame
print(gdf_gpkg.head())
```

## KMZ (Compressed KML) <a name="kmz"></a>

```python
import geopandas as gpd
import shutil
from zipfile import ZipFile

# Path to the KMZ file
kmz_path = 'path/to/file.kmz'
kml_path = 'destination/path/file.kml'

# Extract the KML from the KMZ file
with ZipFile(kmz_path, 'r') as kmz:
    kmz.extractall('destination/path/')

# Load the KML into a GeoDataFrame
gdf_kmz = gpd.read_file(kml_path)

# Display the GeoDataFrame
print(gdf_kmz.head())
```

## TopoJSON (.topojson) <a name="topojson"></a>

```python
import geopandas as gpd
import json
from topojson import topojson

# Path to the TopoJSON file
topojson_path = 'path/to/file.topojson'

# Load the TopoJSON and convert it to GeoDataFrame
with open(topojson_path, 'r') as f:
    topojson_data = json.load(f)

gdf_topojson = gpd.GeoDataFrame.from_features(topojson(topojson_data))

# Display the GeoDataFrame
print(gdf_topojson.head())
```

## GML (.gml) <a name="gml"></a>

```python
import geopandas as gpd

# Path to the GML file
gml_path = 'path/to/file.gml'

# Load the GML into a GeoDataFrame
gdf_gml = gpd.read_file(gml_path)

# Display the GeoDataFrame
print(gdf_gml.head())
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

## DXF (.dxf)) <a name="dxf"></a>
For DXF files, you may use the ezdxf library. You can install it using pip install ezdxf
```python
import geopandas as gpd
import ezdxf

# Path to the DXF file
dxf_path = 'path/to/file.dxf'

# Load the DXF into a GeoDataFrame
doc = ezdxf.readfile(dxf_path)
msp = doc.modelspace()
gdf_dxf = gpd.GeoDataFrame(geometry=[entity.geometry for entity in msp.query('LINE')])

# Display the GeoDataFrame
print(gdf_dxf.head())
```

## Geoparquet (.parquet) <a name="parquet"></a>

```python
import geopandas as gpd

# Path to the Geoparquet file
parquet_path = 'path/to/file.parquet'

# Load the Geoparquet into a GeoDataFrame
gdf_geoparquet = gpd.read_parquet(parquet_path)

# Display the GeoDataFrame
print(gdf_geoparquet.head())
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
