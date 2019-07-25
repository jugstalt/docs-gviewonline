Datenformate für Karten und Dienste
===================================

Vektordaten
-----------

Folgende Vektordaten können mit *gView GIS* verwendet werden:

+--------------------------+----------+-------------+-------------------------------+ 
| Format                   | Lesend   | Schreibend  | Anmerkung                     |
+==========================+==========+=============+===============================+
| Esri Shape               |    x     |             |                               |
+--------------------------+----------+-------------+-------------------------------+ 
| ArcSDE (SQL Server)      |    x     |      x      |  Geometrie muss als GEOMETRY  |
|                          |          |             |  oder GEOGRAPHY verspeichet   |
|                          |          |             |  sein                         |
+--------------------------+----------+-------------+-------------------------------+ 
| Post GIS                 |    x     |      x      |                               |
+--------------------------+----------+-------------+-------------------------------+ 
| MS SQL Server            |    x     |      x      |  Geometrie muss als GEOMETRY  |
|                          |          |             |  oder GEOGRAPHY verspeichet   |
|                          |          |             |  sein                         |
+--------------------------+----------+-------------+-------------------------------+ 
| MS SQL                   |    x     |      x      |  proprietäres gView Format    |
| Feature Database         |          |             |                               |
+--------------------------+----------+-------------+-------------------------------+ 
| PostgeSQL                |    x     |      x      |  proprietäres gView Format    |
| Feature Database         |          |             |                               |
+--------------------------+----------+-------------+-------------------------------+ 
| SQLite                   |    x     |      x      |  proprietäres gView Format    |
| Feature Database         |          |             |                               |
+--------------------------+----------+-------------+-------------------------------+