
.. gView Online documentation master file, created by
   sphinx-quickstart on Thu Jul 25 18:00:17 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Willkommen bei gView Online
===========================

gView Framework
---------------

gView GIS ist ein benutzerfreundliches GI Framework zum Visualisieren und Verwalten von räumlichen Daten (Geodaten). 
Außer dem Anzeigen der Daten unterstützt gView GIS eine Menge weiter Features, wie zum Beispiel Layer Kontrolle, Legendengestaltung, 
räumliche und sachbezogene Abfragen, Themenbeschriftung (Labeling), messen von Strecken und Flächen und vieles mehr.

Das gesamte Framework ist in .NET programmiert und lässt sich mit allen .NET Sprachen über Plug-Ins erweitern. Das Spektrum dieser Erweiterungen 
umfasst zum Beispiel benutzerdefinierte Werkzeuge, Renderer für die Darstellung von Kartenobjekten, Symbolik, 
Datenquellen für Vektor- und Rasterdaten sowie Schnittstellen für den Export der Karten über den gView Map Server. 

Die Komponenten des gView Frameworks lassen sich in zwei Kategorien einteilen:

* **Desktop GIS:** Hierbei handelt es sich um die Programme *gView Carto* und *gView DataExplorer*, die für den Windows Desktop entwickelt wurden. 
  Mithilfe dieser Programme, können Karten erstellt bzw. GeoDaten verwaltet werden.

* **Server GIS:** Der gView Server ist ein Kartenserver, mit dem die erstellten Karten als Kartendienste veröffentlicht werden können.
  Neben OGC Standards (WMS, WFS) stehen die Dienste auch in Formaten wie GeoServices REST und ArcXML zu verfügung. Damit lassen sich 
  die Dienste in jeder gängigen GIS Software anzeigen und Abfragen.

gView Online
------------

Mit dieser Plattform, wird der *gView Server* als Cloud Dienst angeboten. Damit ist keine Installation des Kartenservers mehr im
eigenen Rechenzentrum notwendig. Die Karten werden zuerst am Desktop mit *gView Carto* erstellt und dann in der Cloud veröffentlicht.
Voraussetzung ist hierbei, dass ein Zugriff von gView Online auf die Geo-Datenbank möglich ist. Hostet man eine Geo-Datenbank ebenfalls in der Cloud,
ist keine eigene Server Landschaft zum Veröffentlichen von Kartendiensten mehr notwendig.
 

.. toctree::
   :maxdepth: 2
   :caption: Inhaltsverzeichnis:

   formats


