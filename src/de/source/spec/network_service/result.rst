Ergebnis
========

Alle Operationen liefern dieselbe Struktur (``f=json`` oder ``pjson``):

.. code-block:: json

    {
      "operation": "trace",
      "success": true,
      "exceededLimit": false,
      "cost": 0,
      "srs": "epsg:31254",
      "messages": ["Trace from 1 source(s)"],
      "nodes": [
        { "id": 1234, "label": "Trafo#36", "layer": "Trafo", "oid": 36, "type": "Trafo",
          "kind": "Reached", "cost": 0, "source": "", "aliasOf": "", "x": 0.0, "y": 0.0 }
      ],
      "edges": [
        { "id": 5678, "label": "Leitung#1", "layer": "Niederspannung/Leitung", "oid": 1, "type": "Leitung",
          "kind": "Edge", "length": 12.3, "fromMeasure": 0.0, "toMeasure": 12.3, "from": 1234, "to": 1235,
          "coordinates": [[0.0, 0.0], [1.0, 1.0]] }
      ],
      "violations": [],
      "rules": [],
      "objectIds": { "Niederspannung/Leitung": [1, 2, 3] }
    }

- **operation**: die Operation.
- **success**: ``false``, wenn nichts gefunden wurde (kein Weg, keine Regeln) bzw. eine Regel der Schwere ``Error`` verletzt ist.
- **exceededLimit**: ``true``, wenn das Ergebnis wegen ``MaxResultNodes`` abgeschnitten wurde.
- **cost**: Gesamtkosten (``path``).
- **srs**: Koordinatensystem des Netzwerks (die Koordinaten sind nach ``outSR`` transformiert).
- **messages**: lesbarer Bericht.
- **nodes**: Knoten mit Layer und OID (bei Hilfsknoten leer), Typ, ``kind``, Kosten, ``source`` (Ausgangsknoten),
  ``aliasOf`` (bei zusammengeführten Punkten die Bezeichnung des Knotens, auf dem sie liegen) und Position.
- **edges**: ``kind`` (``Edge``, ``Disabled``, ``Dead``; die letzten beiden bei ``outage``), Teil eines Linien-Features zwischen ``fromMeasure`` und ``toMeasure`` mit Länge, Anfangs- und Endknoten und
  ``coordinates`` (bei ``geometry=false`` nur Anfangs- und Endpunkt).
- **violations** (``validate``): ``rule``, ``severity``, ``message``, ``kind`` (``Node``/``Edge``), ``node``,
  ``nodeLabel``, ``layer``, ``oid``, ``edge``/``edgeLabel``/``edgeLayer``/``edgeOid``, ``type``, ``count``, ``min``,
  ``max``, ``x``, ``y``, ``aliases``.
- **rules** (``validate``): je Regel ``rule``, ``severity``, ``description``, ``checked``, ``violations``.
- **objectIds**: OIDs der Ergebnis-Features je Layer.
- **attributes** (nur mit ``attributes=true``): Objekt mit den Feldern des Original-Features, an jedem Knoten und jeder
  Kante; Verletzungen haben ``attributes`` und ``edgeAttributes``.

GeoJSON und CSV
---------------

- ``f=geojson``: ``FeatureCollection``; Knoten und Verletzungen als ``Point``, Kanten als ``LineString``.
  Die ``properties`` enthalten u. a. ``kind``, ``id``, ``layer``, ``oid``, ``type``, ``cost`` bzw. ``length``,
  ``from_measure``, ``to_measure``. Kanten haben außerdem ``from_node`` und ``to_node`` (Knoten-Ids). Mit ``attributes=true`` zusätzlich ``properties.attributes`` (bei Verletzungen auch
  ``properties.edge_attributes``).
- ``f=csv``: eine Zeile pro Knoten (``id;node;type;kind;cost;source;alias_of``), bei ``validate`` eine Zeile pro
  Verletzung (``rule;severity;kind;node;edge;type;count;min;max;x;y;aliases``).

Fehler
------

Fehler (unbekannte Operation, ungültige Referenz, kein Netzwerk, fehlendes Recht) werden wie bei den anderen
GeoServices-Ressourcen als Fehlerobjekt geliefert (HTTP 200). ``code`` ist ``504`` bei Zeitüberschreitung, ``503``, wenn zu
viele Suchen gleichzeitig laufen, sonst ``999``. Ein Netzwerk, das mit einer älteren Version gebaut wurde, meldet
"rebuild network": Es muss neu gebaut werden.
