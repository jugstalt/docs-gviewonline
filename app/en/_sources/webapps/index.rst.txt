gView WebApps
=============

**gView.WebApps** replaces **gView.Desktop** from versions *gView < 6*. To be platform-independent with 
the tools from *gView GIS*, the existing Windows applications have been converted into web applications. 
These apps (Carto, DataExplorer) now run in the browser. However, installation on a server is not 
necessary. The application **gView.WebApps** can also be launched locally. The application then runs 
in the browser at ``localhost`` (see the Installation section).

In this section, the individual components of *gView.WebApps* are briefly introduced. 
It discusses managing and converting vector data and raster catalogs, as well as creating 
simple maps. Knowledge of the fundamentals of geodata and 
GIS systems is assumed.

When you start the application (following the installation guide), there are different tiles,
which represent the individual apps:

.. image:: img/apps-01.png

.. note::

   If an Admin/Carto User was set up during installation, you must log in first. 
   Depending on the login, more or fewer tiles are available.

.. note::

   Clicking on a tile opens the app. If you want to open an *App* in a new window, 
   you can click on the *arrow icon* within the tile.

.. note::

   The ``Used Memory`` tile is only available to administrators. It displays the current
   memory usage. Clicking on the *icon* forces the *Garbage Collector* 
   to release memory (for diagnostic purposes only).

Although all apps can now be installed on a server via **gView.WebApps**, 
it should not be confused with a classic WebGIS. The applications should not be 
made available to a large user base on the internet, but rather serve to create 
maps with **gView.Server**. **gView.WebApps** is not *stateless*, 
scaling would be difficult.

If you want to offer GIS functionality to a large user base, you should use a 
WebGIS application that accesses **gView.Server** services. **gView.Server** 
is *stateless*, can be scaled as needed, and is optimized for performance.
  
.. toctree::
   :maxdepth: 2
   :caption: Table of Contents:

   carto/index
   dataexplorer/index