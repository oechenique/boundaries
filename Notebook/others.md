# Other commands

## TABLE OF CONTENTS
1. [GeoPackage (GPKG)](#gpkg)
2. [KMZ (Compressed KML)](#kmz)
3. [TopoJSON](#topojson)
4. [GML](#gml)
5. [DXF](#dxf)
6. [Geoparquet](#parquet)


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
