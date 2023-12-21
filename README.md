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
10. [ENC (S-57)](#encs57)


## Shapefile (.shp) <a name="shapefile"></a>

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

## ENC S-57 (.000) <a name="encs57"></a>

```python
import geopandas as gpd
from osgeo import ogr

# Path al archivo S-57 (.000)
s57_path = 'path/to/file.000'

# Crear un objeto ogr con el driver de S-57
driver = ogr.GetDriverByName('S57')
dataset = driver.Open(s57_path, 0)

# Obtener la capa de objetos
layer = dataset.GetLayerByIndex(0)

# Crear un GeoDataFrame desde la capa de objetos
gdf_s57 = gpd.read_file(layer)

# Cerrar el dataset
dataset = None

# Mostrar el GeoDataFrame
print(gdf_s57.head())
```
