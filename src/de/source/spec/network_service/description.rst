Network Description
===================

Beschreibt das Netzwerk eines Dienstes.

``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver?f=pjson``

Antwort
-------

.. code-block:: json

    {
      "currentVersion": 10.81,
      "name": "net",
      "created": "2025-01-31 10:15:00",
      "srs": "epsg:31254",
      "nodeCount": 120000,
      "edgeCount": 118000,
      "mergedPointCount": 5000,
      "shardCount": 40,
      "operations": ["trace", "reach", "path", "supply", "info", "validate"],
      "nodeLayers": ["Trafo", "Niederspannung/Kasten"],
      "edgeLayers": ["Niederspannung/Leitung"],
      "weights": ["length"],
      "types": [ { "id": 1, "kind": "Node", "name": "Trafo" } ],
      "rules": [ { "id": "r1", "severity": "Error", "description": "..." } ]
    }

- **srs**: Koordinatensystem des Netzwerks.
- **mergedPointCount**: Punkte, die beim Aufbau mit einem Knoten zusammengeführt wurden (z. B. Kunden auf einem Kasten).
- **weights**: Werte für den Parameter ``weight``. ``length`` (Länge in Metern) gibt es immer.
- **types**: verwendete Typen (für ``filter`` und Typ-Muster).
- **rules**: Regeln für ``validate``. ``severity`` ist ``Error`` oder ``Warning``.
