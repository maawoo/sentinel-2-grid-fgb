# sentinel-2-grid-fgb

This is an experimental repository containing the polygon features of the Sentinel-2 tiling grid converted to the [Flatgeobuf](https://github.com/flatgeobuf/flatgeobuf) format. The original Sentinel-2 tiling grid in KML format is available [here](https://sentinel.esa.int/web/sentinel/missions/sentinel-2/data-products).

Note, that accuracy of the tiles has not been verified yet.

## Usage

The Flatgeobuf file can be accessed directly from GitHub using the GDAL `/vsicurl/` [virtual file system handler](https://gdal.org/user/virtual_file_systems.html#vsicurl-http-https-ftp-files-random-access).

The following command on your local system
``` 
ogrinfo -al -so /vsicurl/https://github.com/maawoo/sentinel-2-grid-fgb/raw/main/S2A_OPER_GIP_TILPAR_MPC__20151209T095117_V20150622T000000_21000101T000000_B00_poly.fgb
```

should print this output:
```
INFO: Open of `/vsicurl/https://github.com/maawoo/sentinel-2-grid-fgb/raw/main/S2A_OPER_GIP_TILPAR_MPC__20151209T095117_V20150622T000000_21000101T000000_B00_poly.fgb'
      using driver `FlatGeobuf' successful.

Layer name: features
Geometry: Polygon
Feature Count: 56984
Extent: (-180.000000, -83.835951) - (180.000000, 84.644279)
Layer SRS WKT:
GEOGCRS["WGS 84",
    ENSEMBLE["World Geodetic System 1984 ensemble",
        MEMBER["World Geodetic System 1984 (Transit)"],
        MEMBER["World Geodetic System 1984 (G730)"],
        MEMBER["World Geodetic System 1984 (G873)"],
        MEMBER["World Geodetic System 1984 (G1150)"],
        MEMBER["World Geodetic System 1984 (G1674)"],
        MEMBER["World Geodetic System 1984 (G1762)"],
        MEMBER["World Geodetic System 1984 (G2139)"],
        ELLIPSOID["WGS 84",6378137,298.257223563,
            LENGTHUNIT["metre",1]],
        ENSEMBLEACCURACY[2.0]],
    PRIMEM["Greenwich",0,
        ANGLEUNIT["degree",0.0174532925199433]],
    CS[ellipsoidal,2],
        AXIS["geodetic latitude (Lat)",north,
            ORDER[1],
            ANGLEUNIT["degree",0.0174532925199433]],
        AXIS["geodetic longitude (Lon)",east,
            ORDER[2],
            ANGLEUNIT["degree",0.0174532925199433]],
    USAGE[
        SCOPE["Horizontal component of 3D system."],
        AREA["World."],
        BBOX[-90,-180,90,180]],
    ID["EPSG",4326]]
Data axis to CRS axis mapping: 2,1
Name: String (0.0)
```

Individual tiles can be extracted and converted to another format (e.g., GeoJSON) using the following command:
```
ogr2ogr -f "GeoJSON" out.geojson /vsicurl/https://github.com/maawoo/sentinel-2-grid-fgb/raw/main/S2A_OPER_GIP_TILPAR_MPC__20151209T095117_V20150622T000000_21000101T000000_B00_poly.fgb -sql "SELECT * FROM features WHERE Name='32TNT'"
```

## Conversion

The original KML file was first converted using [ogr2ogr](https://gdal.org/programs/ogr2ogr.html):
```
ogr2ogr -f "Flatgeobuf" -explodecollections -skipfailures out.fgb in.kml`
```
(Thanks for the advice in [this discussion](https://github.com/flatgeobuf/flatgeobuf/discussions/262))

The output file was then imported in QGIS and only the polygon features exported to a separate Flatgeobuf file, without 
any attribute except of "Name". 

If anyone has a better suggestion on how to do the conversion more efficiently and only using the commandline, please let me know.
