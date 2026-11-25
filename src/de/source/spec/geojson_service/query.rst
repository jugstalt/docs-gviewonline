Query Endpoint
==============  

Der **Query**-Endpunkt ermöglicht das Abfragen von Features eines bestimmten Layers,  
basierend auf verschiedenen räumlichen und attributiven Filtern.  

Request-Struktur
----------------  

Der **Query**-Endpunkt unterstützt GET- und POST-Anfragen. Der Body der Anfrage wird durch das  
folgende Objekt definiert:  

.. code-block:: json

    {
      "type": "GetFeatures",
      "command": "select",  // "select", "distinct", "countOnly", "IdsOnly"
      "outFields": ["field1", "field2"],
      "returnGeometry": "geometry",  // "none", "geometry", "bbox"
      "outCRS": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "orderByFields": ["field1 ASC", "field2 DESC"],
      "objectIds": ["1", "2", "3"],
      "spatialFilter": {
        "crs": {
          "type": "name",
          "properties": {
            "name": "EPSG:4326"
          }
        },
        // geometry OR bbox
        "geometry": {
          "type": "Polygon",
          "coordinates": [
            [
              [10.0, 50.0],
              [20.0, 50.0],
              [20.0, 60.0],
              [10.0, 60.0],
              [10.0, 50.0]
            ]
          ]
        },
        "bbox": {
          "minX": 10.0,
          "minY": 50.0,
          "maxX": 20.0,
          "maxY": 60.0
        },
        "operator": "intersects"  // "within", "intersects", "contains", "intersectsBBox"
      },
      "filter": {
        "whereClause": "field1 = @value1 AND field2 > @value2",
        "parameters": {
          "value1": "someValue",
          "value2": 100
        }
      },
      "limit": 100,
      "offset": 0
    }

- **type**: (string) Der Typ der Anfrage, in diesem Fall "GetFeatures".  
- **command**: (string) Der Befehl: "select", "distinct", "countOnly", "idsOnly" oder "statistics" (siehe unten).  
- **outFields**: (array) Eine Liste der Felder, die in der Antwort zurückgegeben werden sollen.  
- **returnGeometry**: (string) Gibt an, ob und welche Geometrieinformationen zurückgegeben werden sollen: "none", "geometry" oder "bbox".  
- **outCRS** (optional): (object) Das Koordinatenreferenzsystem für die Ausgabe.  
- **orderByFields** (optional): (array) Die Felder, nach denen die Ergebnisse sortiert werden sollen.  
- **objectIds** (optional): (array) Eine Liste von Objekt-IDs, die abgefragt werden sollen.  
- **spatialFilter** (optional): (object) Ein räumlicher Filter, um die Abfrage auf einen bestimmten Bereich zu beschränken.  
- **filter** (optional): (object) Ein attributiver Filter zur Einschränkung der Ergebnisse.  
- **limit** (optional): (int) Die maximale Anzahl an Features, die zurückgegeben werden soll.  
- **offset** (optional): (int) Der Offset für die Paginierung.  

Attributfilter: SQL oder CQL2
-----------------------------

**filter** kann einen SQL-Ausdruck, einen CQL2-Filter oder beides (beide müssen gelten) enthalten:

.. code-block:: text

    "filter": {
      "whereClause": "BEZIRK_ID = @id",
      "parameters": { "id": 101 }
    }

    "filter": { "cql2": "NAME = 'Wien' AND EW > 1000" }

    "filter": { "cql2": { "op": "=", "args": [ { "property": "NAME" }, "Wien" ] } }

- **whereClause**: SQL-Ausdruck in der Syntax der Datenquelle. Parameter werden im Ausdruck mit ``@name``
  geschrieben, der Schlüssel in **parameters** mit oder ohne ``@``. Zahlen und Wahrheitswerte werden als Literale
  eingesetzt, Texte als SQL-Strings (Anführungszeichen verdoppelt). Der Ausdruck wird gegen SQL-Injection geprüft.
- **cql2**: ein CQL2-Filter. Ein String ist CQL2-Text, ein Objekt CQL2-JSON. Unterstützt werden Vergleiche,
  ``LIKE``, ``BETWEEN``, ``IN``, ``IS NULL``, ``AND``/``OR``/``NOT`` und ``CASEI`` (ohne Groß/Kleinschreibung);
  Datum und Wahrheitswerte nur bei Datenquellen mit bekanntem SQL-Dialekt. CQL2 ist unabhängig von der Datenbank:
  gView übersetzt den Filter selbst in SQL, unbekannte Felder werden mit 400 abgelehnt.

GET-Parameter
-------------

Eine Abfrage kann auch als GET-Request gestellt werden, z. B.:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/query/0?outFields=NAME,EW&filter=EW%20%3E%201000&limit=100``

