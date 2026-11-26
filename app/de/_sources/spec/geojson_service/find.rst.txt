Find Endpoint
=============

Der **Find**-Endpunkt sucht einen Text in den Textfeldern mehrerer Layer, ohne Rücksicht auf Groß- und
Kleinschreibung. Er braucht das Recht *Query*.

Request-Struktur
----------------

GET oder POST:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/find?searchText=eisen&layers=0,1&returnGeometry=centroid``

.. code-block:: json

    {
      "type": "Find",
      "searchText": "eisen",
      "searchFields": [ "BEZIRK", "NAME" ],
      "layers": [ "0", "1" ],
      "contains": true,
      "returnGeometry": "geometry",
      "outCRS": { "type": "name", "properties": { "name": "EPSG:4326" } },
      "limit": 10
    }

- **searchText**: der gesuchte Text.
- **searchFields** (optional): die Felder, in denen gesucht wird (nur Textfelder); ohne alle Textfelder eines Layers.
- **layers** (optional): die Layer-IDs; ohne alle abfragbaren Layer.
- **contains**: ``true`` (Standard): das Feld enthält den Text; ``false``: das Feld ist der Text.
- **returnGeometry**, **outCRS**, **maxAllowableOffset**, **geometryPrecision**: wie bei :doc:`query`.
- **limit** (optional): maximale Anzahl Features je Layer.

Ein Layer liefert nur Features, die er zeigt (Definitionsabfrage des Layers). Die Suche wird als CQL2-Filter
(``CASEI(feld) LIKE CASEI('%text%')``) für jede Datenquelle in SQL übersetzt.

Response-Struktur
-----------------

Wie bei :doc:`identify`, mit ``"type": "FindResponse"``: ein Eintrag je Layer mit Treffern (**layerId**,
**layerName**, **featureCollection** mit **exceededLimit**).
