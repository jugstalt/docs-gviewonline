.. _network-service-administration:

Access, build and configuration
===============================

Access
------

- The service needs the right ``network``, granted per user in the security settings.
- The service list shows ``NetworkServer`` as a type only if the network is built **and** the user has the right.
  The HTML page of the MapServer then shows the button *NetworkServer*.

Location
--------

``{services-folder}/networks/{folder}/{service}.network``

The folder can be changed with ``networks-folder`` in ``_config/mapserver.json``. The network is deleted together with
the service. The ``directory`` setting of the network configuration is only used by *gView Carto* and *gView.Cmd*,
the server ignores it.

Building the network
--------------------

- **Server administration:** network icon of the service, **Build** (background job with log, *manage* users only).
  Reports: ``build.log``, ``duplicates.json``, ``merged.json``, ``diagnostics.json``, ``conflicts.json``.
- **Command line:** ``gView.Cmd --command Network -u build -mxl map.mxl``.
- Or copy a built network directory to the location above.

The built network directory also contains ``spatial.idx`` (all nodes, line ends and merged points with their location)
and ``edges.idx``. **Networks built before this must be rebuilt**, otherwise ``@x,y`` references, ``nearest``, edge
references and ``outage`` fail with "build the network again".

Network configuration
---------------------

Besides the build settings, the network configuration (written into ``network.json`` at build) has two node patterns
that are used as defaults by ``outage``:

- ``supplySources``: the supply sources, e.g. ``[*,Trafo,*]``.
- ``customers``: the customers, e.g. ``[*,Kunde,*]``.

In *gView Carto* they are set on the network module page, tab **General**, card **Supply**.

Refresh metadata
----------------

Supply sources, customers, rules and tiers do not depend on the data. To change them without building the shards again:

- **Carto:** network module page, tab **Tiers**, **Refresh metadata**.
- **Command line:** ``gView.Cmd --command Network -u refresh-metadata -mxl map.mxl`` (or ``-config net.json``, optionally ``-output <network directory>``).

Only ``supplySources``, ``customers``, ``rules`` and ``tiers`` in ``network.json`` are rewritten (tiers and patterns are
checked against the types of the built network). ``separateTiers`` and ``connectRules`` apply at build: build the network
again after changing them. The server opens the network again when ``network.json`` changes.

.. _network-service-tiers:

Tiers (Ebenen)
--------------

A tier restricts searches to a part of the network, e.g. high (HS), medium (MS) or low (NS) voltage. **The tier of a type is
the value of its first facet** (e.g. ``Ebene``): ``[Mittelspannung,Leitung,Kabel]`` belongs to the tier ``Mittelspannung``.
Every value of the first facet that edge types use is a tier, also without an entry. In *Carto*, one group per tier gives
the value through the type template ``{group-name}`` of the first facet.

The section ``tiers`` of the network configuration (written into ``network.json`` at build and by *Refresh metadata*) only
adds settings:

.. code-block:: json

   "tiers": [
     { "name": "Mittelspannung", "order": 2, "sources": "[*,Umspannwerk,*]", "sinks": "[*,Trafo,*]" },
     { "name": "Niederspannung", "order": 3, "sources": "[*,Trafo,*]", "sinks": "[*,Kunde,*]", "block": "[*,Schalter,*]" }
   ]

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Field
     - Meaning
   * - ``name``
     - A value of the first facet. Used as the ``tier`` parameter.
   * - ``order``
     - Rank: lower is higher up (HS before MS before NS). Empty: the number of the facet value.
   * - ``sources``, ``sinks``
     - Node references or patterns. They replace ``supplySources`` / ``customers`` when the tier is searched
       (if empty, the global ones are used). With ``separateTiers``, line ends of the tier may connect to nodes of these types.
   * - ``block``
     - Nodes that are blocked in every search on the tier.

**Membership.** Edges belong to the tier of their type. A node belongs to the tier of its type and to every tier whose edges
touch it, so a transformer between medium and low voltage is in both.

**Pattern syntax:** ``[Mittelspannung,*,*]`` has one entry per facet (``*`` = any). ``|`` is *or*, ``&`` is *and* (binds
tighter than ``|``), ``!`` negates one term: ``[Mittelspannung,*,*] & ![*,*,Freileitung] | [Niederspannung,*,*]``. A leading
``!`` in a pattern without ``&`` and without further ``!`` negates the whole pattern (``!a | b`` = everything except a and b).
``untyped`` are the helper nodes (line ends without a point); with separate tiers every tier has a helper type of its own,
e.g. ``untyped & [Niederspannung,*,*]``. The short form ``Mittelspannung.*`` is still read.

**Searches** (``trace``, ``reach``, ``path``, ``supply``, ``outage``, ``validate``) take ``tier`` (comma separated names):