- **command**: ``select`` (Standard), ``distinct``, ``countOnly``, ``idsOnly``
- **outFields**: Feldnamen, durch Komma getrennt (Standard ``*``)
- **returnGeometry**: ``none`` (Standard), ``geometry``, ``bbox``
- **outCRS**: ``EPSG:4326`` oder ``4326``
- **orderBy**: z. B. ``NAME,EW DESC``
- **objectIds**: Objekt-IDs, durch Komma getrennt
- **where**: SQL-Ausdruck (wie **whereClause**)
- **filter**, **filter-lang**: CQL2-Filter, ``cql2-text`` (Standard) oder ``cql2-json``
- **bbox**, **bbox-crs**, **spatialOperator**: räumlicher Filter ``minx,miny,maxx,maxy``
- **limit**, **offset**: Paging

Statistik
---------

Mit ``"command": "statistics"`` liefert die Abfrage eine Tabelle statt Features, z. B. die Anzahl der Bezirke und die
Summe der Einwohner je Bundesland:

.. code-block:: json

    {
      "type": "GetFeatures",
      "command": "statistics",
      "statistics": [
        { "type": "count", "field": "BEZIRK_ID", "alias": "n" },
        { "type": "sum", "field": "EINWOHNER" }
      ],
      "groupBy": [ "LAND" ],
      "having": "n > 10",
      "orderByFields": [ "n DESC" ],
      "filter": { "cql2": "EINWOHNER > 0" },
      "limit": 5
    }

- **statistics**: **type** ``count``, ``sum``, ``min``, ``max``, ``avg``, ``stddev`` oder ``var`` (``sum``, ``avg``,
  ``stddev``, ``var`` nur für Zahlenfelder), **field** und **alias** (Name der Spalte; Standard ``{type}_{field}``).
- **groupBy** (optional): die Felder, nach denen gruppiert wird; ohne eine Zeile.
- **having** (optional): ein CQL2-Filter auf die Zeilen des Ergebnisses (Text oder JSON), mit den Namen der
  **groupBy**-Felder und Statistiken.
- **orderByFields**: Namen der **groupBy**-Felder oder Statistiken, mit ``ASC``/``DESC``.
- **filter**, **spatialFilter**, **objectIds**, **limit**, **offset** wie bei einer normalen Abfrage; **limit** und
  **offset** gelten für die Zeilen des Ergebnisses.

Als GET: ``?command=statistics&statistics=count:BEZIRK_ID:n,sum:EINWOHNER&groupBy=LAND&having=n%20%3E%2010&orderBy=n%20DESC``
(``statistics`` als ``type:field[:alias]``, durch Komma getrennt).

.. code-block:: json

    {
      "type": "FeaturesStatisticsResponse",
      "fields": [ { "name": "LAND", "type": "String" }, { "name": "n", "type": "Integer" }, { "name": "sum_EINWOHNER", "type": "Float" } ],
      "rows": [ { "LAND": "NIEDERÖSTERREICH", "n": 25, "sum_EINWOHNER": 1690879 } ],
      "exceededLimit": true
    }

Geometrie-Optionen
------------------

Für :doc:`query`, :doc:`identify` und :doc:`find`:

- **returnGeometry** ``centroid``: statt der Geometrie ein Punkt, bei Flächen sicher innerhalb, bei Linien auf halber Länge.
- **maxAllowableOffset**: Linien und Flächen werden generalisiert; Stützpunkte, die näher als dieser Abstand (in
  Einheiten von **outCRS**) sind, entfallen. Gut für Übersichtskarten.
- **geometryPrecision**: die Koordinaten werden auf diese Anzahl Nachkommastellen gerundet.

Response-Struktur
------------------  

Der Response enthält die abgefragten Features im GeoJSON-Format.  
Die Struktur des Responses ist wie folgt definiert:  

.. code-block:: json

    {
      "type": "FeatureCollection",
      "crs": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Point",
            "coordinates": [10.0, 50.0]
          },
          "properties": {
            "field1": "value1",
            "field2": 123
          }
        }
      ],
      "exceededLimit": false
    }

- **type**: (string) Der Typ der Antwort, in diesem Fall "FeatureCollection".  
- **crs** (optional): (object) Das Koordinatenreferenzsystem der Ausgabe.  
- **features**: (array) Eine Liste der abgefragten Features, jedes Feature  
  enthält Geometrie- und Attributinformationen.  
- **exceededLimit**: (bool) ``true``, wenn es mehr Features gibt als zurückgegeben wurden (**limit** oder die
  maximale Anzahl des Dienstes). Die nächste Seite beginnt bei **offset** plus der Anzahl der Features.
- Jedes Feature hat zusätzlich **oid**, die Objekt-ID.

Die anderen Befehle liefern eigene Antworten:

- ``countOnly``: ``{ "type": "FeaturesCountOnlyResponse", "count": 117 }``
- ``idsOnly``: ``{ "type": "FeaturesIdsOnlyResponse", "objectIdFieldName": "OBJECTID", "objectIds": [1, 2, 3] }``
- ``distinct``: ``{ "type": "FeaturesDistinct", "distinctField": "LAND", "distinctValues": ["Wien", "Tirol"] }``

**objectIds** wirkt auch zusammen mit einem räumlichen Filter; ``distinct`` kann nicht mit einem räumlichen Filter
kombiniert werden. Ein räumlicher Filter enthält entweder **geometry** oder **bbox**.
