# Point-Cloud-Data_analysis
# Load necessary libraries /of course after installed.
library(lidR)
library(terra)
library(ggplot2)
library(rgl)

# Read the LAS file
las <- readLAS("G:/PhD Courses/Project_Report/33.las")

# Print and summary of the data
print(las)
summary(las)

# Check the point cloud's coordinate reference system (CRS)
crs(las)

# Get a basic summary of point attributes (Z, Intensity, Return Number)
summary(las$Z)
summary(las$Intensity)
table(las$ReturnNumber)

plot(las, color = "Z", axis = TRUE,  legend = TRUE, bg = "white")

# This  interactive window you can rotate and zoom
plot(las, bg = "white", size = 3)

# Classify ground points using the Progressive Morphological Filter (pmf) algorithm
# PMF is an algorithm that progressively analyzes the point cloud at different scales 
# to separate ground points from non-ground points. (** is take long time ).
las <- classify_ground(las, algorithm = pmf(ws = 5, th = 3)) 

# Plot the results, coloring by classification
plot(las, color = "Classification", size = 3)

# Generate a Digital Terrain Model (ground only)
dtm <- rasterize_terrain(las, res = 1, algorithm = tin())

# Generate a Digital Surface Model (highest points, including vegetation/buildings)
dsm <- rasterize_canopy(las, res = 1, algorithm = dsmtin())

# Plot them side-by-side
par(mfrow = c(1, 2))
plot(dtm, main = "Digital Terrain Model (DTM)")
plot(dsm, main = "Digital Surface Model (DSM)")

# Calculate the CHM (Canopy Height Model)
chm <- dsm - dtm

# Plot the CHM
plot(chm, main = "Canopy Height Model (CHM)", szie = 6)
