
.. gView Online documentation master file, created by
   sphinx-quickstart on Thu Jul 25 18:00:17 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Willkommen bei gView GIS
========================

gView GIS ist ein benutzerfreundliches `Open Source`_ GI Framework zum Erstellen von Karten- und Featurediensten. 
Die Erstellung der Karten erfolgt in einer Windwos Desktop Programm *gView Carto*. Die Karten bestehen aus Layern, die auf verschiedene GeoDaten Qeullen basieren können.
Die einzelnen Layer können beliebig eingefärbt und/oder beschriftet werden.

Fertige Karten könne dann über den *gView Server* publiziert werden. Auf die so entstehenden Karten- und Featuredienste kann dann über definierte Schnittstellen (WMS, WMTS, GeoServices REST...)
zugegriffen werden. 

Positionierung von gView GIS
----------------------------

*gView GIS* ist nicht als eigenstänge GIS Plattform zu verstehen. Es bietet lediglich ein benutzerfreundliches Autorentool (*gView Carto*) zum Erstellen von Karten mit ansprechender 
kartographischer Darstellung und einen performanten Kartenserver zum Veröffentlichen dieser Karten. Diese Karten stehen damit in verschieden Schnittstelen zur Verfügung und können beispielsweise in ein bestehendes WebGIS eingebunden werden.

*gVie GIS* ist als Desktop Software zu verstehen, mit der man Geo-Daten visualisiert/analysiert werden können. Für diese Aufgaben gibt es bereits leistungsfähige Open Source Projekte wie  etwa https://www.qgis.org

Komponenten von gView GIS
-------------------------

Die Komponenten des gView Frameworks lassen sich in zwei Kategorien einteilen:

* **Desktop GIS:** Hierbei handelt es sich um die Programme *gView Carto* und *gView DataExplorer*, die für den Windows Desktop entwickelt wurden. 
  Mithilfe dieser Programme, können Karten erstellt bzw. GeoDaten verwaltet werden.
  Die Basis für beide Programme ist **.NET Framework 4.7.2**. Die Software ist damit nur auf Windows Systemen lauffähig.

* **Server GIS:** Der gView Server ist ein Karten und Featureserver, mit dem die erstellten Karten als Kartendienste veröffentlicht werden können.
  Neben OGC Standards (WMS, WFS) stehen die Dienste auch in Formaten wie GeoServices REST und ArcXML zu verfügung. Damit lassen sich 
  die Dienste in jeder gängigen GIS Software anzeigen und Abfragen.
  Die Basis ist **.NET Core 3.1**. Der Server ist sowohl auf Windows, Linux und MacOS lauffähig


* **gView Commandline Tools:** Eine Sammlung von Kommandozeilen Werkzeugen, mit der immer wieder kehrende Aufgaben im *gView GIS* Umfeld automatisiert werden können (z.B. Karte für den Server veröffentlichen)
  Die Basis ist **.NET Core 3.1**. Der Server ist sowohl auf Windows, Linux und MacOS lauffähig
 
 .. _`Open Source`: https://github.com/jugstalt/gview5
 .. _`Plattform`: https://gviewonline.com

.. toctree::
   :maxdepth: 2
   :caption: Inhaltsverzeichnis:

   setup/index
   desktop/index
   server/index
   commandline/index
   examples/index
   appendix/index


