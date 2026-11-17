Operations
==========

Node references
---------------

Parameters that specify nodes (``start``, ``from``, ``to``, ``sources``, ``sinks``, ``node``, ``block``, ``unblock``)
take a comma separated list of:

- **Layer#OID**, e.g. ``Trafo#5929`` or ``Niederspannung/Kasten#23660`` (layer name or path; if the name is ambiguous
  the path is required). In URLs ``#`` is sent as ``%23``: ``start=Trafo%235929``.
- a **raw node id** (number).
- a **type pattern**, e.g. ``[*,Trafo,*]`` or ``Niederspannung.Kasten``: all nodes of that type
  (commas inside ``[ ]`` don't separate).

Merged points resolve to the node they were merged into.
For ``path`` (``from``, ``to``) and ``info`` (``node``) the reference must result in exactly one node.

Common parameters
-----------------

For ``trace``, ``reach``, ``path`` and ``supply``:

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Parameter
     - Description
   * - ``filter``
     - Only edges of these types are used (type pattern).
   * - ``block``
     - Nodes that are blocked for this search.
   * - ``unblock``
     - Nodes that are passable although they are blocked in the network.
   * - ``weight``
     - Weight for the costs. Default ``length`` (metres), others see *Network Description*.
   * - ``maxCost``
     - Cost limit (decimal number, ``.`` or ``,``). Mainly for ``reach``.

For all operations with a result (``info``: only ``f``):

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Parameter
     - Description
   * - ``geometry``
     - ``false``: edges only as a straight line between their nodes. Default: the real parts of the lines.
   * - ``attributes``
     - ``true``: additionally returns the fields of the original features (without geometry) for nodes, merged points,
       edges and violations. Needs the right ``query`` in addition to ``network``, otherwise the request fails
       (forbidden). Without ``attributes`` nothing changes.
   * - ``outFields``
     - Comma separated field names, default ``*`` (all fields). Applied per layer: each layer returns only the requested
       fields it has (case-insensitive). Unknown names are ignored without error, a layer without any of the fields
       returns ``{}``. The id field is only returned if requested (the OID is already in ``oid``).
   * - ``outSR``
     - EPSG code (``4326`` or ``epsg:4326``). Default: spatial reference of the network.
   * - ``f``
     - ``json``, ``pjson`` (indented), ``geojson`` or ``csv``. Without ``f``: HTML form.

trace
-----

Follows the **connectivity** from the start nodes up to blocked nodes. Returns all reachable nodes and all edges;
there is no cost limit.

Parameters: ``start`` (required), ``filter``, ``block``, ``unblock``.

``.../networkserver/trace?start=Trafo%2336&f=geojson&outSR=4326``

With attributes: ``.../networkserver/trace?start=Trafo%2336&attributes=true&outFields=E_TP&f=pjson``

Node ``kind``: ``Reached``, ``Blocked``.

reach
-----

**Cost-limited** search from the start nodes: the search ends at ``maxCost`` (measured in ``weight``). Returns the
shortest-path tree: every node with its cost (distance from the start) and the edges of the tree.

Parameters: ``start`` (required), ``maxCost``, ``weight``, ``filter``, ``block``, ``unblock``.

``.../networkserver/reach?start=Trafo%2336&maxCost=250&f=pjson``

Node ``kind``: ``Start``, ``Reached``, ``Blocked``.

*Difference to trace:* ``trace`` follows the network as long as it is connected and not blocked; ``reach`` stops at
``maxCost`` and returns the cost per node.

path
----

Shortest path between two nodes. If there is none, ``success`` is ``false``. ``cost`` holds the total cost, the nodes
the cost along the path.

Parameters: ``from``, ``to`` (required), ``weight``, ``filter``, ``block``, ``unblock``.

``.../networkserver/path?from=Trafo%2336&to=Niederspannung/Kasten%2323660&f=json``

Node ``kind``: ``From``, ``Via``, ``To``.

supply
------

Checks which sources supply the sinks. One node per sink with ``kind`` ``Supplied`` (exactly one source),
``NotSupplied`` or ``Multiple``; ``source`` names the sources.

Parameters: ``sources``, ``sinks`` (required), ``weight``, ``filter``, ``block``, ``unblock``.

info
----

Data of a node: type, position, shard, blocked, merged points, adjacent edges and neighbour nodes
(``kind`` ``Node``, ``Neighbor``).

Parameters: ``node`` (required), ``f`` (``json`` or ``pjson``).

validate
--------

Checks the rules of the network configuration (``network.json``) over the whole network. ``success`` is ``false`` if a
rule of severity ``Error`` is violated (warnings don't count) or there are no rules.

Parameters: ``rules`` (rule ids, comma separated; optional, default: all rules), ``geometry``, ``outSR``, ``f``.

``.../networkserver/validate?f=csv``
