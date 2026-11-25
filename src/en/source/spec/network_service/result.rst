Result
======

All operations return the same structure (``f=json`` or ``pjson``):

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

- **operation**: the operation.
- **success**: ``false`` if nothing was found (no path, no rules) or a rule of severity ``Error`` is violated.
- **exceededLimit**: ``true`` if the result was cut because of ``MaxResultNodes``.
- **cost**: total cost (``path``).
- **srs**: spatial reference of the network (the coordinates are transformed according to ``outSR``).
- **messages**: readable report.
- **nodes**: nodes with layer and OID (empty for helper nodes), type, ``kind``, cost, ``source`` (originating node),
  ``aliasOf`` (for merged points the label of the node they lie on) and position.
- **edges**: ``kind`` (``Edge``, ``Disabled``, ``Dead``; the latter two for ``outage``), part of a line feature between ``fromMeasure`` and ``toMeasure`` with length, start and end node and
  ``coordinates`` (with ``geometry=false`` only start and end point).
- **violations** (``validate``): ``rule``, ``severity``, ``message``, ``kind`` (``Node``/``Edge``), ``node``,
  ``nodeLabel``, ``layer``, ``oid``, ``edge``/``edgeLabel``/``edgeLayer``/``edgeOid``, ``type``, ``count``, ``min``,
  ``max``, ``x``, ``y``, ``aliases``.
- **rules** (``validate``): per rule ``rule``, ``severity``, ``description``, ``checked``, ``violations``.
- **objectIds**: OIDs of the result features per layer.
- **attributes** (only with ``attributes=true``): object with the fields of the original feature, on every node and
  edge; violations have ``attributes`` and ``edgeAttributes``.

GeoJSON and CSV
---------------

- ``f=geojson``: ``FeatureCollection``; nodes and violations as ``Point``, edges as ``LineString``.
  The ``properties`` contain among others ``kind``, ``id``, ``layer``, ``oid``, ``type``, ``cost`` or ``length``,
  ``from_measure``, ``to_measure``. Edges also have ``from_node`` and ``to_node`` (node ids). With ``attributes=true`` also ``properties.attributes`` (violations also
  ``properties.edge_attributes``).
- ``f=csv``: one row per node (``id;node;type;kind;cost;source;alias_of``), for ``validate`` one row per
  violation (``rule;severity;kind;node;edge;type;count;min;max;x;y;aliases``).

Errors
------

Errors (unknown operation, invalid reference, no network, missing right) are returned like for the other GeoServices
resources as an error object (HTTP 200). ``code`` is ``504`` on timeout, ``503`` when too many searches run at the same
time, otherwise ``999``. A network built by an older version answers "rebuild network": build it again.
