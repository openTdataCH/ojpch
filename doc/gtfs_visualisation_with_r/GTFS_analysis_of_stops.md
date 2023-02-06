# Analysis of GTFS stops in R
Matthias Günter, GnostX GmbH


## Sources
* https://gis.stackexchange.com/questions/17638/clustering-spatial-data-in-r
* https://stackoverflow.com/questions/32583606/create-spatialpointsdataframe (about SpacialPointsDataFrame generation)

## Installing

```R
 install.packages("geosphere")
 install.packages("rgdal")
 install.packages("rgeos")
 install.packages("dismo")
```
 
 ## Getting clusters for stops (distance)
  
```R
library(sp)
library(rgdal)
library(geosphere)
library(gtfstools)
library(sf)

# change the data path to your data path
data_path <- file.path("D:","development","gtfs_with_r", fsept="\\") 

#the spo_path should be set to files that are on your machine. This is a snapshot from mine (exactly one should be uncommented here
#Switzerland
spo_path <- file.path(data_path, "gtfs_fp2023_2023-01-18_04-15.zip")
spo_gtfs <- read_gtfs(spo_path)

# Load the matrix to work it
xy <-spo_gtfs$stops

# Transform xy into a SpatialPointsDataFrame
coordinates(xy) <- 3:4
proj4string(xy) <- CRS("+proj=longlat +ellps=WGS84 +datum=WGS84")

# Calculate the distance matrix
mdist <- distm(xy)

# Set the distance to 40 meters
d=40

# Build the clusters
hc <- hclust(as.dist(mdist), method="complete")
xy$clust <- cutree(hc, h=d)

# some info
# class
class(xy)
# bounding box
bbox(xy)


# Print out all clusters that are not simple (containing one element)
#
my_splits <- split(xy,xy$clust)

for (i in 1:length(my_splits)){

    o <- cbind(my_splits[[i]]@data,my_splits[[i]]@coords)
    if (nrow(o)>1){
       cat("\n\ngroup: ",i,"\n")    
       print(o)
       }
    }
```

## Calculating grouping from the structure in stop_areas.txt
(see https://gtfs.org/schedule/reference/#stop_areastxt)

**tbd**

## Calculating grouping from the structure in stops.txt
Using parent_station and location_type

**tbd**

# Generalised stop registry
The file contains an extended stops.txt as a master table 

Additional fields are:
- main_id: the id which this stop is grouped into (it is always flattened to a single parent - child relationship regardless how deep the structure is mapped. 
- source_ref: the source that the stop was loaded from (e.g. SBB-OeV)
- harvest_date: the date it was harvested (e.g. 2023-12-01)

Algorithm:
Identical stop_id are flattened into one, when the coordinates match. If the coordinates don't match, they are added.

```
1. Load the main database
2. Load a new GTFS file
3. For each element in the new GTFS file
     Try to use the grouping mechanism
     If a new entry (stop_id) is found
         If it has a master element
           Add it with a main_id
         Else
           Add it with itself as the main_id
         End If
     Else If coordinates are not identical to at least one incarnation of the group defined by the same main_id
        Add it with that main_id
     End If   
   EndFor
 4. Housekeeping: Remove items with the same stop_id,main_id,source_ref but an earlier harvest_date
 5. Save main database 
```

An optimisation could be that we try to define, which source is the master for a given area. This can be achieved by defining the area and check whether a given stop is within it or not. In such an instance the stops with the same main_id should be reorganised and the main_id should be the id of the master.


