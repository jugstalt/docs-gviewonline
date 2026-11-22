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
  (commas inside ``[ ]`` don't separate). A pattern can be **negated** with a leading ``!`` for the whole pattern:
  ``!a | b`` means every type of the kind except ``a`` and ``b`` (helper nodes stay out, as with ``*``). A pattern
  that excludes every type is an error.
- a **location**: ``@x,y`` or ``@x,y~radius`` = the nearest network node to that location, in the spatial reference of
  the network or in ``inSR``. This includes **helper nodes**, i.e. line ends that only arise during the build (e.g. where
  a *Leitung* hangs on an *Anlagen Verbinder* at a station). The radius is given in network units, the default is
  10 × the build tolerance. If no node lies within the radius, the request fails. The comma inside ``@x,y`` does not
  split the list, e.g. ``start=@5,0, Trafo%231``. When several nodes lie at the place (separate tiers, see
  :ref:`network-service-connect`), ``tier`` chooses the node of the tier; when more than one is left, the request fails
  and lists them (then give one by ``Layer#OID`` or id).

Merged points resolve to the node they were merged into.
For ``path`` (``from``, ``to``) and ``info`` (``node``) the reference must result in exactly one node.

Line ends are labelled in the results like ``end of Verbinder#12, Leitung#3456``.

Example: ``filter=![*,Anlagen Verbinder,*]`` keeps a trace started at a line end on its branch instead of running back
over the station.

Edge references
---------------

Parameters that specify edges (``disable`` of all searches and of ``outage``) take a comma separated list of:

- **Layer#OID**: all parts of the line feature.
- **Layer#OID/part**: one part of the line feature.
- a **raw edge id** (number).

Common parameters
-----------------

For ``trace``, ``reach``, ``path``, ``supply`` and ``outage``:

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Parameter
     - Description
   * - ``filter``
     - Only edges of these types are used (type pattern).
   * - ``tier``
     - Only these tiers (Ebenen, comma separated names), see :ref:`network-service-tiers`.
   * - ``stopAtSources``
     - ``trace`` with ``tier``: ``false`` does not stop at the sources of the tier.
   * - ``block``
     - Nodes that are blocked for this search.
   * - ``unblock``
     - Nodes that are passable although they are blocked in the network.
   * - ``disable``
     - Edges (see *Edge references*) that are switched off for this one search only.
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
   * - ``snap``
     - ``true``: (with geometry) the ends of every edge part are set exactly onto the location of its from/to node,
       so the result is a valid network (e.g. for power-flow software that needs a snapped network). Merged points are
       output at the location of their node. A shift larger than the build tolerance is reported in ``messages``.
       Default ``false``.
   * - ``outSR``
     - EPSG code (``4326`` or ``epsg:4326``). Default: spatial reference of the network.
   * - ``inSR``
     - EPSG code of the coordinates in ``x``/``y`` and in every ``@x,y`` reference. Default: spatial reference of the
       network. The radius stays in network units.
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

outage
------

Computes the supply with and without the edges that are switched off, and reports which customers lose their supply.
Only the part of the network that is connected to the switched-off edges is examined.

Parameters: ``disable`` (required: the edges to switch off), ``sources``, ``sinks`` (both default to ``supplySources``
and ``customers`` of the network configuration), ``filter``, ``block``, ``unblock``, ``weight`` etc.

``.../networkserver/outage?disable=Leitung%2312&f=pjson``

Result:

- Node ``kind`` ``Affected``: customers that were supplied before but are not supplied after the switch-off. Merged
  points are listed as alias rows.
- ``messages`` with the counts: affected, still supplied (mesh) and not supplied before.
- Edge ``kind`` ``Disabled`` (switched off) and ``Dead`` (no supply any more).

nearest
-------

Nodes near a location. Every node is returned once, the nearest first; ``cost`` is the distance.

Parameters: ``x``, ``y`` (required), ``radius`` (network units, default 10 × the build tolerance), ``count``
(default 1), ``inSR``, ``outSR``, ``f``.

``.../networkserver/nearest?x=5&y=0&count=3&f=pjson``

Node ``kind``: ``Nearest``.

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
