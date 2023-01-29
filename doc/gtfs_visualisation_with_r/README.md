
# GTFS viewing with R
Matthias Günter, matthias.guenter@gnostx.ch, Creative Commons Share Alike 3.0

#Abstract
This small tutorial shows how to visualise GTFS files on maps, have some simple and more complex check on them.

## Introduction to GTFS Tools
We will work with gtfstools (https://ipeagit.github.io/gtfstools/articles/gtfstools.html). Another description can be found here: https://cran.r-project.org/web/packages/gtfstools/gtfstools.pdf and https://rdrr.io/cran/gtfstools/man/get_trip_geometry.html

We will use leaflet for some plots and maps. Especially when we want to see the gtfs on an osm map: https://www.earthdatascience.org/courses/earth-analytics/spatial-data-r/make-maps-with-ggplot-in-R/

The GTFS static reference can be found here: https://gtfs.org/schedule/reference/

## Installing packages in R
The following packages are neededd in the installation, if they are not yet present:
```R
install.packages("leaflet")
install.packages("gtfstools")
```


## Possible sources around Switzerland
The analysis we are doing is of GTFS files that exist around Switzerland. It will work with other GTFS files as well. We also list the areas, where unfortunately the GTFS files are missing, which they should not be as EU/1926/2017 explicitly states that the timetables should be available.

### Problems with data
Several problem points may exist and in level of increasing severity they are:

* permalink does not end in ".zip" (a problem for gtfstool)
* no permalink (changing once a year is not really a problem)
* dataset split in multiple datasets
* Awkward additions to the licenses (only standard open licenses should be used)
From here on the problems are really bad, either for harvesting or for using the data at all (and again, its against European regulation, well NeTEx instead of GTFS is ok):
* Acess only with logging in
* One has to pay
* Not open government data
* no GTFS available
* no timetable available (not even as NeTEx)

Furthermore we may encounter additional problems in the data, that might need some cleansing before integration. But that is a very different topic.

### Switzerland and Liechtenstein
* Swiss GTFS (!changes with year): https://opentransportdata.swiss/en/dataset/timetable-2023-gtfs2020

### Germany
* Baden-Würtemberg: https://www.mobidata-bw.de/dataset/
* Bayern: missing
* Deutschland: https://gtfs.de/

### Austria
* https://mobilitaetsdaten.gv.at/daten/soll-fahrplandaten-gtfs 

### Italy
* STA: missing
* 5T: missing
* ARIA: missing
* Aosta: missing
* Italian rail: missing

### Slowenia
* https://www.nap.si/en (not open and not free)

### France
* ARA: https://transport.data.gouv.fr/datasets/agregat-oura
* Alsace: https://www.data.gouv.fr/fr/organizations/mulhouse-alsace-agglomeration/
* Grand-Est: missing
* Bourgogne-Franche-Comte: https://transport.data.gouv.fr/datasets/reseau-de-transport-interurbain-mobigo-en-bourgogne-franche-comte/?locale=en&slug=reseau-de-transport-interurbain-mobigo-en-bourgogne-franche-comte
* TER: https://ressources.data.sncf.com/explore/dataset/sncf-ter-gtfs/table/

### Long distance buses
* Flixbus: https://www.data.gouv.fr/fr/organizations/flixbus-france/
* blablacarbus: https://www.data.gouv.fr/fr/organizations/blablacar-bus/

### Others
Some other data sets might be used for test purposes.

* Eurostar: https://www.data.gouv.fr/fr/datasets/eurostar-gtfs/
* Provence: https://www.data.gouv.fr/fr/datasets/lignes-des-reseaux-de-transport-zou-en-provence-alpes-cote-dazur/ 

## Getting the data from a file
For this part we assume that the files have been downloaded. The program will work only, once you have set the data path correcty and also have set the file path
```R
library(gtfstools)
library(sf)
# change the data path to your data path
data_path <- file.path("D:","development","gtfs_with_r", fsept="\\") 

#the spo_path should be set to files that are on your machine. This is a snapshot from mine (exactly one should be uncommented here
#Switzerland
#spo_path <- file.path(data_path, "gtfs_fp2023_2023-01-18_04-15.zip")
# Baden-Würtemberg
spo_path <- file.path(data_path, "bwgesamt.zip")
# Deutschland
spo_path <- file.path(data_path, "latest.zip")
#Flixbus
#spo_path <- file.path(data_path, "gtfs_generic_flixbus.zip")
#Blablacarbus
#spo_path <- file.path(data_path, "gtf_blablacarbus.zip")
#Österrecih
#spo_path <- file.path(data_path, "20230127-0130_gtfs_salzburgverkehr_2023.zip")
#spo_path <- file.path(data_path, "20230128-0207_gtfs_vmobil_2023.zip")
#spo_path <- file.path(data_path, "20230128-0202_gtfs_vvt_2023.zip")
#spo_path <- file.path(data_path, "20230128-0132_gtfs_verbundlinie_2023.zip")
#spo_path <- file.path(data_path, "20230128-0121_gtfs_evu_2023.zip")
#spo_path <- file.path(data_path, "20230128-0148_gtfs_vor_2023.zip")
#France
spo_path <- file.path(data_path, "UT25.GTFS.zip")
#spo_path <- file.path(data_path, "UT89.GTFS.zip")
#spo_path <- file.path(data_path, "UT39.GTFS.zip")
#spo_path <- file.path(data_path, "UT71.GTFS.zip")
#spo_path <- file.path(data_path, "UT70.GTFS.zip")
#spo_path <- file.path(data_path, "UT21.GTFS.zip")
#spo_path <- file.path(data_path, "UT58.GTFS.zip")
#spo_path <- file.path(data_path, "DAT_AURA_GTFS_ExportAOM_Option1.zip")
#spo_path <- file.path(data_path, "export-ter-gtfs-last.zip")

# load the file
spo_gtfs <- read_gtfs(spo_path)

# check what file names exist
names(spo_gtfs)

# extract the geometry as a LINESTRING sf: Here the method basing on stop_times is used as it should always work. The shape based one has more problem, as 
# often shapes are not incorporated.
trip_geom <- get_trip_geometry(spo_gtfs, file = "stop_times")

# we will introduce clippling later. Here we don't do it, so it is just a copy.
clipped <- trip_geom$geometry  #no clipping

# Now we put the geometry on a map with a certain zoom factor and centered around Bern 
library(leaflet)
m <- leaflet() %>% setView(lng =7.444 , lat = 46.947, zoom = 7) %>% addTiles() %>% addPolylines(data=clipped)
m
```

# Some analytics (withouth plotting)
gtfstool has some detailed validation and we come to that. However, sometimes one is interested in some general information. Here are some possibilities.

```R
library(gtfstools)
library(sf)
library(data.table)
from datatable import dt
data_path <- file.path("D:","development","gtfs_with_r", fsept="\\")
#Flixbus
spo_path <- file.path(data_path, "gtfs_generic_flixbus.zip")
spo_gtfs <- read_gtfs(spo_path)
# >>> here the information start
print("The files seen:")
names(spo_gtfs)
print("number of agencies:")
uniqueN(spo_gtfs$agency)
print("some agencies seen:")
t1 <- unique(spo_gtfs$agency)
print(t1)
print("number of routes:")
uniqueN(spo_gtfs$routes)
print("number of trips:")
uniqueN(spo_gtfs$trips)
print("number of stops:")
uniqueN(spo_gtfs$stops)
# Not all GTFS files cover the same amount of time. Good practice would dicate that a feed_info.txt is provided, but well....
# So we check the first and last date in the two calender files and show them. This gives an indication.
print("time covered (we assume no feed info is present)")
min(spo_gtfs$calendar$start_date)
min(spo_gtfs$calendar_dates$date)

max(spo_gtfs$calendar$end_date)
max(spo_gtfs$calendar_dates$date)

# We then start to cover some enumeration fields. E.g. route_type is needed in one of the filter. Unfortunately there is more than one
# definition. Unfortunately especially the Swiss one does not adhere to the official definiation. This happens with extensions :-(
print("route_type:")
t1 <- unique(spo_gtfs$routes$route_type)
head(t1)

```

# Clipping to a bounding box around Switzerland
For bigger datasets we want to have speed up (especially in leaflet this otherwise does not work out well). 
```R
library(gtfstools)
library(sf)
data_path <- file.path("D:","development","gtfs_with_r", fsept="\\")
#currently stored files (on my machine), select only one spo_path
#Switzerland
spo_path <- file.path(data_path, "gtfs_fp2023_2023-01-18_04-15.zip")

spo_gtfs <- read_gtfs(spo_path)
names(spo_gtfs)
#the next command returns a LINESTRING sf
trip_geom <- get_trip_geometry(spo_gtfs, file = "stop_times")
#Switzerland  https://gist.github.com/graydon/11198540
xmin = 6.02260949059
xmax = 10.4427014502
ymax = 47.8308275417
ymin = 45.7769477403
#Increase bounding box a bit to see more
slack = 0.5
bb <- st_bbox(c(xmin = xmin-slack, xmax = xmax+slack, ymax = ymax+slack, ymin = ymin-slack), crs = st_crs(4326))
ch1 <-st_as_sfc(bb)
#clipping the lines to the bounding box
# TODO sometime single points remain that cause problem with leaflet.
clipped <- st_intersection(trip_geom$geometry,ch1)
# and put things on a map centered around Bern
library(leaflet)
m <- leaflet() %>% setView(lng =7.444 , lat = 46.947, zoom = 7) %>% addTiles() %>% addPolylines(data=clipped)
m
```
# Reading form an URL
```R
#Permalink Swiss data 2023: https://opentransportdata.swiss/en/dataset/timetable-2023-gtfs2020/permalink

data_path <- "https://opentransportdata.swiss/dataset/507c734f-0d9f-48be-843c-7bfd3bf5ec15/resource/afa1055f-21bf-47b5-9421-f64fc9f00bd6/download/gtfs_fp2023_2023-01-25_04-15.zip"
#TODO problem with Swiss permalink (does not have a .ZIP ending)
gtfs <- read_gtfs(data_path)
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
TODO: This does not work, as Switzerland uses the new route_types 103 etc. Possible idea: To transform them before using them in gtfstools

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
# Auxiliary R things

## More links to stuff done in leaflet
* https://rpubs.com/walkerke/rstudio_gis
## Plotting with ggplot
https://www.earthdatascience.org/courses/earth-analytics/spatial-data-r/make-maps-with-ggplot-in-R/

## Running scripts from rgui
```R
source("where")
```
