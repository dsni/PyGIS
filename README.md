# PyGIS

#----------------------------------------------------------------------------------------------------------------------------------------
#Part 1. List directory & create baselayer, load local .shp files
#----------------------
from PyQt4.QtCore import *
#PyQt4.QtCore -->  QFileInfo
from PyQt4.QtGui import * 
# PyQt4.QtGui --> QApplication
import qgis.core
#qgis.core --> QgsProject, QgsComposition, QgsApplication, QgsProviderRegistry
import qgis.gui #adds additional gui components 
#qgis.gui --> QgsMapCanvas, QgsLayerTreeMapCanvasBridge
from glob import glob
import qgis.utils #allows interface with stuff
import os
#os --> os.file.path #allows filepath manipulations
import sys
import PyQt4.QtXml
#PyQt4.QtXml --> QDomDocument
#-----------------------------
pathToFiles = 'C:\Users\Alison Keenan\Desktop\DVC_qGIS' #'_____________________'
base_name = 'DVCBoundary_Corrected' # , '_____________________' #copy,paste file name from list in python console!
#----------------------------

#===================================
# 1.1. List files in directory
#===================================
  
for file in os.listdir(pathToFiles):      
    if file.endswith('.shp'):  
		print file  

#===================================
# 1.2. Create baselayer
#===================================

format = '.shp'
full_file_location = os.path.join(pathToFiles , base_name + format)
print full_file_location #copy and paste into finder to confirm that this is the correct file!

new_layer_name = base_name

baselayer = QgsVectorLayer(full_file_location, new_layer_name, 'ogr')
if baselayer.isValid():
    QgsMapLayerRegistry.instance().addMapLayer(baselayer)
if not baselayer.isValid():
    print "Layer failed."
else:
    print "Hurray! Layer Loaded."			
			
#==================================
# 1.3. Create Canvas map
#==================================
import sys
from qgis.gui import QgsMapCanvas, QgsLayerTreeMapCanvasBridge
import os
from qgis.core import QgsProject
from qgis.core.contextmanagers import qgisapp
from PyQt4.QtCore import QFileInfo

# Get all layer names
allLayers = QgsMapLayerRegistry.instance().mapLayers()
for name,layer in allLayers.iteritems():
    print layer.name()
	
#show and set map canvas
canvas = QgsMapCanvas()

canvas.setCanvasColor(Qt.white)
#canvas.setCanvasColor(Qt.red)
canvas.enableAntiAliasing(True)

# set extent to the extent of our layer
canvas.setExtent(baselayer.extent())

# Load our project
bridge = QgsLayerTreeMapCanvasBridge(QgsProject.instance().layerTreeRoot(), canvas)
#QgsProject.instance().read(QFileInfo(project_path))
bridge.setCanvasLayers()

canvas.show()

#----------------------------------------------------------------------------------------------------------------------------------------
#Part 2. Select local .shp file and load as vector layer 
#----------------------
from PyQt4.QtCore import *
#PyQt4.QtCore -->  QFileInfo
from PyQt4.QtGui import * 
# PyQt4.QtGui --> QApplication
import qgis.core
#qgis.core --> QgsProject, QgsComposition, QgsApplication, QgsProviderRegistry
import qgis.gui #adds additional gui components 
#qgis.gui --> QgsMapCanvas, QgsLayerTreeMapCanvasBridge
from glob import glob
import qgis.utils #allows interface with stuff
import os
#os --> os.file.path #allows filepath manipulations
import sys
import PyQt4.QtXml
#PyQt4.QtXml --> QDomDocument
#-----------------------------
pathToFiles = 'C:\Users\Alison Keenan\Desktop\DVC_qGIS' #'_____________________'
file_name = 'DVC_Parks' # , '_____________________' #copy,paste file name from list in python console!

# Local Layers: ['DVC_Parks' , ] 
#----------------------------

format = '.shp'
full_file_location = os.path.join(pathToFiles , file_name + format)
print full_file_location #copy and paste into finder to confirm that this is the correct file!

new_layer_name = file_name

layer = QgsVectorLayer(full_file_location, new_layer_name, 'ogr')
if layer.isValid():
    QgsMapLayerRegistry.instance().addMapLayer(layer)
if not layer.isValid():
    print "Layer failed."
else:
    print "Hurray! Layer Loaded."

