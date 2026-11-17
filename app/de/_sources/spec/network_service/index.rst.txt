Network Service Spezifikation
=============================

Der **NetworkServer** ist eine Ressource jedes Kartendienstes des *gView Server*. Er bietet Suchen auf einem
*SpatialNET*-Netzwerk (z. B. einem Niederspannungsnetz), das aus den Layern der Karte aufgebaut wurde
(*NetworkModule* der Karte, siehe *gView Carto*).

Die Schnittstelle ist an die Ressourcen des ArcGIS REST API angelehnt (``.../services/{service}/{Resource}``,
Parameter ``f=json|pjson``, HTML-Formular im Browser). Sie ist eine **gView-eigene Schnittstelle** und
**nicht kompatibel** mit dem Esri *Utility Network Service*.

Funktionen:

* **Network Description:** Kennzahlen, Layer, Gewichte, Typen und Regeln des Netzwerks.
* **Trace:** Konnektivität ab Startknoten bis zu gesperrten Knoten.
* **Reach:** kostenbegrenzte Suche (Kürzeste-Wege-Baum).
* **Path:** kürzester Weg zwischen zwei Knoten.
* **Supply:** Versorgung von Senken durch Quellen.
* **Info:** Daten eines Knotens und seiner Nachbarn.
* **Validate:** Prüfung der Netzwerk-Regeln.

Struktur der API-Links
----------------------

``{service}`` ist der Dienstname, optional mit Ordner (``{folder}/{service}``).

**Network Description**

- **GET**

  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver``

**Operationen**

- **GET/POST**

  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/trace``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/reach``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/path``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/supply``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/info``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/validate``

Ohne den Parameter ``f`` liefert jede Ressource eine HTML-Seite bzw. ein Formular zum Ausprobieren.
Parameter werden als Query-String, als Formularfelder oder als JSON-Body (``application/json``) übergeben.

Beispiele (Dienst ``sqlite/net``):

.. code-block:: text

    /geoservices/rest/services/sqlite/net/networkserver?f=pjson
    /geoservices/rest/services/sqlite/net/networkserver/trace?start=Trafo%2336&f=geojson&outSR=4326
    /geoservices/rest/services/sqlite/net/networkserver/reach?start=Trafo%2336&maxCost=250&f=pjson

.. toctree::
   :maxdepth: 1
   :caption: Table of contents:

   description
   operations
   result
   administration
