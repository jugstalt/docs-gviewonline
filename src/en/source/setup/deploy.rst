.. _deploy:

Installation
============

The installation is carried out via the command line program ``gview.deploy`` or ``gview.deploy.exe``.
This tool performs the following tasks:

* Fresh installation of *gView.WebApps* and *gView.Server*
* Management of deployment profiles (e.g., ``local``, ``test``, ``staging``, ``production``)
* Distribution of configuration changes (e.g., ``mapserver.json``, ``gview-web.config``)


Delivering a New Version
------------------------

On Windows, the program can be copied to ``C:\deploy\gview-gis``, for example.

When starting the program for the first time, a profile must be created first.
The profile could be, for example, ``test``, ``staging``, or ``production``. Since in the first
step we only want to test *gView GIS* locally, a profile named ``local`` is 
suitable for the start:


.. code-block:: batch
   :emphasize-lines: 1,5

   C:\> .\gview.deploy.exe

   Work-Directory: C:\\deploy\\gview-gis
   Choose a profile or create a new by enter an unique name, eg. production, staging, test
   Input profile index [0]: local

In the next step, the program offers to download the latest release from *GitHub*,
if it is not already present. 

.. code-block:: batch

   Do you want to download latetest version from GitHub? Y/N [Y]

If that is not possible, the latest release can also be downloaded manually. 
To do this, the ZIP files must be placed in the ``download`` directory.
For example, here: ``C:\deploy\gview-gis\download``

If ZIP files are in the ``download`` directory, the different versions are displayed:

.. code-block:: batch

   Choose a version
   0 ... 6.24.1801
   Input version index [0]:

The latest version is assigned index ``0``.

.. note::

   All values entered through ``gview.deploy`` do not need to be re-entered in subsequent
   calls. Instead, these values are displayed with an index number. One only needs to enter
   the corresponding number, or simply press ``ENTER`` if the desired index is the
   suggested value, e.g., ``Input version index [0]`` => ``ENTER`` => Version with
   index ``0``.

In the next step, it can be determined which products should be installed.
``Everything`` deploys both ``gView.Server`` and ``gView.Web``:


.. code-block:: batch

   Choose a product
   0 ... Everything
   1 ... gView.Server
   2 ... gView.Web
   Input product index [0]:

After the version and product have been selected, the deployment tool asks again whether 
the chosen version should indeed be deployed with the profile:

.. code-block:: batch

   Deploy 'Everything' from version 6.24.1801 to profile local
   Do you want to continue? Y/N [Y]

Pressing ``ENTER`` or ``Y`` starts the deployment process.

When publishing a profile (here ``local``) for the first time, some values need to be provided. 
If you want to use the default value, simply confirm by pressing ``ENTER``.


.. code-block:: batch

   Target installation path [C:\apps\gview-gis]:
   Repsitory path [C:\apps\gview-gis\local\gview-repository]:
   gView Server online url [http://localhost:5050]:

* **Target installation path:** The path where gView-GIS should be installed.
  Under this directory, the deployment tool will also create a folder with the profile name
  and version. Here, the app would be installed at ``C:\\apps\\gview-gis\\local\\6.24.1801``.

* **Repository path:** In the repository path, various files necessary for the software's operation are stored,
  such as map documents (XML files) published by the map server. The repository folder is usually placed
  in the directory of the profile (here: ``C:\\apps\\gview-gis\\local``). Since the folder is not in the *versions* 
  directory, it can be immediately used by a newly installed version. It is important that different profiles
  use their own repository directories.

* **gView Server online URL:** A URL at which the *gView.Server* will be accessible.
  If you want to test the ``local`` profile and only run the programs locally, this is usually done through http://localhost:5050.
  The advantage of setting this value here is that later, in the *gView.WebApps* app, an additional tile for accessing the *gView.Server*
  will be offered, which facilitates administration. Without this URL, only tiles for *gView.Carto* and *gView.Explorer* would be displayed.

The next values we set are the **Admin User** and the Admin password.
We also define a **Carto User**.
The password must be entered for each:

.. code-block:: batch

   gView Admin Username [admin]:
   gView Admin Password [*****]: my-secret-admin-password
   gView Admin Username [carto]:
   gView Admin Password [*****]: my-secret-carto-password

The difference between the two users is that the **Carto User** can only access limited 
tools. For example, he cannot use **gView.Explorer** but only **gView.Carto**. Furthermore, he does not see the actual *Connection Strings*
of the connections. The **Carto User** can thus only access predefined connections
but cannot create his own connections to databases, etc. This user 
should be used by those who want to create new maps. These users generally do not need to know the database credentials.

Afterwards, the deployment process begins:

.. code-block:: batch

   ***********************************************************************
   Create a new webgis repositiry C:\apps\gview-gis\local\gview-repository
   ***********************************************************************

   Deploy version 6.24.1801
   Deploy gView Server:
   ...succeeded 972 items created
   Deploy gView WebApps:
   ...succeeded 448 items created
   Overrides
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\server\override\_config\mapserver.json
   ...succeeded 1 items created/overridden
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\web\override\_config\gview-web.config
   ...succeeded 1 items created/overridden

Both *gView.WebApps* and *gView.Server* are deployed. After the ZIP files are unpacked,
user-specific files from the directory ``_deploy_repository\profiles\{profile}\[server|web]\override``
are copied to the respective application directory.
This process overwrites the configuration from the installation package with the configuration from the
current profile.

.. note::

   Any files can be copied into the *Override* directories, which are then additionally
   copied or overwritten in the application directories.
   Configuration files should never be changed directly in the application directory (deploy directory),
   but always in the *Override* directory. This ensures that changes
   to the configuration are copied again with the next update of a profile.

  
Modify Current Configuration
----------------------------

If changes are made to the configuration (e.g., ``mapserver.json``), this is done in the *Override*
directory. Afterward, when you run ``gview.deploy`` again, you receive the following message:

.. code-block:: batch

   Choose a profile or create a new by enter an unique name, eg. production, staging, test
   0 ... local
   Input profile index [0]:

   Do you want to download latetest version from GitHub? Y/N [Y]

   Choose a version
   0 ... 6.24.1801
   Input version index [0]:

   Deploy version 6.24.1801 to profile local
   Do you want to continue? Y/N [Y]
   Target installation path: C:\apps\gview-gis
   Repsitory path: C:\apps\gview-gis/local/gview-repository
   gView Server online url: http://localhost:5050
   gView Admin Username: admin
   gView Admin Password:
   gView User Username: carto
   gView Carto Password:

   Deploy version 6.24.1801


   **************************************

   Warning: version already deployed

   ***************************************

   Overrides
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\server\override\_config\mapserver.json
   ...succeeded 1 items created/overridden
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\web\override\_config\gview-web.config
   ...succeeded 1 items created/overridden


A warning appears that this version has already been deployed. No data are copied from the ZIP files. 
Only the *Overrides* are executed.


