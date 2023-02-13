# ARCH 3102 - QGIS Workshop, Day 2
<https://kgjenkins.github.io/arch-3102-miami/>

Workshop 2023-02-13 by Keith Jenkins, GIS Librarian at Mann Library

For help after this workshop, contact me at kgj2@cornell.edu  
Or set up a Zoom appointment at <https://guides.library.cornell.edu/gis/help>

All the data and documentation for this workshop can be downloaded from:  
<https://github.com/kgjenkins/arch-3102-miami/archive/main.zip>


## Elevation raster data

Various scales of elevation data are available for different parts of the world.  For much of the globe, the best available is 30-meter SRTM data, collected as part of the Shuttle Radar Topography Mission in 2000.  Most of the United States is covered by the National Elevation Dataset (NED) with 10-meter pixels.  Some places have higher resolution data available from a local lidar survey.  For Miami, there was a 2018 lidar survey which was used to generated a high-resolution elevation raster with 5-foot pixels.

Due to the large size of these elevation datasets, the data has already been downloaded from <https://gis-mdc.opendata.arcgis.com/documents/MDC::2018-miami-dade-county-dem-5ft/about> and clipped to the area surrounding the focus neighborhoods (bringing the size down from 2.5 GB to just 75 MB).

* Open the `elevation` folder in your Windows file explorer or Mac finder
* Drag the `dem_5ft_clipped.tif' file onto your project

By default, raster data is displayed with a gray gradient, with higher values brighter.  We can change the style to bring out more details in the data.

* In the Layer Styling panel, change from "Singleband gray" to "Singleband pseudocolor"
* Try changing the color ramp to "Turbo" (click the small down-arrow to the right of the color bar)

It will be easier to adjust a small number of colorrs.  If you have more than 5-6 colors listed, try setting the Mode to "Quantile" and adjust the number of Classes to 6.

Notice the values assigned to each color.  You will probably want to adjust those values to better represent sea level.  Try the following, and notice how the map changes at each step:

* Double-click the value for light blue and set it to 0
* Set the green value to 0.01 and make it a dark green
* Set the orange value to 20

Zoom into an area along the water.  With the settings above, any value 0 or less will be bluish, and anything just above that (land) will be green.  The highest places around Miami are where the land has been built up for highway ramps and bridges.  Notice that there are some land areas along the Miami River that are actually below sea level, according to this dataset.


## Value Tool plugin

Although we could use the "Identify" tool to click pixels and see their values, the "Value Tool" plugin will allow you to instantly show the values from raster layers without even having to click.

* Plugins > Manage and Install Plugins...
* Search all plugins for "value tool" and install it

The Value Tool will appear on your toolbar as a green circle with white "i".

* Click the Value Tool button to open the panel
* Check the box to enable the tool
* Move your cursor across the map to view the raster values

Try checking the elevations for various parts of the map.  Note that the elevations are in feet (which could be verified by reading the metadata file.)


## Elevation hillshade

Even though we can see the differences between the highest and lowest parts of the country, the area along the coast looks rather flat and devoid of detail.  Let's use a hillshade to made it look more 3-dimensional.

* In the Layers Panel, right-click the "dem_5ft_clipped" layer > Duplicate
* Right-click "dem_5ft_clipped copy" > Rename to "hillshade"
* Drag the "hillshade" layer name just above "dem_5ft_clipped" and check the box to display the hillshade layer
* Open the styling panel be sure that you are styling the "hillshade" layer
* Change "Singleband pseudocolor" to "Hillshade"
* Set the blending mode to "multiply" (which will let us also see the colorized version underneath)

* Try turning the dial to adjust the angle of the "sun"

We can exaggerate the hillshade effect by increasing the Z Factor, which is usually the ratio between the vertical units (feet) and horizontal units (also feet).

* Set Z Factor to 2

Also notice that if you zoom in too far, you see individual pixel edges.  To avoid this effect:

* Set the resampling "Zoomed in" to "Bilinear" which will smooth out the pixels


## Generating contour lines

There is a "contours" display style for rasters, but is just a visual effect.  If we want to export 3D contour lines for use in a program like Rhino or AutoCAD, we will need to create a vector contour layer.

* Open the processing toolbox (gear icon, or in the Processing menu)
* Search for "contour" and open the "Contour" tool under GDAL > Raster extraction
* Set the following parameters:
  * Input layer = "elevation-1m"
  * Interval between contour lines = 2ft (this value should usually not be much less than the pixel size)
  * Check the box to "Produce 3D vector" (if you don't see it, expand the Advanced Parameters section)
* Keep the other options and click "Run"

To export the 3D contours to DXF, right-click the layer > Export > Save features as ... AutoCAD DXF or else export your whole project to DXF.


# Georeferencing historical maps

Georeferencing is the process of adding location information to an image so that it can be displayed in the correct location on a map, with the proper scale and rotation.  The process involves defining a set of control points that can be identified on both the image and a basemap.

The 1937 HOLC "redlining" map of Miami is a good example to start with.  This map shows a grading of neighborhoods for credit risk that were created by the federal government's Home Owners' Loan Corporation (HOLC).  These maps are notorious for being biased against neighborhoods having larger numbers of racial and ethnic minority residents, and prevented these populations from having equal access to bank loans.  The maps for Miami and many other US cities can be downloaded from the "Mapping Inequality" project:
<https://dsl.richmond.edu/panorama/redlining/#loc=5/39.1/-94.58&text=downloads>

Let's start by adding a satellite basemap and use its CRS to use as a reference:

* Web web > QuickMapServices > Bing > Bing Map
* Right-click the layer name > Layer CRS > Set Project CRS from Layer

Open and configure the Georeferencer:

* Raster menu > Georeferencer
* File > Open Raster...
  * data/historical-maps/holc-scan.tif
* Settings menu > Transformation settings...
  * Transformation type = Polynomial 1
  * Resampling method = Linear
  * Target SRS = EPSG:3857 (this is the CRS used by most QuickMapServices basemaps)
  * Compression = LZW (for a smaller output file)
  * Save GCP points (in case you want to adjust things later)
  * Check the box to open the output in QGIS

Find a spot that you can find on both the scanned map and the Google basemap, and add a ground control point:

* Select the "Add Point" tool
* Click a point on the scanned map
* In the dialog, click "From Map Canvas"
* Click the corresponding point on the Google basemap
* Repeat for several points scattered across the map.

More points generally leads to better georeferencing, but as you go, keep your eye on the "Residual" values in the table (which appear after 3 or more points have been added, depending on the transformation type) and try to keep these values as low as possible.  If one of your points has a residual much higher than the others, then you may want to delete that point and re-do it.  (Right-click the table row > delete)

When you think you have enough good points:
* Click the green triangle button to "Start Georeferencing"

It may take a moment to warp the image to fit the control points and output the file.  When done, it should display in the main QGIS window.

The `historical-maps` folder also has some Sanborn fire insurance maps from 1918.  These are a bit more difficult to georeference, because they cover a smaller area and it can be difficult to find the same location on the reference basemap due to numerous street name changes and the building of the interstate highways.