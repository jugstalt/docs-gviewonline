Standalone (Windows, Linux, MacOS)
==================================

The *gView Server* can also be started as a standalone application (without Internet Information Server).
To do this, the application must be started via the command line as follows:

.. code::

   cd ./server
   set GDAL_DRIVER_PATH=.\gdalplugins
   start dotnet gView.Server.dll -expose-http 5000

.. note::

   In order for the GDAL jpg2000 and ECW formats to be recognized, the environment variable 
   ``GDAL_DRIVER_PATH`` must be point to the ``gdalplugins`` directory. 
   Above, this path is specified relative to the home directory.
