gView Feature Database
======================

*gView Feature Database* ist ein Datenformat, das direkt für *gView GIS* entwickelt wurde.
Die Daten werden dabei in einer Datenbank gespeichert (MS Sql Server, Postgres, SQlite). Für jeden Datenlayer
wird dabei eine Tabelle angelegt in der die Sachdaten und die Geometrie gespeichert wird.
Die Geometrie wird in einem *Binary Blob* verspeichert.

.. note::
   Als die erste Version von *gView GIS* entwickelt wurde, waren offene GeoDatenbank wie PostGIS noch 
   nicht verbreitet. Ebenfalls waren Standard wie WKB (Well Known Binary) noch nicht etabliert. 
   Die *gView Feature Database* ist durch diese GeoDatenbanken nicht mehr zwingend notwendig.
   Es wird empfohlen, als Datenquelle für die Kartendienste PostGIS oder SQLServer zu verwenden, da diese auch
   von anderen GIS Software Paketen verarbeitet werden können.

.. note::
   Gibt es bereits eine bestehende GeoDatenbank (etwa PostGIS) sollte diese verwendet werden. Auch wenn noch keine GeoDatenbank
   vorhanden ist, wird empfohlen die Daten in einer PostGIS Datenbank zu halten, um offen für andere Softwarepakete zu bleiben.

Die einzige Relevanz hat die *gView Feature Database* noch, um ein Offline GIS bereitzustellen. Dazu werden die Daten einer 
gView Karte in eine *gView SQLite Feature Database* gespeichert. Diese kann dann "offline" mitgenommen werden.
Ein Kompatibilität mit anderen GIS Software Paketen ist dafür in der Regel nicht vorrangig.

Außerdem lassen sich in einer *gView Feature Database* auch Raster Kataloge erstellen und einfach verwalten. Aus diesem
Grund wurde die Beschreibung für die *gView Feature Database* aus dem ursprünglichen Handbuch 1:1 übernommen (Screenshot
sind nicht aus der aktuellen Version). 

Bleiben die Daten in der bestehenden GIS Datanbank (zB PostGIS) kann diese Beschreibung übersprungen werden.

.. toctree::
   :maxdepth: 2
   :caption: Inhaltsverzeichnis:

   create
   raster_catalog


