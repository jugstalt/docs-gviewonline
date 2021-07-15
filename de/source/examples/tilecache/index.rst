Berechnung eines *TileCaches* (Ortsplan)
========================================

In diesem Beispiel wird gezeigt, wie aus einem bestehenden *gView Server* Dienst ein TileCaches gerechnet werden kann.
Dieser kann über die WMTS Schnittstelle in unterschiedlichen Anwendungen eingebunden werden. Durch das Vorprozessieren der Kacheln
erhöht sich die Performance des Dienstes. Außerdem vermindert sich die Serverlast.

Ein *TileCache* besteht man Ende nur noch aus einzelnen (Kachel) Bilder. Diese können auch ein *Compact TileCache Kacheln* zusammengefasst werden,
wodurch die Anzahl der vorgehalten Einzelbilder reduziert werden (einfache zu kopieren, weniger Speicherplatz)

Ein *gView TileCache* bietet unterschiedliche *Styles* an. Die Kacheln können über Filter *on-the-fly* beispielsweise auch in Schwarz-Weiß angezeigt werden.

Um für einen Dienst später eine Tilecache erstellen zu können, muss dies schon im Kartenprojekt in *gView Carto* festgelegt werden. Dazu muss in der entsprechenden
Karte die *Karteneigenschaften* Seite aufgerufen werden. Dort finden man den *Tab-Reiter* ``MapService``. Klickt man dort auf ``Metadata`` öffnet sich das 
Metadatenfenster für das Kartenservice. Unter den Metadaten kann beispielsweise eingestellt werden, in welchen Projektionen WMS Dienste angeboten werden.

Bei den Metadaten auf ``Tile Service Properties`` klicken. Dort kann eingestellt werden, das Tiling Dienste für diese Karte möglich sind:

.. image:: img/metadata1.png

Hier sind schon die gewünschten Maßstäbe definiert und die Kachelgrößen von 256x256 auf 512x512 geändert. Da es sich beim dem Dienst um einen Ortsplan 
handelt, wird als Bildformat nur ``image/png`` angeboten.

Im nächsten Schritt muss die Ausdehnung des TileCache Dienstes und der Ursprung angegeben werden:
Für einen Dienst können mehrere TileCaches für unterschiedliche Koordinatensystem gehalten werden. Bevor die Ausdehnung festgelegt wird, muss noch ein Koordinatensystem 
angeben werden (``+`` Button):

.. image:: img/metadata2.png

FÜr die Berechnung der Tiles kann ein Ursprung (Tile 0/0/0) links oben oder links unten angegeben werden. Tilecaches mit Ursprung links unten sind allerdings veraltet und 
werden hier nur der Vollständigkeit angeboten. FÜr WMTS Dienste sollten Einstellungen mit dem Ursprung links oben erzeugt werden.

Werden die einstellungen auf für andere Dienste verwendet, könne diese auch mit dem ``Speichern`` / ``Laden`` Button gespeichert bzw. geladen werden.
Für WebMercator Karten können die Einstellungen beispielsweise von ``gview5/desktop/misc/tiling/osm.xml`` geladen werden.

