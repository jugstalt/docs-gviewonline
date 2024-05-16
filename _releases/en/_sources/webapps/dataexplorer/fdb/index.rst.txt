gView Feature Database
======================

*gView Feature Database* is a data format specifically developed for *gView GIS*. The data are stored in a database 
(MS SQL Server, Postgres, SQLite). For each data layer, a table is created in which the attribute data and the geometry are stored. 
The geometry is stored in a *Binary Blob*.

.. note::
   When the first version of *gView GIS* was developed, open geodatabases like PostGIS were not yet widespread. 
   Standards such as WKB (Well Known Binary) were also not yet established. The *gView Feature Database* is no longer 
   strictly necessary due to these geodatabases. It is recommended to use PostGIS or SQLServer as the data source for 
   mapping services, as these can also be processed by other GIS software packages.

.. note::
   If there is already an existing geodatabase (such as PostGIS), it should be used. Even if no geodatabase exists yet, 
   it is recommended to keep the data in a PostGIS database to remain open for other software packages.

The only relevance of the *gView Feature Database* now is to provide an offline GIS. For this purpose, the data of a 
gView map are saved in a *gView SQLite Feature Database*. This can then be taken "offline".
Compatibility with other GIS software packages is generally not a priority.

Furthermore, raster catalogs can also be created and easily managed in a *gView Feature Database*. For this reason, the description 
for the *gView Feature Database* was taken directly from the original manual 1:1 (screenshots are not from the current version).

If the data remains in the existing GIS database (e.g., PostGIS), this description can be skipped.

.. toctree::
   :maxdepth: 2
   :caption: Inhaltsverzeichnis:

   create
   raster_catalog


