gView.Cmd.MxlUtil
=================

This tool provides some methods to automate recurring tasks with map project files (MXL):

* **PublishService**: Publish a map to *gView MapServer*.
* **MxlDataset**: View and customize connection properties of an MXL file
* **MxlToFdb**: Copy all vector data of a map project in a *gView Feature Database*. *gView FDB* is a format for which *gView* guarantees high performance. The database is created here in *SQL Server*, *PostGre* or *Sqlite*.
   This tool can be used to provide map projects *offline* (all data in a *Sqlite* database) 

   The general call for this tool is:

.. code::

   gView.Cmd.MxlUtil.exe <utiltiy-name> [parameters]

As a ``utility name`` you can use one of the options listed here.

PublishService
--------------

To publish map services, the following parameters can be passed:

.. code::

  .\gView.Cmd.MxlUtil.exe publishservice

  PublishService
  --------------

  Publish Mxl as gView Map Service

  Required arguments:
  -mxl <mxl-file>
  -server <gview-server-url>
  -service <servicename incl. folder ... folder/servicename>

  Optional arguments:
  -client <clientname>
  -secret <clientsecret>

* ``-mxl``: Pfad zum XML File
* ``-server``: Url der gView Instanz, über die der Dienst publiziert werden sollte
* ``-service``: Name des Services: ``{Verzeichnis}``/``{Dienstname}``

Example:

.. code::

    .\gView.Cmd.MxlUtil.exe publishservice -mxl c:\my-map.mxl -server https://localhost -service produktiv/my-service

The optional parameters can be used to pass a *client* through which the service should be published.
As a rule, you should not allow every user to publish services. To control this, the administrator/manager of the *gView MapServer* 
can create clients (in the Manager Web interface under ''Security''). For these clients, rights can be granted at the service and directory level (lock symbol). 
Clients who are given the right to ``publish`` for a directory are allowed to publish services.
 
.. note::
   If no rights are set for a directory, all rights are assigned to each user.

.. code::

    .\gView.Cmd.MxlUtil.exe publishservice -mxl c:\my-map.mxl -server https://localhost -service produktiv/my-service -client publisher -secret pa3sw0rd

.. note::
   *only* a valid *client* can be specified via the command line. The administrator cannot be passed here.
   If you want to publish services *securely* via the command line, a client must be created!
    
MxlDatasets
-----------

Here the connection parameters of the datasets can be displayed or changed within an XML file. A use case
can be when developing maps with connections to a (local) test database. Before (automated) publishing
here the connection properties can be rewritten to a productive database.

Call:

.. code::
  
   .\gView.Cmd.MxlUtil.exe mxldatasets
    
   Required arguments:
   -mxl <mxl-file>
   -cmd <info|modify-cs|modify-connectionstring>

   Optional arguments:
   -out-xml <name/path of the out xml>

   Commands:
   info
   ----
   Shows all dataset connections and connection string parameters in the mxl file.

   modify-cs|modify-connectionstring
   ---------------------------------
   Changes the value of an parameter in a connection  in the mxl file.

   Required arguments:
   -parameter|-parameter-name <name of the parameter>
   -new-value|-new-parameter-value <set this value for the parameter>

   Optional arguments:
   -dsindex|-dataset-index <the index of the dataset you want change the parameter> default = -1 => all datasets 


* ``-mxl``: Path to the MXL file
* ``-cmd``: Further specification of the command.
 
Optional:
 
* ``-mxl-out``:  Path to an MXL that should be created (modify-connectionstring-only). If no Output XML is specified, the **original file** will **overwritten**!
    
**Command - Info**

This command is the default. If this or no command is passed, the connection parameters of each Dataset will be displayed:

.. code::

   .\gView.Cmd.MxlUtil.exe mxldatasets -mxl C:\gview5\mxl\my-map.mxl
    
   Dataset 0
   ==============================================================================
   Type: gView.DataSources.MSSqlSpatial.DataSources.Sde.SdeDataset


   ConnectionString:
   ------------------------------------------------------------------------------
   Server=testdbserver
   Database=gisdb
   User Id=DB_READ
   Password=*************

   In the collection of datasets, each dataset gets a number (here ``0``). If you only want one parameter for a specific 
   To change the dataset, this must be specified with the parameter ``-dsindex`` (see below).
   
**Command - modify-cs|modify-connectionstring**

With this command a *Connection parameters* can be changed. In addition to the parameters listed above, the following 
Parameters are passed:

* ``-parameter|-parameter-name``: name of the parameter (e.g. ``server``)
* ``-new-value|-new-parameter-value``: the new value for the parameter
  
If several parameters are changed, the parameters must be repeated in the command line.

Optional:

* ``-dsindex|-dataset-index``: If only one specific dataset is changed, the index number of the dataset can be specified here.
   The index number can be taken from the ``info`` command shown above. If the parameters are not specified, the parameters for 
   all datasets changed.

Example:

.. code::

    .\gView.Cmd.MxlUtil.exe mxldatasets -mxl C:\gview5\mxl\my-map.mxl -cmd modify-cs -parameter Server -new-value proddbserver -parameter password -new-value ProdPa3sw0rd -out-mxl C:\gview5\mxl\my-map-produktiv.mxl


.. note::
   The connection parameters are checked both when opened and overwritten. If no connection is possible with the given parameters,
   cancels the program.
   If an MXL is damaged and a connection cannot be established with the connection parameters, this tool cannot be used.
   In this case, the MXL file must be repaired via a text editor.

MxlToFdb
--------

Copy all vector data of a map project in a *gView Feature Database*. *gView FDB* is a format for which *gView* guarantees high performance. The database is created here in *SQL Server*, *PostGre* or *Sqlite*.
This tool can be used to provide map projects *offline* (all data in a *Sqlite* database) 


.. code::

   .\gView.Cmd.MxlUtil.exe mxltofdb

   MxlToFdb
   --------

   Copies all vector data in an MXL file to an FeatureDatabase (fdb) [SqlServer, PostGres or Sqlite).
   The result is a new MXL file with the same symbology in changed connections to the new FeatureDatabase.

   Example: Use this utitiity to make an existing database driven MXL to an 'offline' file driven (Sqlite)
   MXL.

   Required arguments:
   -mxl <mxl-file>
   -target-connectionstring <target fdb connection string>
   -target-guid <guid or sqlserver|postgres|sqlite>

   Optional arguments:
   -out-xml <name/path of the out xml>
   -dont-copy-features-from <a comma seperated list of layernames, where only an empty Db-Table-Schema is created>


* ``-mxl``: path to the MXL file
* ``-target-connectionstring``: connection string to the target Feature Database
* ``-target-guid``: GUID of the target database plugin or simply ``sqlserver|postgres|sqlite`` 
 
Optional:
 
* ``-mxl-out``: Path to an MXL that should be created. If no Output XML is specified, the original file is overwritten!
* ``--dont-copy-features-from``: A list of layers that should not be copied. The tool will mainly be used to make existing maps *offline* 
   by writing the data to a SQLite database. If (large) data sets of a map *offline* not absolutely necessary, 
   they can be specified here. Although the schema of these tables is created in the target database, no data is copied.
  
Example:

.. code::

   .\gView.Cmd.MxlUtil.exe mxltofdb -mxl C:\gview5\mxl\my-map.mxl -target-connectionstring: c:\offline.fdb -target-guid sqlite -out-mxl C:\gview5\mxl\my-map-offline.mxl -dont-copy-features-from bigdata-layer1,bigdata-layer2

