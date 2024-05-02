Willkommen bei gView GIS
========================

gView GIS ist ein benutzerfreundliches `Open Source`_ GI Framework zum Erstellen von Karten- 
und Featurediensten. 

Die Tools zum Erstellen der Karten werden über eine Web-Oberfläche angeboten, 
die sowohl lokal am Client 
ausgeführt als auch auf einem Server gehostet werden können.
Die Erstellung der Karten erfolgt mit dem Programm *gView Carto*, das innerhalb der *gView.WebApps*
Anwendung angeboten wird. Die Karten bestehen aus Layern, die auf verschiedene GeoDaten-Quellen 
basieren können. Die einzelnen Layer können beliebig eingefärbt und/oder beschriftet werden.

Fertige Karten können über den *gView Server* publiziert werden. Auf die erstellten Karten- und 
*Feature*-Dienste kann über definierte Schnittstellen (WMS, WMTS, GeoServices REST...)
zugegriffen werden.


Positionierung von gView GIS
----------------------------

*gView GIS* ist nicht als eigenständige GIS-Plattform zu verstehen. Es bietet lediglich ein benutzerfreundliches 
Autorentool (*gView Carto*) zum Erstellen von Karten mit ansprechender kartographischer Darstellung und einen 
performanten Kartenserver zum Veröffentlichen dieser Karten. Diese Karten stehen damit über verschiedene 
Schnittstellen zur Verfügung und können beispielsweise in ein bestehendes WebGIS eingebunden werden.

*gView GIS* ist nicht als Desktop-Software zu verstehen, mit der Geo-Daten zum Zwecke von Analysen visualisiert
werden können. Für diese Aufgaben gibt es bereits leistungsfähige Open-Source-Projekte wie  
etwa https://www.qgis.org.

Vielmehr geht es darum, performante und kartographisch ansprechende Kartendienste über den *gView.Server*
zu publizieren. Analyse-Werkzeuge werden in anderen/bestehenden Programmen angeboten, die *gView Kartendienste*
einbinden können.

Eine empfohlene produktive Konstellation für den Einsatz von *gView GIS* wäre beispielsweise:

.. image:: img/gview1.png

Die beiden Komponenten ``Datenbank`` und ``Karten-/FeatureServer`` können sich hier auf 
einem Server befinden und der ``GIS Client`` lokal auf dem Desktop oder in einem Browser laufen.
*gView Carto* wird ebenfalls lokal auf einem Desktop installiert. Die erstellten Karten 
werden im *gView MapServer* veröffentlicht. Der *gView MapServer* besitzt zusätzlich einen 
Security Layer, mit dem bestimmt werden kann, welcher Client Karten anzeigen, 
abfragen oder die Daten bearbeiten darf.

Eine weitere Einsatzmöglichkeit von *gView GIS* ist die Erstellung von Offline-GIS-Lösungen. 
Diese können auf einem beliebigen (Windows, Linux, MacOS) Gerät installiert werden und können 
als Ausfalls-/Notsystem oder Offline-System (Laptop) dienen.
Dazu bietet *gView GIS* über die Kommandozeilen-Tools die Möglichkeit an, alle Vektordaten 
einer Karte in eine SQLite-Datenbank zu übernehmen. Damit fällt die Datenbank-Schicht weg und 
die SQLite-Datenbank kann einfach auf das Zielgerät kopiert werden. Am Zielgerät muss dann 
nur noch der *gView MapServer* und die entsprechende Client-Software installiert werden. 
Der *gView MapServer* kann auf dem Zielgerät wie in der Installationsanleitung gezeigt als 
*Standalone*-Anwendung ausgeführt werden.

           

Komponenten von gView GIS
-------------------------

Die Komponenten des gView Frameworks lassen sich in drei Kategorien einteilen:

* **gView WebApps:** Dies umfasst die Web-Anwendungen *gView Carto* und *gView DataExplorer*. 
  Beide können über eine Web-Oberfläche bedient werden.
  Mithilfe dieser Programme können Karten erstellt bzw. GeoDaten verwaltet werden.
  Die Basis für beide Programme ist .NET 8 (Microsoft.AspNetCore.App 8.0.x Runtime)
  und damit auf Windows, Linux und MacOS Betriebssystemen lauffähig.

* **gView Server:** Der gView Server ist ein Karten- und Featureserver, 
  mit dem die erstellten Karten als Kartendienste veröffentlicht werden können.
  Neben OGC-Standards (WMS, WFS) stehen die Dienste auch in Formaten wie GeoServices REST 
  und ArcXML zur Verfügung. Damit lassen sich die Dienste in jeder gängigen GIS-Software 
  anzeigen und abfragen.
  Die Basis des Programms ist .NET 8 (Microsoft.AspNetCore.App 8.0.x Runtime)
  und damit auf Windows, Linux und MacOS Betriebssystemen lauffähig.

* **gView Commandline Tools:** Eine Sammlung von Kommandozeilenwerkzeugen, mit denen wiederkehrende 
  Aufgaben im *gView GIS* Umfeld automatisiert werden können (z.B. eine Karte für den Server veröffentlichen).
  Die Tools befinden sich nach der Installation im gleichen Verzeichnis wie *gView.WebApps*
  und haben die gleichen Laufzeitvoraussetzungen.

 
 .. _`Open Source`: https://github.com/jugstalt/gview-gis
 .. _`Plattform`: https://gviewonline.com

.. toctree::
   :maxdepth: 2
   :caption: Inhaltsverzeichnis:

   setup/index
   webapps/index
   server/index
   commandline/index
   examples/index
   appendix/index

   desktop/index


