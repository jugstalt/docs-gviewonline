Identify Endpoint
=================

Der **Identify**-Endpunkt liefert die Features mehrerer Layer an einer Stelle: an einem Punkt (mit einer Toleranz in
Pixeln) oder entlang einer Linie bzw. in einer Fläche. Er braucht das Recht *Query*.

Request-Struktur
----------------

GET oder POST:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/identify?geometry=15155,300038&crs=31256&mapExtent=-200000,200000,100000,450000&imageWidth=800&imageHeight=600``

.. code-block:: json

    {
      "type": "Identify",
      "geometry": { "type": "Point", "coordinates": [15155.4, 300038.1] },
      "crs": { "type": "name", "properties": { "name": "EPSG:31256" } },
      "layerOption": "visible",
      "layers": [ "-4" ],
      "tolerance": 3,
      "mapExtent": { "minX": -200000, "minY": 200000, "maxX": 100000, "maxY": 450000 },
      "imageWidth": 800,
      "imageHeight": 600,
      "dpi": 96,
      "returnGeometry": "geometry",
      "outCRS": { "type": "name", "properties": { "name": "EPSG:4326" } },
      "limit": 10
    }

- **geometry**: ein Punkt, eine Linie oder eine Fläche (GeoJSON). Bei GET ``x,y`` oder eine GeoJSON-Geometrie.
- **crs**: das Koordinatensystem von **geometry** und **mapExtent**.
- **layerOption**: ``visible`` (Standard): die sichtbaren Layer, die im Maßstab der Karte gezeichnet werden;
  ``top``: davon nur der oberste mit Treffern; ``all``: alle abfragbaren Layer, ohne Sichtbarkeit und Maßstab.
- **layers** (optional): Layer-IDs wie bei GetMap: ohne = die Standard-Sichtbarkeit, IDs = genau diese (eine Gruppe
  zeigt alle ihre Layer), ``+id``/``-id`` = zur Standard-Sichtbarkeit hinzu bzw. weg.
- **tolerance**: Toleranz um einen Punkt in Pixeln (Standard 3).
- **mapExtent**, **imageWidth**, **imageHeight**, **dpi**: die Karte, die der Client zeigt. Daraus ergeben sich Maßstab
  und Toleranz in Karteneinheiten. Für ``visible`` und ``top`` erforderlich.
- **returnGeometry**: ``geometry`` (Standard), ``bbox`` oder ``none``.
- **outCRS** (optional): das Koordinatensystem der Ergebnisse; Standard **crs**.
- **limit** (optional): maximale Anzahl Features je Layer (Standard und Höchstwert: die maximale Anzahl des Dienstes).

Ein Layer liefert nur Features, die er zeigt (Definitionsabfrage des Layers).

Response-Struktur
-----------------

.. code-block:: json

    {
      "type": "IdentifyResponse",
      "results": [
        {
          "layerId": "0",
          "layerName": "Bezirke",
          "featureCollection": {
            "type": "FeatureCollection",
            "crs": { "type": "name", "properties": { "name": "EPSG:4326" } },
            "features": [ { "type": "Feature", "oid": 1, "geometry": { "...": "..." }, "properties": { "BEZIRK": "Eisenstadt (Stadt)" } } ],
            "exceededLimit": false
          }
        }
      ]
    }

- **results**: ein Eintrag je Layer mit Treffern, der oberste Layer zuerst.
- **featureCollection**: wie bei :doc:`query`, mit **exceededLimit**, wenn der Layer mehr Treffer hat als **limit**.
