.. _commandline-tools-render-tile-cache:

gView.Cmd.RenderTileCache
=========================

Dieses Werkzeug sendet die Aufforderung zum Berechnen von *TileCache* Tiles an den *gView Server*. Der entsprechende muss über seine Metadaten in *gView Carto* für die Bereitstellung von *Tiles* freigegeben sein.
Ein Aufruf der *WMTS-Capabilities* (beispielsweise über die gView Server Oberfläche) muss möglich sein.

.. code::

   .\gView.Cmd.RenderTileCache.exe

   USAGE:
   gView.Cmd.RenderTileCache <-info|-render> -server <server> -service <service>
          optional paramters: -epsg <epsg-code>                           [default: first]
                              -compact ... create a compact tile cache
                              -orientation <ul|ll|upperleft|lowerleft>    [default: upperleft]
                              -bbox <minx,miny,maxx,maxy>                 [default: fullextent]
                              -scales <scale1,scale2,...>                 [default: empty => all scales
                              -threads <max-parallel-requests>            [default: 1]

Erforderliche Parameter:

* ``-info``: Hier wird nur eine Information über den TileCache Dienst ausgegeben. Das kann nützlich sein, um zu überprüfen, ob ein Dienst als *Tile Cache* verwendet werden.

.. code::

   .\gView.Cmd.RenderTileCache.exe -info -server https://localhost:44331 -service cache/ortsplan
   TileSize [Pixel]: 512 x 512
   ImageFormats: png
   Scales:
     1 : 1000000
     1 : 500000
     1 : 250000
     1 : 100000
     1 : 50000
     1 : 25000
     1 : 10000
     1 : 5000
     1 : 2500
    1 : 1000
   Origin: upperleft
     EPSG:31256 upperleft: -5622500, 5001000
   BBox:
     EPSG:31256: -226900, 163300, 0, 315500
   
Außerdem werden hier alle Inforationen zurückgegeben, die für optionalen Parameter für das Rendering nützlich sein können.
Steht der Dienst nicht als *Tiling Dienst* zur Verfügung lautet die Ausgabe in etwa folgendermaßen:

.. code::

   Exception:
   Can't read metadata from server. Are you sure taht ervice is a gView WMTS service?

* ``-render``: dieser Befehl löst das eigentlich Rendern von *Tile Cache* Kacheln aus. Um weiter zu spezifizieren, was gerendert werden sollte dienen die optionalen Parameter. Es werden hier nur Befehle zum *gView Server*
  geschickt, die das Rendern veranlassen. Das *Rending* passiert im *gView Server*. Dort werden die Tiles erstellt und im File System abgelegt.
  
.. note::
   Existiert eine Kachel bereits, wird sie vom Server nicht neu berechnet. Der Server berechnet nur Kacheln, die noch nicht existieren. Das macht Sinn, wenn nur einige Tiles eines *Tile Cache* neu berechnet werden müssen.
   Hier löscht man zuerst die betroffen *Tiles* am File Sytsem (z.B. mit dem Befehl ``gView.Cmd.ClipCompactTilecache``)

Optionale Parameter:

* ``-epsg``: Der *gView Server* kann für einen Dienst Tilecaches in unterschiedlichen Koordinatensystemen anbieten. Welche Koordinatensystemen möglich sind, kann bei den Metadaten in *gView Carto* eingestellt werden. Über den oben gezeigten ``-info`` Befehl
  können die möglichen Werte angezeigt werden.

* ``-compact``: Mit dieser Option wird ein *Compact Tile Cache* erzeugt. Der Unterschied zu einem *klassischen Tile Cache* ist, dass nicht für jede Kachel eine Datei angelegt wird. Hier werden immer 128 x 128 Kacheln zu einer 
  Datei im FileSystem zusammengefasst. Dadurch braucht der Tile Cache im File System etwas weniger Platz und kann vor allen leichter Kopiert werden (da ein klassischer Tile Cache oft aus Millionen Dateien besteht. Eine große Datei 
  ist für das File System in der Regel leichter zu händeln als viele kleine Einzeldateien). Allerdings muss bei jeden späteren Request die Einzelkachel aus den großen Files herausgelöst werden (was in der Regel sehr schnell vonstatten geht).

* ``-orientation``: Hier wird die Orientierung des *Tile Cache* (bzw. die Lage des Ursprungs) angegeben. *Tile Caches* mit der Orientierung *links unten (lowerleft)* können mit WMTS allerdings nicht verwendet werden. Daher wird diese Option nur
  der Vollständigkeit angeboten und kann in der Regel weggelassen werden. 

* ``-bbox``: Hier kann eine *Bounding Box* (im jeweiligen Koordinatensystem) angegeben werden. Nur für diesen Bereich werden die Render Kommandos an der Server geschickt

* ``-scales``: Eine Liste von Maßstäben (mit Komma getrennt) für die Kommandos an der Server geschickt werden.

* ``-threads``: Zum Beschleunigungen der Tile Cache Erstellung, können mehrere Kommandos gleichzeitig zum *gView Server* geschickt werden. Ansonsten wird gleichzeitig immer nur das Kommando für eine Kachel zum Server geschickt. Es macht keinen
   Sinn, hier extrem hohe Werte anzugeben. Faustregel: ``-threads`` = Anzahl der Prozessoren. Steigt die Prozessorlast damit nicht hoch an, bedeutet das, dass die meiste Zeit beim rendern mit dem Warten auf die Datenbank verwendet wird.
   In diesem Fall kann der Wert hier auch noch erhöht werden.
  

Beispiel:

.. code::

   .\gView.Cmd.RenderTileCache.exe -render -server https://localhost:44331 -service cache/ortsplan -compact -scales 1000000,500000,250000,100000,50000,25000,10000,5000 -threads 10

