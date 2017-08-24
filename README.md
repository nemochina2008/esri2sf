# esri2sf
Scraping Geographic Features from ArcGIS Server

Still many geographic data is delivered through ESRI's ArcGIS Server. 
It is not easy to utilize the geographic data in the GIS servers from data analysis platform like R or Pandas. 
This package enables users to use vector data in ArcGIS Server from R through the server's REST API. 
It download geographic features from ArcGIS Server and saves it as Simple Features. 

Git repository is here [https://github.com/yonghah/esri2sf](https://github.com/yonghah/esri2sf)


## How it works

This program sends a request to an ArcGIS Server and gets json responses containing coordinates of geometries. 
Unfortunately, ESRI's json format is not the same as geojson. So it converts the json into WKT strings first; 
then creates simple feature geometries from the strings. Then it combines attribute data to the geometries to 
create sf dataframe. Often ArcGIS servers limits the maximum number of rows in the result set. So this program 
creates 500 features per request and send requests until it gets all features. 

## Install

I recommend devtools to install this package.This package has dependency on dplyr, sf, httr, jsonlite

```
devtools::install_github("yonghah/esri2sf")
library("esri2sf")
```

## How to use

What you need is the URL of REST service you want. You can get the URL by asking GIS admin or looking at 
javascript code creating feature layer.

### Point data

```
url <- "https://services.arcgis.com/V6ZHFr6zdgNZuVG0/arcgis/rest/services/Landscape_Trees/FeatureServer/0"
df <- esri2sf(url)
plot(df)
```
![point plot](https://user-images.githubusercontent.com/3218468/29668766-544723a2-88af-11e7-8852-e8f7d21ffd5b.png)


### Polyline data

You can filter output fields. This may take a minute since it gets 18000 polylines. 

```
url <- "https://services.arcgis.com/V6ZHFr6zdgNZuVG0/arcgis/rest/services/Florida_Annual_Average_Daily_Traffic/FeatureServer/0"
df <- esri2sf(url, outFields=c("AADT", "DFLG"))
plot(df)
```
![line plot](https://user-images.githubusercontent.com/3218468/29668781-5dc1f4de-88af-11e7-8680-4d2ad648e04f.png)

### Polygon data

You can filter rows as well by giving where condition.

```
url <- "https://sampleserver1.arcgisonline.com/ArcGIS/rest/services/Demographics/ESRI_Census_USA/MapServer/3"
df <- esri2sf(url, 
              where="STATE_NAME = 'Michigan'", 
              outFields=c("POP2000", "pop2007", "POP00_SQMI", "POP07_SQMI"))
plot(df)
```
![polygon plot](https://user-images.githubusercontent.com/3218468/29668791-63e66976-88af-11e7-9f6c-5d95bac4a69e.png)
