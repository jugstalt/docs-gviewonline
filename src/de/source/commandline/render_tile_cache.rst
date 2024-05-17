.. _commandline-tools-render-tile-cache:

gView.Cmd RenderTile
====================

Dieses Werkzeug sendet die Aufforderung zum Berechnen von *TileCache* Tiles an den *gView Server*. 
Der entsprechende Dienst muss über seine Metadaten in *gView Carto* für die Bereitstellung von *Tiles* 
freigegeben sein.

Ein Aufruf der *WMTS-Capabilities* (beispielsweise über die gView Server Oberfläche) muss möglich sein.

.. note::

    Die folgenden Befehle werden im **interaktiven Modus** gezeigt. Dazu muss ``gView.Cmd.exe -i``
    aufgerufen werden. Ohne *interaktivem Modus* müsste jedem hier gezeigten Befehl noch
    ``gView.Cmd.exe --command`` vorangestellt werden.

Es gibt drei ``TileCache`` Kommandos:

.. code-block:: batch

  TileCache.Render:        Forces a gView Server instance to render service a tile cache
  TileCache.Info:          Shows information about a tilecache service
  TileCache.ClipCompact:   Clips a tile compact cache by polygon(s)

TileCache.Info
--------------

Hier wird nur eine Information über den TileCache Dienst ausgegeben. Das kann nützlich sein, 
um zu überprüfen, ob ein Dienst als *Tile Cache* verwendet werden kann.

.. code-block:: batch

   Command:>TileCache.Info --help
   Help: TileCache.Info
   Shows information about a tilecache service
   Usage:
      -server: gView Server Instance, eg. https://my-server/gview-server
      -service: The service to pre-render, eg. folder@servicename

   Command:>TileCache.Info -server https://localhost:44331 -service cache/ortsplan
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
   
Außerdem werden hier alle Informationen zurückgegeben, die für optionale Parameter für das Rendering 
nützlich sein können. Steht der Dienst nicht als *Tiling Dienst* zur Verfügung, lautet die Ausgabe in 
etwa folgendermaßen:

.. code-block:: batch

   Exception:
   Can't read metadata from server. Are you sure taht ervice is a gView WMTS service?

TileCache.Render
----------------

Dieser Befehl löst das eigentliche Rendern von *Tile Cache* Kacheln aus. Um weiter zu spezifizieren,
was gerendert werden sollte, dienen die optionalen Parameter. Es werden hier nur Befehle zum *gView Server*
geschickt, die das Rendern veranlassen. Das *Rendering* passiert im *gView Server*. Dort werden die
Tiles erstellt und im File System abgelegt.

.. note::
   Existiert eine Kachel bereits, wird sie vom Server nicht neu berechnet.
   Der Server berechnet nur Kacheln, die noch nicht existieren. Das macht Sinn, wenn nur einige
   Tiles eines *Tile Cache* neu berechnet werden müssen.
   Hier löscht man zuerst die betroffenen *Tiles* am File System
   (z.B. mit dem Befehl ``TileCache.ClipCompact``)

Optionale Parameter:

* ``-epsg``: Der *gView Server* kann für einen Dienst Tilecaches in unterschiedlichen Koordinatensystemen
  anbieten. Welche Koordinatensysteme möglich sind, kann bei den Metadaten in *gView Carto* eingestellt
  werden. Über den oben gezeigten ``-info`` Befehl können die möglichen Werte angezeigt werden.

* ``-compact``: Mit dieser Option wird ein *Compact Tile Cache* erzeugt. Der Unterschied zu einem
  *klassischen Tile Cache* ist, dass nicht für jede Kachel eine Datei angelegt wird. Hier werden immer
  128 x 128 Kacheln zu einer Datei im FileSystem zusammengefasst. Dadurch braucht der Tile Cache im File
  System etwas weniger Platz und kann vor allem leichter kopiert werden (da ein klassischer Tile Cache
  oft aus Millionen Dateien besteht. Eine große Datei ist für das File System in der Regel leichter zu
  handhaben als viele kleine Einzeldateien). Allerdings muss bei jedem späteren Request die Einzelkachel
  aus den großen Files herausgelöst werden (was in der Regel sehr schnell vonstatten geht).

* ``-orientation``: Hier wird die Orientierung des *Tile Cache* (bzw. die Lage des Ursprungs) angegeben.
  *Tile Caches* mit der Orientierung *lower left* können mit WMTS allerdings nicht verwendet
  werden. Daher wird diese Option nur der Vollständigkeit angeboten und kann in der Regel
  weggelassen werden.

* ``-bbox``: Hier kann eine *Bounding Box* (im jeweiligen Koordinatensystem) angegeben werden. Nur für diesen
  Bereich werden die Render Kommandos an den Server geschickt.

* ``-scales``: Eine Liste von Maßstäben (mit Komma getrennt) für die Kommandos an den Server geschickt
  werden.

* ``-threads``: Zum Beschleunigen der Tile Cache Erstellung können mehrere Kommandos gleichzeitig
  zum *gView Server* geschickt werden. Ansonsten wird gleichzeitig immer nur das Kommando für eine Kachel
  zum Server geschickt. Es macht keinen Sinn, hier extrem hohe Werte anzugeben.
  Faustregel: ``-threads`` = Anzahl der Prozessoren. Steigt die Prozessorlast damit nicht hoch an,
  bedeutet das, dass die meiste Zeit beim Rendern mit dem Warten auf die Datenbank verwendet wird.
  In diesem Fall kann der Wert hier auch noch erhöht werden.

Beispiel:

.. code-block:: batch

   Command:>TileCache.Render -server https://localhost:44331 -service cache/ortsplan -compact -scales 1000000,500000,250000,100000,50000,25000,10000,5000 -threads 10

