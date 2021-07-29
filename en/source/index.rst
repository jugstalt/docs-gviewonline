
.. gView Online documentation master file, created by
   sphinx-quickstart on Thu Jul 25 18:00:17 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

.. note::
  This documentation was based on the *gView documentation* in German
  Language.
  The translation was (semi) automated. 
  Feel free to contribute! More inforation in the document footer.

Welcome to gView GIS
====================

gView GIS is an easy-to-use `Open Source`_ GI framework for publishing Map- and FeatureServices. 
The maps are created in a Windwos desktop program *gView Carto*. The maps consist of layers that can be based on different geodata sources.
The individual layers can be colored and/or labeled as desired.

The maps can be published via the *gView Server*. The resulting map and feature services can then be accessed via defined interfaces (WMS, WMTS, GeoServices REST...)

Positioning of gView GIS
------------------------

*gView GIS* is not to be understood as a separate GIS platform. It simply provides an easy-to-use authoring tool (*gView Carto*) for creating maps with attractive 
cartographic representation and a high-performance map server for publishing these maps. These maps are thus available in different sections and can be integrated into an existing WebGIS, for example.

*gView GIS* is not to be understood as desktop software with which geo-data can be visualized/analyzed. For these tasks, there are already powerful open source projects such as https://www.qgis.org

A recommended productive constellation for the use of *gView GIS* would be, for example:

.. code::

   +-- Datenbank ----+     +-- Karten-/FeatureServer -+     +-- GIS Client --+
   |                 |     |                          |     |                |
   |   PostGIS,      | <=> |   gView MapServer        | <=> |   WebGIS,      |
   |   SQL Server    |     |                          |     |   QGis, ...    | 
   +-----------------+     +--------------------------+     +----------------+
                                       ^
                                       |
                           +--------------------------+
                           |                          |
                           |  gView Carto:            |  
                           |  Used to create maps     |
                           |                          | 
                           +--------------------------+         

The two components ``Database`` and ``Map/FeatureServer`` can be located here on a server and the ``GIS Client`` can run locally on the desktop or in a browser.
*gView Carto* is also installed locally on a desktop. The created maps are published in the *gView MapServer*. The *gView MapServer* also has a security layer,
which can be used to determine which client is allowed to display, query or edit maps.


Another application of *gView GIS* is the creation of offline GIS solutions. These can be installed on any (Windows, Linux, MacOS) device and can be 
serve as a failure/emergency system or offline system (laptop).
For this purpose, *gView GIS* offers the possibility to transfer all vector data of a map into a SQLite databnk via the command line tools. This eliminates the database layer and the SQLite database 
can be easily copied to the target device. All you have to do is install the *gView MapServer* and the corresponding client software on the target device. The *gView MapServer* can 
run as a *standalone* application on the target device as shown in the installation guide.
           

Components of gView GIS
-----------------------

The components of the gView Framework can be divided into two categories:

* **Desktop GIS:** the programs *gView Carto* and *gView DataExplorer*, which were developed for the Windows desktop. 
  With the help of these programs, maps can be created or geodata can be managed.
  The base for both programs is **.NET Framework 4.7.2**. The software can only be run on Windows systems.

* **Server GIS:** The gView Server is a map and feature server that allows the created maps to be published as map services.
  In addition to OGC standards (WMS, WFS), the services are also available in formats such as GeoServices REST and ArcXML. This makes it possible to 
  view and query the services in any popular GIS software.
  The base is **.NET Core 3.1**. The server runs on Windows, Linux and MacOS


* **gView Commandline Tools:** A collection of command line tools that can be used to automate tasks in the *gView GIS* environment (e.g. publish map for the server)
  The base is **.NET Core 3.1**. The server runs on Windows, Linux and MacOS
 
 .. _`Open Source`: https://github.com/jugstalt/gview5
 .. _`Plattform`: https://gviewonline.com

.. toctree::
   :maxdepth: 2
   :caption: Table of contents:

   setup/index
   desktop/index
   server/index
   commandline/index
   examples/index
   appendix/index