#----------------------------------------------------------------------------------------------------------------------------------------
# Part 3. Add vector layer from MassGIS WFS
#----------------------
from PyQt4.QtCore import *
#PyQt4.QtCore -->  QFileInfo
from PyQt4.QtGui import * 
# PyQt4.QtGui --> QApplication
import qgis.core
#qgis.core --> QgsProject, QgsComposition, QgsApplication, QgsProviderRegistry
import qgis.gui #adds additional gui components 
#qgis.gui --> QgsMapCanvas, QgsLayerTreeMapCanvasBridge
from glob import glob
import qgis.utils #allows interface with stuff
import urllib #makes uri path
import os
#os --> os.file.path #allows filepath manipulations
from owslib.fes import * #WFS stuff
from owslib.etree import etree #WFS stuff
from owslib.wfs import WebFeatureService #WFS stuff
import sys
import PyQt4.QtXml
#PyQt4.QtXml --> QDomDocument
#-----------------------------
pathToFiles = 'C:\Users\Alison Keenan\Desktop\DVC_qGIS' #'_____________________'
wfs_layer_name = 'massgis:GISDATA.LIBRARIES_PT' #'_________________________' #add the layer name

# wfs_layer_name = ['massgis:GISDATA.LIBRARIES_PT' , 'massgis:GISDATA.MBTABUSROUTES_ARC' ] # Add the layer names in list format
#----------------------------

#===================================
# 3.1. Add MassGIS WFS
#===================================
#http://giswebservices.massgis.state.ma.us/geoserver/wfs?
#===================================
# 3.2. List files on MassGIS WFS 
#===================================

wfs = WebFeatureService(url='http://giswebservices.massgis.state.ma.us/geoserver/wfs', version='1.1.0')
contentlist = list(wfs.contents)
print '\n'.join(contentlist)

#===================================
# 3.3. Select and Load WFS Layer
#===================================

url = 'http://giswebservices.massgis.state.ma.us/geoserver/wfs?' 

params = [
	('SERVICE', 'WFS'),
    ('VERSION', '1.0.0'),
    ('REQUEST', 'GetFeature'),
	('TYPENAME', wfs_layer_name), 
	('SRSNAME', 'EPSG:26986')]

uri_parms = url + urllib.unquote(urllib.urlencode(params))
print uri_parms

new_layer_name = wfs_layer_name

layer = QgsVectorLayer(uri_parms, new_layer_name, "WFS")
if layer.isValid():
    QgsMapLayerRegistry.instance().addMapLayer(layer)
if not layer.isValid():
    print "Layer failed."
else:
    print "Hurray! Layer Loaded."

#----------------------------------------------------------------------------------------------------------------------------------------		
# Part 4. Export Canvas
#----------------------------
from PyQt4.QtCore import *
from PyQt4.QtGui import *
import os
#os --> os.file.path #allows filepath manipulations
#----------------------------
# PARAMETERS
mainPath = 'C:\Users\Alison Keenan\Desktop\DVC_qGIS' 
filename = 'filename'
imageType = '.png'
#----------------------------

imageWidth_mm = 400
imageHeight_mm = 160
dpi = 300

map_settings = iface.mapCanvas().mapSettings()
c = QgsComposition(map_settings)
c.setPaperSize(400, 160)
c.setPrintResolution(dpi)

#set page background to transparent
transparent_fill =QgsFillSymbolV2.createSimple({ 'outline_style': 'no', 'style': 'no'})
c.setPageStyleSymbol( transparent_fill )

x, y = 0, 0
w, h = c.paperWidth(), c.paperHeight()
composerMap = QgsComposerMap(c, x ,y, w, h)
composerMap.setBackgroundEnabled(False)
c.addItem(composerMap)

dpmm = dpi / 25.4
width = int(dpmm * c.paperWidth())
height = int(dpmm * c.paperHeight())

# create output image and initialize it
image = QImage(QSize(width, height), QImage.Format_ARGB32)
image.setDotsPerMeterX(dpmm * 1000)
image.setDotsPerMeterY(dpmm * 1000)
image.fill(Qt.transparent)

imagePainter = QPainter(image)

c.setPlotStyle(QgsComposition.Print)
c.renderPage( imagePainter, 0 )
imagePainter.end()

imageFilename =  os.path.join(pathToFiles , filename + imageType)
print imageFilename

image.save(imageFilename)
print 'image saved'
print 'done'


