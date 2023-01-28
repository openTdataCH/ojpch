
# GTFS viewing with R
Matthias Günter, matthias.guenter@gnostx.ch, Creative Commons Share Alike 3.0

## Introduction to GTFS Tools
* https://ipeagit.github.io/gtfstools/articles/gtfstools.html
* https://cran.r-project.org/web/packages/gtfstools/gtfstools.pdf
* https://cran.r-project.org/web/packages/gtfstools/vignettes/gtfstools.html
* https://www.earthdatascience.org/courses/earth-analytics/spatial-data-r/make-maps-with-ggplot-in-R/
* https://stackoverflow.com/questions/28753444/how-to-create-an-interactive-plot-of-gtfs-data-in-r-using-leaflet
* https://rpubs.com/walkerke/rstudio_gis
* https://rdrr.io/cran/gtfstools/man/get_trip_geometry.html

## Installing packages in R
```R
install.packages("leaflet")
install.packages("gtfstools")
```

# Running scripts from rgui
```R
source("where")
```

## Possible sources around Switzerland
xxx

## Getting the data from a file
```R
library(gtfstools)
library(sf)
data_path <- file.path("D:","development","gtfs_with_r", fsept="\\")
#currently stored files (on my machine), select only one spo_path
spo_path <- file.path(data_path, "gtfs_fp2023_2023-01-18_04-15.zip")
#spo_path <- file.path(data_path, "SOLEA.GTFS_current.zip")
#spo_path <- file.path(data_path, "gtfs-interurbain-du-01-01-22-au-31-08-22.zip")
#spo_path <- file.path(data_path, "bwgesamt.zip")
#spo_path <- file.path(data_path, "gtfs_generic_flixbus.zip")
#spo_path <- file.path(data_path, "gtf_blablacarbus.zip")
spo_gtfs <- read_gtfs(spo_path)
names(spo_gtfs)
#the next command returns a LINESTRING sf
trip_geom <- get_trip_geometry(spo_gtfs, file = "stop_times")


#Switzerland  https://gist.github.com/graydon/11198540
xmin = 6.02260949059
xmax = 10.4427014502
ymax = 47.8308275417
ymin = 45.7769477403
#Increase bounding box
slack = 0.5
bb <- st_bbox(c(xmin = xmin-slack, xmax = xmax+slack, ymax = ymax+slack, ymin = ymin-slack), crs = st_crs(4326))
ch1 <-st_as_sfc(bb)
clipped <- st_intersection(trip_geom$geometry,ch1)
 ```

And putting things onto a map
```R
library(leaflet)
m <- leaflet() %>% setView(lng =7.444 , lat = 46.947, zoom = 6) %>% addTiles() %>% addPolylines(data=clipped)
m
```

#  Filtering: Only a  given hour of the day
```R

spo_gtfs_filtered <- filter_by_time_of_day(spo_gtfs,from="23:00:00",to="23:59:59",keep=TRUE, full_trips=TRUE,update_frequencies=TRUE)
trip_geom <- get_trip_geometry(spo_gtfs_filtered, file = "stop_times")
clipped <- st_intersection(trip_geom$geometry,ch1)
m <- leaflet() %>% setView(lng =7.444 , lat = 46.947, zoom = 6) %>% addTiles() %>% addPolylines(data=clipped)
m

```
#  Filtering: Only a  given weekday
```R
weekday=c("sunday")
spo_gtfs_filtered <- filter_by_weekday(spo_gtfs,weekday=weekday,keep=TRUE)
trip_geom <- get_trip_geometry(spo_gtfs_filtered, file = "stop_times")
clipped <- st_intersection(trip_geom$geometry,ch1)
m <- leaflet() %>% setView(lng =7.444 , lat = 46.947, zoom = 6) %>% addTiles() %>% addPolylines(data=clipped)
m

```

#  Filtering for a given stop (with id)
```R
xxx

```
#  Filtering for a given stop with bounding box
```R
xxx

```

#  Filtering by service
```R
# 0 - Tram, Streetcar, Light rail. Any light rail or street level system within a metropolitan area.
# 1 - Subway, Metro. Any underground rail system within a metropolitan area.
# 2 - Rail. Used for intercity or long-distance travel.
# 3 - Bus. Used for short- and long-distance bus routes.
# 4 - Ferry. Used for short- and long-distance boat service.
# 5 - Cable tram. Used for street-level rail cars where the cable runs beneath the vehicle, e.g., cable car in San Francisco.
# 6 - Aerial lift, suspended cable car (e.g., gondola lift, aerial tramway). Cable transport where cabins, cars, gondolas or open chairs are suspended by means of one or more cables.
# 7 - Funicular. Any rail system designed for steep inclines.
# 11 - Trolleybus. Electric buses that draw power from overhead wires using poles.
# 12 - Monorail. Railway in which the track consists of a single rail or a beam
route_types=c(2)
spo_gtfs_filtered <- filter_by_route_type(spo_gtfs,route_type=route_types,keep=TRUE)
trip_geom <- get_trip_geometry(spo_gtfs_filtered, file = "stop_times")
clipped <- st_intersection(trip_geom$geometry,ch1)
m <- leaflet() %>% setView(lng =7.444 , lat = 46.947, zoom = 6) %>% addTiles() %>% addPolylines(data=clipped)
#TODO Has problem with the Swiss data
m

```

# How to get only one trip per route (to speed things up) 
Be aware that some trips may be shorter than others for the same route. Also the first one probably is, if it is at a beginning or the end of the day

We start from https://ipeagit.github.io/gtfstools/articles/gtfstools.html that the GTFS object is a data.table element.

We use unique in the trip table to reduce the number

```R
spo_gtfs_filtered <- spo_gtfs
unique(spo_gtfs_filtered$trips,by=("route_id"))
trip_geom <- get_trip_geometry(spo_gtfs_filtered, file = "stop_times")
clipped <- st_intersection(trip_geom$geometry,ch1)
m <- leaflet() %>% setView(lng =7.444 , lat = 46.947, zoom = 6) %>% addTiles() %>% addPolylines(data=clipped)
m
# 


}

```

# validate a GTFS file
``` R
output_path <- tempfile("validation_result")
> validator_path <- download_validator(tempdir())
> validate_gtfs(spo_gtfs_filtered, output_path, validator_path)
```
