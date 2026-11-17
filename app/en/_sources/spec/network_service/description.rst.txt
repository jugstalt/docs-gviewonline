Network Description
===================

Describes the network of a service.

``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver?f=pjson``

Response
--------

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

- **srs**: spatial reference of the network.
- **mergedPointCount**: points merged into a node during the build (e.g. customers on a box).
- **weights**: values for the parameter ``weight``. ``length`` (length in metres) is always available.
- **types**: types in use (for ``filter`` and type patterns).
- **rules**: rules for ``validate``. ``severity`` is ``Error`` or ``Warning``.
