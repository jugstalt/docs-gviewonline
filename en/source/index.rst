
.. gView Online documentation master file, created by
   sphinx-quickstart on Thu Jul 25 18:00:17 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to gView GIS
====================

gView GIS is an easy-to-use `Open Source`_ GI framework for visualizing and managing spatial data (geospatial data). 
In addition to displaying the data, gView GIS supports a lot of other features, such as layer control, legend design, 
spatial queries, labeling, measure distances and surfaces, and much more.

The entire framework is programmed in .NET and can be extended with all .NET languages via plug-ins. The spectrum of these extensions 
includes, for example, custom tools, renderers for displaying map objects, symbolism, 
Data sources for vector and raster data, as well as interfaces for exporting the maps through the gView Map Server. 

The components of the gView Framework can be divided into two categories:

* **Desktop GIS:** These are the *gView Carto* and *gView DataExplorer* applications designed for the Windows Desktop. 
  Using these applications, maps can be created or geodata can be managed.


* **gView Server** is a map server that publishes the created maps as map services.
  In addition to OGC standards (WMS, WFS), the services are also available in formats such as GeoServices REST and ArcXML. This makes it 
  easy to view and query the services in any popular GIS software.

* **gView Online:** With this `Plattform`_, gView is offered as a cloud service. This means that there is no installation of the map server in the
  data center is necessary. The maps are first created on the desktop with *gView Carto* and then published to the cloud.
  The prerequisite for this is that gView Online can access the geo database. If you also host a geo-database in the cloud,
  it is no longer necessary to create your own server infrastructure for publishing map services.
 
 .. _`Open Source`: https://github.com/jugstalt/gview5
 .. _`Plattform`: https://gviewonline.com

.. toctree::
   :maxdepth: 2
   :caption: Table of contents:

   formats
   desktop/index
   server/index
   online/index


