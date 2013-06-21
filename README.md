# HDM-CartoCSS

The HDM rendering is a Carto project, focusing on the [Humanitarian Data Model](http://wiki.openstreetmap.org/wiki/Humanitarian_OSM_Tags).

Preview: http://umap.fluv.io/en/map/hdm-first-draft_728

Compare: http://compare.fluv.io/

## Deploying

### Local configuration

Suggestion is to use [cartocc](https://github.com/yohanboniface/CartoCC) for managing the local config (like db credentials, path to shp...). Have a look at the `cartocc.json.sample` file to quick start

#### Workflow to use it on TileMill:

1. Clone this dir

1. Symlink it in your MapBox/project directory with the name `hdm`

1. Rename `cartocc.json.sample` to `cartocc.json` and adapt it

1. run `cartocc hdm`: this will create a `project.mml` file with correct local config in the project dir

1. Run tilemill

### Importing data

We are using PostGIS, with the default osm2pgsql import style, AND with the hstore column. Ex.:

```
osm2pgsql -G -U ybon -d hdm data/haiti-and-domrep-latest.osm.pbf --hstore --create
```

### DEM

1. get the file from [CGIAR](http://srtm.csi.cgiar.org/)

1. Reproject it: `gdalwarp -s_srs EPSG:4269 -t_srs EPSG:3785 -r bilinear srtm_22_09.tif haiti-3785.tif`

1. Create slope file (transitional file): `gdaldem slope haiti-3785.tif haiti-slope-3785.tif`

1. Create slopeshade (this one will be loaded by carto): `gdaldem color-relief -co compress=lzw haiti-slope-3785.tif slope-ramp.txt haiti-slopeshade-3785.tif`

1. Create contour line: `gdal_contour -a height haiti-3785.tif haiti_contour_25m.shp -i 25.0`