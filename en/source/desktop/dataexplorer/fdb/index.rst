gView Feature Database
======================

*gView Feature Database* is a data format developed directly for *gView GIS*.
The data is stored in a database (MS SqlServer, Postgres, SQlite). For each data layer
a table is created in which the data and the geometry are stored.
The geometry is stored in a *Binary Blob*.

.. note::
   When the first version of *gView GIS* was developed, open geodatabases such as PostGIS were still 
   not widespread. Also, standards such as WKB (Well Known Binary) were not yet established. 
   The *gView Feature Database* is no longer absolutely necessary due to these geodatabases.
   We recommend that you use PostGIS or SQLServer as the data source for the map services because they are also
   can be processed by other GIS software packages.

.. note::
   If there is already an existing geodatabase (such as PostGIS), it should be used. Even if no geodatabase yet
   is available, it is recommended to keep the data in a PostGIS database in order to remain open to other software packages.

The *gView Feature Database* still has the only relevance to provide an offline GIS. For this purpose, the data of a 
gView map stored in a *gView SQLite Feature Database*. This can then be taken "offline".
Compatibility with other GIS software packages is usually not a prior for this scenario.

In addition, raster catalogs can also be created and easily managed in a *gView Feature Database*.
For this reason, the description for the *gView Feature Database* was taken from the original manual 1:1 (Screenshot
are not from the current version). 

If the data remains in the existing GIS database (e.g. PostGIS), this description can be skipped.

.. toctree::
   :maxdepth: 2
   :caption: Table of contents:

   create
   raster_catalog


