# KML Notebook

## Libraries

```python
# Main libraries installation
!pip install gdown pykml basemap contextily > nul
```
```python
# Data download and extraction
import gdown
import zipfile
import os

# KML parsing
import pykml
from pykml import parser
from lxml import etree

# Geospatial data manipulation and visualization
import geopandas as gpd
import matplotlib.pyplot as plt
from shapely.geometry import Point, Polygon

# Input/Output operations
from io import BytesIO

# Basemaps and context tiles
import contextily as cx

# Data retrieval and manipulation
import requests
import pandas as pd

# Interactive visualizations
import plotly.express as px

# Interactive maps
import folium
from folium.plugins import MarkerCluster

# Displaying Matplotlib plots inline
%matplotlib inline

import warnings
warnings.filterwarnings("ignore")
```

## Data Ingestion

```python
# Read and parse KML file
with open("data/jp_kml/japan_boundaries.kml", "r") as f:
    kml = parser.parse(f)
```

```python
# Print the parsed KML content
print(kml)
```

```python
# KML file URL
url = 'https://kmlnetworklink.gsi.go.jp/kmlnetworklink/kml/gsi_ort.kml'

# Download the content of the KML file
response = requests.get(url)
kml_content = response.content

# Save the KML content to a local temporary file
local_filename = 'data/jp_kml/output/japan_boundaries.kml'
with open(local_filename, 'wb') as f:
    f.write(kml_content)
```

```python
# Read the content of the KML file
with open("data/jp_kml/output/japan_boundaries.kml", "rb") as f:
    kml_content = f.read()

# Parse the content of the KML
root = etree.fromstring(kml_content)

# Find all Placemarks in the document
places = root.findall(".//{http://www.opengis.net/kml/2.2}Placemark")

# Now, 'places' should contain a list of Placemark elements
# You can iterate over them and perform the necessary operations
for place in places:
    # Perform the needed operations with each Placemark
    # For example, you can access coordinates with:
    coordinates = place.find(".//{http://www.opengis.net/kml/2.2}coordinates").text
    print("Coordinates:", coordinates)
```

## Data Exploring

```python
# Iterate over Placemarks and print information
for placemark in kml.getroot().Document.findall('.//{http://www.opengis.net/kml/2.2}Placemark'):
    # Find the ExtendedData element
    extended_data_element = placemark.find('.//{http://www.opengis.net/kml/2.2}ExtendedData')

    if extended_data_element is not None:
        # Find the SchemaData element within ExtendedData
        schema_data_element = extended_data_element.find('.//{http://www.opengis.net/kml/2.2}SchemaData')

        if schema_data_element is not None:
            # Find SimpleData elements within SchemaData
            name_element = schema_data_element.find('.//{http://www.opengis.net/kml/2.2}SimpleData[@name="nam"]')
            district_element = schema_data_element.find('.//{http://www.opengis.net/kml/2.2}SimpleData[@name="laa"]')

            # Extract information or provide default values if not found
            name = name_element.text if name_element is not None and name_element.text is not None else "Nombre no encontrado"
            district = district_element.text if district_element is not None and district_element.text is not None else "Distrito no encontrado"
            
            # Extract coordinates
            coordinates = placemark.find('.//{http://www.opengis.net/kml/2.2}Polygon/{http://www.opengis.net/kml/2.2}outerBoundaryIs/{http://www.opengis.net/kml/2.2}LinearRing/{http://www.opengis.net/kml/2.2}coordinates').text.strip()

            # Print information
            print("Prefecture:", name)
            print("District/Province:", district)
            print("Coordinates:", coordinates)
            print("\n")
        else:
            print("SchemaData not found in ExtendedData.")
    else:
        print("ExtendedData not found in Placemark.")
```

```python
# Create a list to store Placemark geometries
geometries = []

# Iterate over Placemarks and create geometries
for placemark in kml.getroot().Document.findall('.//{http://www.opengis.net/kml/2.2}Placemark'):
    # Find the ExtendedData element
    extended_data_element = placemark.find('.//{http://www.opengis.net/kml/2.2}ExtendedData')

    if extended_data_element is not None:
        # Find the SchemaData element within ExtendedData
        schema_data_element = extended_data_element.find('.//{http://www.opengis.net/kml/2.2}SchemaData')

        if schema_data_element is not None:
            # Find SimpleData elements within SchemaData
            name_element = schema_data_element.find('.//{http://www.opengis.net/kml/2.2}SimpleData[@name="nam"]')
            district_element = schema_data_element.find('.//{http://www.opengis.net/kml/2.2}SimpleData[@name="laa"]')

            # Extract information or provide default values if not found
            name = name_element.text if name_element is not None and name_element.text is not None else "Nombre no encontrado"
            district = district_element.text if district_element is not None and district_element.text is not None else "Distrito no encontrado"
            
            # Extract coordinates
            coordinates = placemark.find('.//{http://www.opengis.net/kml/2.2}Polygon/{http://www.opengis.net/kml/2.2}outerBoundaryIs/{http://www.opengis.net/kml/2.2}LinearRing/{http://www.opengis.net/kml/2.2}coordinates').text.strip()

            # Parse coordinates and create a Shapely geometry
            coords = [tuple(map(float, coord.split(','))) for coord in coordinates.split()]
            if len(coords) > 2:
                # It's a polygon
                geometry = Polygon(coords)
            else:
                # It's a point
                geometry = Point(coords[0])

            geometries.append({'name': name, 'district': district, 'geometry': geometry})
        else:
            print("SchemaData not found in ExtendedData.")
    else:
        print("ExtendedData not found in Placemark.")

# Create a GeoDataFrame with GeoPandas
gdf = gpd.GeoDataFrame(geometries, geometry=gpd.GeoSeries([geom['geometry'] for geom in geometries]))

# Save as GeoJSON
gdf.to_file("data/jp_kml/output/output.geojson", driver="GeoJSON")

# Save as a Shapefile in a temporary file
temp_shapefile_path = "data/jp_kml/output/temp_shapefile.shp"
gdf.to_file(temp_shapefile_path, driver="ESRI Shapefile")

# Read the Shapefile
gdf_kml = gpd.read_file(temp_shapefile_path)

# Display the GeoDataFrame
print(gdf_kml)
```

## Data Visualization

```python
# Set the Coordinate Reference System (CRS) to EPSG:4326
gdf_kml = gdf_kml.set_crs(4326, allow_override=True)
```
```python
# Visualize the vector file of county subdivisions
fig, ax = plt.subplots(figsize=(12, 10))
gdf_kml.plot(ax=ax, column='name', cmap='rainbow', legend=True, legend_kwds={'bbox_to_anchor': (1.4, 1)})
cx.add_basemap(ax, source=cx.providers.OpenStreetMap.Mapnik)
ax.set_title('JAPAN Prefectures')
plt.show()
```
![img_jp_md](data/jp_kml/output/japan_md.png)

