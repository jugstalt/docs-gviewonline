gView Carto
===========

Die App **gView.Carto** dient zum Erstellen und Betrachten von Karten. Diese
Karten können im **gView.Server** veröffentlicht werden und stehen danach in unterschiedlichen 
Schnittstellenformaten zur Verfügung.

In diesem Abschnitt soll der Vorgang der Kartenerstellung beschrieben werden. Auf die einzelnen Werkzeuge
wird nur kurz bzw. gar nicht eingegangen. Ziel ist es, nach der Lektüre dieses Kapitels in
der Lage zu sein, Daten in eine Karte hinzuzufügen und die Legenden zu gestalten.

Nach dem Start durch einen Klick auf die ``gView.Carto`` zeigt die Anwendung zunächst ein leeres
Kartenbild mit einem transparenten Hintergrund *TileCache*. Dieser Hintergrund ist nicht Teil 
der eigentlichen Karte, sondern dient nur zur Orientierung. Speichert man die Karte und 
veröffentlicht sie im **gView.Server**, wird dieser Hintergrund nicht übernommen. Der Hintergrund
erleichtert es, sich in der Karte zurechtzufinden, besonders wenn die Karte später 
nur aus Vektordaten besteht.


.. image:: img/carto1.png

.. toctree::
    :maxdepth: 2
    :caption: Inhaltsverzeichnis:
 
    newmap
    adddata
    toc
    contexttools
    symbology
    renderers
    labeling
    scales
    layersettings
    managemaps