- The edge types used are those of the tiers, cut by ``filter``. Tier blocks are added to ``block``.
- ``supply`` / ``outage``: the sources of the tier with the lowest ``order`` and the sinks of the tier with the highest
  ``order`` are used (``tier=MS,NS``: sources of MS, sinks of NS). ``sources`` / ``sinks`` of the request win.
- ``trace`` with a tier stops at the sources the tier itself names (reported as ``Source``), not at the global supply sources. ``stopAtSources=false`` goes on.
- ``validate``: a rule with the attribute ``tier`` only checks the edges and nodes of that tier (subject and counted edges);
  with ``tier`` in the request all rules are restricted this way. The end types of an edge rule need no cut: the ends are ends of
  edges of the tier, so they belong to the tier whatever their type. A rule that has nothing left to check (for example the rule
  tier ``NS`` and ``tier=MS`` in the request) is skipped, and the result says so.
- ``@x,y``: when several nodes lie at the place (separate tiers), the node of the tier is taken.

Without ``tier`` nothing changes. The tiers are listed in the ``tiers`` of the NetworkServer resource. In *Carto* the
network search tool has the dropdown **Ebene**.

.. _network-service-connect:

Connecting and splitting at build
---------------------------------

By default the build connects everything within the tolerance: line ends and points become one node, and a node at the
inside of a line splits it. When the tiers are kept by different departments, elements often lie on top of each other by
chance, e.g. a low voltage joint on a vertex of a medium voltage cable. These settings of the network configuration control this:

.. code-block:: json

   "separateTiers": true,
   "nodesOnLine": "ignore",
   "lineEndsOnLine": "report",
   "connectRules": [
     { "at": "inside", "nodes": "[Niederspannung,Trenner,*]", "edges": "[Niederspannung,*,*]", "action": "connect" }
   ]

- ``separateTiers`` (default ``false``): connect and split only within a tier. Line ends of a tier also connect to its
  ``sources`` (type patterns, e.g. the transformer of the low voltage). Elements without a tier connect to everything. Real
  nodes of different tiers at one place are no conflict but two nodes; line ends of different tiers at one place give two
  helper nodes (a transition between tiers needs a real node). When a line end may connect to several nodes of one place,
  they are merged as with ``nodeConflicts`` (``priorityByToc``: the layer higher in the TOC wins, the others become aliases).
- ``connectRules``: the first rule that matches decides; without a match the tiers decide (or: everything connects).
- ``nodesOnLine`` and ``lineEndsOnLine`` (``connect``, ``ignore`` or ``report``, default ``connect``): whether a real node or
  a line end (T junction) at the inside of a line splits it when no rule matches and, with ``separateTiers``, both are of one
  tier. A T junction that is not split stays unconnected; with ``report`` the places are listed in the build log.

The decision for a split: the first rule that matches, then another tier (``ignore``), then ``nodesOnLine`` /
``lineEndsOnLine``. For line ends: the first rule that matches, then the ``sources`` of the tier, then the tiers.

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Field
     - Meaning
   * - ``at``
     - ``end``: a line end meets a node (connect?). ``inside``: a node lies at the inside of a line (split?); the node is a
       real node or the helper node of a T junction (``untyped``). ``any`` (default): both.
   * - ``nodes``
     - Node types (pattern), e.g. ``[Niederspannung,Trenner,*]`` or ``untyped & [Niederspannung,*,*]``.
   * - ``edges``
     - Edge types (pattern).
   * - ``action``
     - ``connect`` (connect or split), ``ignore`` (do not connect, silently) or ``report`` (do not connect and report it in
       the build: ``ConnectionRefused`` / ``SplitRefused`` with the location and both objects, probably a data error).

The example describes a network whose lines are already cut at the real nodes: only switches split a low voltage line,
other nodes split nothing, T junctions are reported. In *Carto*: network module page, tab **Connections** (*Separate tiers*
and the two defaults at the top, the rules below). In the pattern editor the switch **untyped** of a row means the helper
nodes of these types (``untyped & [Niederspannung,*,*]``); **Text** shows the whole pattern as text, e.g. for ``&`` and ``!``.
Build the network again after a change.

Configuration (appsettings, section ``Network``)
------------------------------------------------

.. list-table::
   :header-rows: 1
   :widths: 25 15 60

   * - Setting
     - Default
     - Meaning
   * - ``MaxResultNodes``
     - 50000
     - Results with more nodes or more edges are cut (``exceededLimit``). The search itself runs completely.
   * - ``TimeoutSeconds``
     - 120
     - Longer searches are answered with error 504; the time also covers reading the geometry and attributes of
       the result.
   * - ``MaxParallel``
     - 2
     - Searches running at the same time. Others wait at most ``TimeoutSeconds`` for a free slot, then error 503.
       The description and the forms do not wait.
   * - ``MaxLoadedShards``
     - 512
     - Shards loaded per network (0 = unlimited).
   * - ``IdleMinutes``
     - 30
     - Unused networks are dropped from memory after this time (0 = never).
