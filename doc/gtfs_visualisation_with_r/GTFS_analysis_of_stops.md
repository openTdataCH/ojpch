# Analysis of GTFS stops in R
Matthias Günter, GnostX GmbH


## Sources
* https://gis.stackexchange.com/questions/17638/clustering-spatial-data-in-r

## Installing

 '''R
 install.packages("geosphere")
 install.packages("rgdal")
 install.packages("rgeos")
 install.packages("dismo")
 '''
 
 ## Getting clusters for stops (distance)
  
 '''R
library(sp)
library(rgdal)
library(geosphere)

# Load GTFS file
xxx
# Load the matrix to work it
xy <-spo_gtfs$stops

# Transform xy into a SpatialPointsDataFrame
coordinates(xy) <- 3:4

# Calculate the distance matrix
mdist <- distm(xy)

# Set the distance to 40 meters
d=40

# Build the clusters
hc <- hclust(as.dist(mdist), method="complete")
xy$clust <- cutree(hc, h=d)

# Print out all clusters that are not simple (containing one element)

subset(xy,clust ==6)@data
subset(xy,clust ==6)@coords
'''

## Calculating grouping from the structure
