# ARCH 3102 - QGIS Workshop, Day 1
<https://kgjenkins.github.io/arch-3102-miami/>

Workshop 2023-02-06 by Keith Jenkins, GIS Librarian at Mann Library

For help after this workshop, contact me at kgj2@cornell.edu  
Or set up a Zoom appointment at <https://guides.library.cornell.edu/gis/help>

All the data and documentation for this workshop can be downloaded from:  
<https://github.com/kgjenkins/arch-3102-miami/archive/main.zip>


## Some GIS Concepts

**GIS** stands for Geographic Information System (or Science)

"GIS data" refers to data with a spatial component -- it can be mapped!

**QGIS** (<https://qgis.org/>) is one of several popular GIS programs for mapping and spatial analysis.  It is free, open-source desktop software that runs on Windows, Mac, and Linux.  QGIS is created by developers around the world, supported by municipal and national governments, corporations, user groups, and individual users.  Discussions about new features, bug fixes, and future directions happen on [e-mail lists](https://qgis.org/en/site/getinvolved/mailinglists.html) and the [project's GitHub organization](https://github.com/qgis).

Questions about how to use QGIS are typically asked and answered on [GIS StackExchange](https://gis.stackexchange.com/).  If you google for QGIS questions, there is a good chance you'll end up finding the answer already there, so always be sure to search before asking a new question.

**Vector** = points, lines, polygons
Common file formats are GeoPackage (.gpkg) and Shapefile (.shp, .dbf, .prj, etc.)

**Raster** = pixels (images, but also data)
Common file format is GeoTIFF (.tif)

Vector data does not usually contain any information about how to style the display of the data.

Styles are added in a map project.  The same data could be styled in different ways in different maps, or even in the same map!

The QGIS project file .qgz contains your styles and pointers to the data, but does not include the data itself.  I recommend keeping everything within a containing folder (there can be subfolders) that you can zip up or save to a device or the cloud.  Something like this:

* myproject/
  * data/
    * boundaries/
      * counties.gpkg
      * states.gpkg
    * buildings.gpkg
    * streets.shp
  * notes-about-sources.txt
  * mymap.qgz

**CRS** = Coordinate Reference System

Ideally, CRS details are quietly handled behind the scenes, so we barely even need to think about it.

But in the real world, you'll eventually run into CRS puzzles and problems, and CRS becomes even more important when doing distance- or area-based analysis.  Here are the CRS we'll be using today:

* EPSG:4326 = WGS 84 -- "common longitude/latitude"
* EPSG:3857 = WGS 84 / Pseudo-Mercator -- also called "Web Mercator", uses false "meters" (only true at equator)
* EPSG:6439 = NAD83(2011) / Florida GDL Albers -- a projected CRS based on meters that can be used for Florida as a whole
* EPSG:32617 = WGS 84 / UTM zone 17N -- a projected CRS based on meters that is good for Miami


## Administrative Boundaries

Each country in the world is typically subdivided into smaller units like US states, which are then subdivided further into something like US counties, although the names of these subdivisions vary from country to country.  Since this class is focusing on the City of Miami, Florida, we don't really need country or state boundaries.  Instead, we will use a dataset of neighborhood boundaries provided by the city government.

These boundaries are provided in a shapefile format, which is probably the most common geospatial data format found on the Internet, but the shapefile format dates from the early 1990s, which is why there are multiple component files (.shp, .dbf, .shx, .prj, and sometimes others).  To add a shapefile to QGIS, we just need to select the .shp file and QGIS will take care of the rest.  Shapefiles also have other quirks, mostly notably a 10-character limit for attribute names.  Learn more at <http://switchfromshapefile.org/>

Load the region boundaries:
* Look in the `data/city` folder and drag the `Miami_Neighborhoods_Shapefile.shp` file onto the QGIS window

Change the layer style by opening the Layer Styling panel – colorful paintbrush icon at top left of the Layers panel
* Change the color
* Click "Simple fill" to change other properties, like stroke color and width

Get information about a polygon:
* Click "Identify Features" tool, then click a polygon
* To get rid of the red highlighting, click a blank space on the map, or click the yellow and red "Clear Results" icon at the top of the "Identify Results" panel

View information about all the polygons as a table:
* Right-click layer name in Layers pane > Open Attribute Table
* Click on a column header to sort by that column

The table and map are linked, so if you select 'Demerara-Mahaica' from the table (by clicking the row number) you should then see it highlighted in yellow on the map.  There is a toolbar button to "Deselect features from all layers".

Add labels by clicking the label tab (yellow "abc") in the styling panel
* Single Labels, value = NAME
* 9 tabs of options!
* 1st tab (text) - font, size, color
* 2nd tab (formatting) - wrap lines to 12 characters
* 8th tab (placement) - mode = offset from centroid

To find out what CRS (coordinate reference system) is being used:
* Right-click > Properties...  Source tab

Since this class is focusing on a few specific neighborhoods, I added a field called "focus", which will contain a `1` for those neighborhoods.  We can use these values to give those polygons a distinct style.

* At the top of the layer styling panel, change "Single symbol" to "Categorized"
* Set Value = `focus`, then click the "Classify" button towards the bottom of the panel

Random colors are assigned to each of the values (1 or 0), which are probably not the colors that we want.  Let's change the style for the focus neighborhoods:
* Double-click the color swatch next to the `1` value
* Click the color bar and pick a color

Change the styl for the other neighborhoods:
* Double-click the color swatch next to the `0` value
* Click "Simple Fill"
* Change the Fill style to "No Brush"

This neighbor layer will help us stay oriented as we add other data layers.


## Save your project!

Save "mymap.qgz" to the folder that also contains your data -- for example, within the "workshop1" folder. That way, you can zip up or move the containing folder around while keeping your map and data intact. The .qgz project file contains your map styles and pointers to your data files, but not the data itself.


## Roads

Roads are commonly represented as centerlines -- one line per road, regardless of how many lanes it has.  OpenStreetMap (OSM) is widely regarded as the best and most complete set of street data worldwide, but in this case, Miami-Dade County has a good dataset of roads

* Look in the `county/Street` folder, and drag the `Street.shp` file onto QGIS
* Explore the data a bit (identify tool, attribute table)

This particular dataset has a `CLASS` attribute field that contains numbers 0 to 9.  We can use those values to control the layer style and give prominence to the major roads.

* At the top of the layer styling panel, change "Single symbol" to "Categorized"
* Set Value = `CLASS`, then click the "Classify" button towards the bottom of the panel

Random colors are assigned to each of the values, which is not really what we want.  We'll use a greyscale color ramp that applies a darker style to the lower `CLASS` numbers, which are major highways.

* Change the "Color ramp" to "Greys"
* Right-click the color ramp > Invert Color Ramp

The street dataset has street names, so we could use that info to label the roads, but it can require a lot of work and manual adjustments to make it look really good, so we'll use a basemap instead.


## Basemaps via QuickMapServices

Basemaps are web-based map images designed by professional cartographers who have already done the hard work of aggregating different data layers and customizing styles and labels to work at different zoom levels.  Basemaps are usually global in scope, although there are some that only focus on certain regions.  They can be used to add context to your map, or just to help confirm that your data is correctly aligned.  The QuickMapServices plugin makes this easy, but we need to install it first.

* Plugins menu > Manage and Install Plugins...
* Scroll down, or search all plugins for "quickmap" (you don’t need to type the whole name)
* Select the QuickMapServices plugin
* Click the "Install plugin" button (it installs in seconds)
* Click "Close"

When you first install QuickMapServices, you'll want to get the full set of basemap definitions.

* Web menu > QuickMapServices > Settings
* "More services" tab > "Get contributed pack"

To add the Google Hybrid basemap, which combines aerial photos with placename labels:

* Web menu > QuickMapServices > Google > Google Hybrid

Most basemaps are in a different projection (CRS) called Pseudo- or Web-Mercator, EPSG:3857.  QGIS is reprojecting it to match the CRS of the first dataset we loaded, which can cause some pixelation, making the labels hard to read.  In this workshop, our first dataset (the boundaries) were already in EPSG:3857, so it should not be a problem now, but if you ever need to avoid the pixelization, you can set our map to use the basemap CRS:
* Right-click the Google layer > Set CRS > Set Project CRS from Layer
* Right-click the Google layer > Zoom to Native Resolution (100%)

Turn off your admin boundary layers, and adjust the style of your roads layer so that they are clearly visible on the map, and explore how well it aligns with the imagery.  (Try zooming in to individual intersections.)

As we add more data, it may help to use a plainer basemap:
* Web menu > QuickMapServices > Stamen > Stamen Toner Lite
* Uncheck the Google Hybrid layer to hide it
* Adjust the other layer styles as needed (white buffer, for example)

The Stamen Toner Lite layer is very light, so we can add our own roads on top if we want to emphasize the full road network.  You may find that our local street layer may not exactly match from the Stamen layer, which is common when comparing data from different sources.

* In the streets style panel, you can adjust Layer Rendering > Opacity to control the visual weight of the streets layer.

If you zoom out, you may see that the highlighted neighborhood polygons are obscuring the roads below.  Here's one way to fix that:

* In the style panel, select the `Miami_Neighborhoods_Shapefile` layer (at top of panel)
* At the bottom of the style panel, expand the "Layer Rendering" section
* Set the Blending Mode for the layer to "Multiply"


## Extracting a data subset

Notice that the streets dataset covers the whole county (even if most of the roads are near the coast).  If we only wanted the streets around our focus neighborhoods, we could use select a subset of streets and save them to a new file.

* Click the "Select Features by area or single click" tool in the toolbar (the 1st of three yellowing icons in a row)
* Click the "Street" layer name, then drag a rectangle to select an area containing the focus neighborhoods

* Right-click the "Street" layer > Export > Save Selected Features As...
* Set Format = GeoPackage
* Don't just type in a file name! as we don't quite know where it would be saved!  Always click the '...' button to browse to where you want to save the file.  In this case, save it to the 'data' folder as 'streets-subset.gpkg'
* Ignore the other options, and click 'OK' to save.

The exported file will be added to your map.  Turn of the original streets layer to verify the subset.


## Exporting your map to an image file

To export the current map view to an image file:
* Project menu > Import/Export > Export Map to Image...
* Update the extent or leave as is
* Increase the resolution (try 300dpi)
* Save as a .png file


## Exporting your map to a PDF file

Exporting to PDF is useful if you are going to print your map, or if you want to do further work in a program like Inkscape or Adobe Illustrator.  The PDF will keep separate layers for each data layer.  Vector layers will remain vectors, and raster layers will remain raster.

* Project menu > Import/Export > Export Map to PDF...
* Check the settings, especially the resolution if your map includes raster layers, including basemaps


## Exporting data layers to a DXF file (for CAD software)

Architects often want to export GIS layers for use in a CAD program like Rhino or AutoCAD.  Since DXF is a vector format, only vector layers can be exported -- sorry, no basemaps!  When exporting to CAD, be sure to set an appropriate CRS for the exported data, which means a CRS based on meters or feet -- not degrees longitude/latitude.
* Project menu > Import/Export > Export Project to DXF...
* Click the "..." button to specify where to save the .dxf file
* Be sure to select an appropriate CRS!  QGIS will reproject all your layers to the chosen CRS.  A good CRS option for Miami is:
  * EPSG:32617 = WGS 84 / UTM zone 17N

**Do NOT** export to DXF using any of these CRS, or you will see distortions and incorrect measurements:
* EPSG:4326 = WGS 84 -- "common longitude/latitude"
* EPSG:3857 = WGS 84 / Pseudo-Mercator -- also called "Web Mercator", uses false "meters" (only true at equator)

If you have data that extends beyond your area of interest, and don't want to export it all to DXF, try checking the "Export features intersecting the current map extent".  (You may need to cancel if you need to adjust the map extent, and then open the export dialog again.)


## Explore other datasets

The 'data' folder contains several other datasets that have been compiled for this workshop.  If we have time, we'll explore those datasets.

When styling the dep/nhd-flowlines and -waterways, try the following:
* make the lines and polygons the same color of blue
* make the polygon stroke the same color as the fill
* set to line thickness to 1mm

## Coming next week... on QGIS Workshop Day 2

* elevation data - pseudocolor, hillshades, and contours
* georeferencing scanned maps

There are many more datasets in the Box folder, so sure to explore what is available:
<https://cornell.app.box.com/folder/193487073667>