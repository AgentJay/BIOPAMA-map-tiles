# BIOPAMA-map-tiles
The map interaction tiles are generated from these geojson files
# Tippecanoe
The BIOPAMA RIS uses vector tiles to render the data quickly. The tiles need to be vector to allow FAST restyling of the data using the mapbox API. We use the vector layers for both site browsing and data interaction.
To make everything work as quickly as possible we serve the vector tiles from a disk cache using tippecanoe.
## Customising the tiles
### Installing tippecanoe
Install tippecanoe following the instructions in the repo:
https://github.com/mapbox/tippecanoe
### Geojson
Create a geojson of the spatial data that will be used.
There's samples of geojson data found in the sub-directories.

### Creating a tile cache
We need 2 tile caches:
1. Polygons - for all the countries and protected areas
1. Points - for all the labels, and any protected areas that are points


Here's a sample for the commands to create a tile cache:

```tippecanoe -e BIOPAMA_poly -z12 --coalesce-densest-as-needed --extend-zooms-if-still-dropping --force WDPA2019MayPoly.geojson ACP_GAUL.geojson ACP_EEZ.geojson ACP_Countries.geojson ACP_SubGroups.geojson non_acp_countries.geojson```


```tippecanoe -e BIOPAMA_point -z12 -r1 --force ACP_Countries_points.geojson ACP_SubGroups_points.geojson WDPA2019MayPoints.geojson WDPA2019MayPolyPoints.geojson```

#### breaking down what the command does:
-  -e BIOPAMA_poly //the name of the generated tile cache
- -z12 //to force generating tiles to zoom12
- --coalesce-densest-as-needed //simplifies the tile output if it gets too dense.
- --extend-zooms-if-still-dropping //adds aditional zoom level if any nodes were dropped at the previous zoom level
- -r1 //to force including all points (important for point layers)
- --force //allows rewriting layers without giving an error message
- the list of geojson files //all the files that will be combined in the tile cache

# Updating the RIS
Once the map tile cache has been generated using tippecanoe you can update the BIOPAMA RIS site to use the new layers.
## JS
There are only 2 javascript files that must be updated. Both are in the theme folder. This will be changed in the future to ensure the new settings are not accidentally deleted.
1. \js\homepage\global_vars_functs.js
1. \js\map.js

### \js\homepage\global_vars_functs.js
search for the string "BIOPAMA-map-tiles" and you will find a list of the layer names that were used in generating the tippecanoe cache. You can update the layers to match the names used from YOUR geojson files. **if you are not sure. The exact names you need are output by tippecanoe after generating the tile cache**

### \js\map.js
There are a few areas to customize here:
1. Map center. You can search for 'var map = new mapboxgl.Map({' the center and zoom options will change how the homepage looks when it first loads.
2. The tile cache source. You can search for 'map.addSource("BIOPAMA_Poly", {' to update the server path to where your tile cache is located. **it needs to be done for both polygons and points**

