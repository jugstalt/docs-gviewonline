Standalone (Windows, Linux, MacOS)
==================================

Der *gView Server* kann auch als Standalone Applikation (ohne Internet Information Server) gestartet werden.
Dazu ist die Applikation über die Kommandozeile folgendermaßen zu starten:

.. code::

   cd ./server
   set GDAL_DRIVER_PATH=.\gdalplugins
   start dotnet gView.Server.dll -expose-http 5000

.. note::
   Damit die GDAL Formate JPG2000 und ECW erkannt werden, muss die Umgebungsvariable ``GDAL_DRIVER_PATH``
   auf das ``gdalplugins`` Verzeichnis zeigen. Oben wird dieser Pfad relativ zum Startverzeichnis angegeben.

 

