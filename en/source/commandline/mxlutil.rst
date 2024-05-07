gView.Cmd.MxlUtil
=================

This tool provides several methods to automate recurring tasks associated with 
map project files (MXL):

* **PublishService**: Publish a map on the *gView MapServer*.
* **MxlDataset**: Display and adjust connection properties of an MXL file.
* **MxlToFdb**: Copy all vector data of a map project into a *gView Feature Database*. 
  The *gView FDB* format ensures high performance. The database is created in *SQL Server*, 
  *PostGre*, or *Sqlite*.
  This tool can be used to make map projects available *offline* 
  (all data in a *Sqlite* database).

.. note::

    The following commands are shown in **interactive mode**. For this, ``gView.Cmd.exe -i``
    must be called. Without the *interactive mode*, ``gView.Cmd.exe --command`` would have to be 
    prefixed to each command shown here.

The general call for this tool is:

.. code-block:: batch

   Command:>MxlUtil -u <utiltiy-name> [parameters]

As ``utility-name``, one of the options presented here can be used.

PublishService
--------------

The following parameters can be passed for publishing map services:

.. code-block:: batch

  Command:>MxlUtil -u publishservice

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

.. code-block:: batch

    Command:>MxlUtil -u publishservice -mxl c:\my-map.mxl -server https://localhost -service produktiv/my-service

Optional parameters can include a *Client* through which the service should be published. 
Typically, not every user should be allowed to publish services. To control this, the 
administrator/manager of the *gView MapServer* can create clients (in the manager web interface 
under ``Security``). Rights can be assigned to these clients at the service and directory level 
(lock icon). Clients that are granted the right to ``publish`` for a directory are allowed to 
publish services.
 
.. note::
   If no rights are set for a directory, all rights are assigned to every user.

.. code-block:: batch

    Command:>MxlUtil -u publishservice -mxl c:\my-map.mxl -server https://localhost -service produktiv/my-service -client publisher -secret pa3sw0rd

.. note::
   Only a valid *Client* can be specified via the command line. The administrator cannot be passed 
   here. If you want to publish services *securely* via the command line, a client must be created!

MxlDatasets
-----------

Here, the connection parameters of datasets within an MXL file can be displayed or modified. 
One use case could be when maps with connections to a (local) test database are developed. Before 
the (automated) publishing, the connection properties can be rewritten to a production database.

Invocation:

.. code-block:: batch
  
   Command:>MxlUtil -u mxldatasets
    
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
* ``-cmd``: Further specification of the command to be executed for the dataset connections.

Optional:

* ``-mxl-out``: Path to an MXL that should be created (only with ``modify-connectionstring``).
  If no output XML is specified, the original file will be overwritten.
    
**Command - Info**

This command is the default. If this or no command is specified, the connection parameters 
of the individual datasets are displayed:

.. code-block:: batch

   Command:>MxlUtil -u mxldatasets -mxl C:\gview5\mxl\my-map.mxl
    
   Dataset 0
   ==============================================================================
   Type: gView.DataSources.MSSqlSpatial.DataSources.Sde.SdeDataset


   ConnectionString:
   ------------------------------------------------------------------------------
   Server=testdbserver
   Database=gisdb
   User Id=DB_READ
   Password=*************

In the listing of datasets, each dataset is assigned a number (here ``0``). If one later wants to change a parameter for a specific 
dataset, this must be indicated with the parameter ``-dsindex`` (see below).

**Command - modify-cs|modify-connectionstring**

This command can be used to change individual *Connection Parameters*. In addition to the parameters mentioned above, the following 
parameters must be provided:

* ``-parameter|-parameter-name``: The name of the parameter (e.g., ``server``) to be changed.
* ``-new-value|-new-parameter-value``: The new value for the parameter.

If multiple parameters are to be changed, the parameters must be repeated on the command line.

Optional:

* ``-dsindex|-dataset-index``: If only a specific dataset is to be changed, the index number of the dataset can be specified here.
  The index number can be obtained from the ``info`` command shown above. If this parameter is not specified, the parameters for 
  all datasets will be changed.

Example:

.. code-block:: batch

    Command:>MxlUtil -u mxldatasets -mxl C:\gview5\mxl\my-map.mxl -cmd modify-cs -parameter Server -new-value proddbserver -parameter password -new-value ProdPa3sw0rd -out-mxl C:\gview5\mxl\my-map-produktiv.mxl


.. note::
   The connection parameters are checked both when opening and when overwriting. If a connection cannot be made with the given parameters,
   the program will terminate.
   If an MXL is damaged and a connection cannot be established with the connection parameters, this tool cannot be used.
   In this case, the MXL file must be repaired using a text editor.

MxlToFdb
--------

Copying all vector data from a map project into a *gView Feature Database*. *gView FDB* is a format for which *gView* guarantees high performance. The databases are set up in *SQL Server*, *PostGre*, or *Sqlite*.
This tool can be used to provide map projects *offline* (all data in a *Sqlite* database)

.. code-block:: batch

   Command:>MxlUtil -u mxltofdb

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


* ``-mxl``: Path to the XML file
* ``-target-connectionstring``: Connection string to the target feature database
* ``-target-guid``: GUID of the target database plugin or simply ``sqlserver|postgres|sqlite``

Optional:

* ``-mxl-out``: Path to an MXL that should be created. If no output XML is specified, 
  the original file will be overwritten.
* ``--dont-copy-features-from``: A list of layers that should not be copied. The tool 
  is mainly used to make existing maps *offline* 
  capable by writing the data into a SQLite database. If (large) 
  datasets of a map are not necessarily required *offline*, they can be specified here. 
  Although the schema of these tables will be created in the target database, no data will be copied.

Example:

.. code-block:: batch

   Command:>MxlUtil -u mxltofdb -mxl C:\gview5\mxl\my-map.mxl -target-connectionstring: c:\offline.fdb -target-guid sqlite -out-mxl C:\gview5\mxl\my-map-offline.mxl -dont-copy-features-from bigdata-layer1,bigdata-layer2

