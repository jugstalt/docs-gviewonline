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

- **Carto:** network module page, tab **Ebenen**, **Refresh metadata**.
- **Command line:** ``gView.Cmd --command Network -u refresh-metadata -mxl map.mxl`` (or ``-config net.json``, optionally ``-output <network directory>``).

Only ``supplySources``, ``customers``, ``rules`` and ``tiers`` in ``network.json`` are rewritten (tier patterns are checked
against the types of the built network). The server opens the network again when ``network.json`` changes.

.. _network-service-tiers:

Tiers (Ebenen)
--------------

A tier restricts searches to a part of the network, e.g. high (HS), medium (MS) or low (NS) voltage. The network configuration
has a section ``tiers`` (written into ``network.json`` at build and by *Refresh metadata*):

.. code-block:: json

   "tiers": [
     { "name": "MS", "order": 2, "filter": "Mittelspannung.*", "sources": "[*,Umspannwerk,*]", "sinks": "[*,Trafo,*]" },
     { "name": "NS", "order": 3, "filter": "Niederspannung.*", "sources": "[*,Trafo,*]", "sinks": "[*,Kunde,*]", "block": "[*,Schalter,*]" }
   ]

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Field
     - Meaning
   * - ``name``
     - Name of the tier, unique, no comma. Used as the ``tier`` parameter.
   * - ``order``
     - Rank: lower is higher up (HS before MS before NS).
   * - ``filter``
     - Edge types of the tier (type pattern, see below).
   * - ``nodes``
     - Optional node pattern: nodes that belong to the tier although no edge of it touches them.
   * - ``sources``, ``sinks``
     - Node references or patterns. They replace ``supplySources`` / ``customers`` when the tier is searched
       (if empty, the global ones are used).
   * - ``block``
     - Nodes that are blocked in every search on the tier.

**Membership.** Edges belong to a tier by their type. A node belongs to every tier whose edges touch it, so a transformer
between medium and low voltage is in both.

**Pattern syntax** (also for ``filter``): ``|`` is *or*, ``&`` is *and* (binds tighter than ``|``), ``!`` negates one term.
``Mittelspannung.* & !Mittelspannung.Freileitung | Niederspannung.*``. A leading ``!`` in a pattern without ``&`` and without
further ``!`` negates the whole pattern as before (``!a | b`` = everything except a and b).

**Searches** (``trace``, ``reach``, ``path``, ``supply``, ``outage``, ``validate``) take ``tier`` (comma separated names):

- The edge types used are those of the tiers, cut by ``filter``. Tier blocks are added to ``block``.
- ``supply`` / ``outage``: the sources of the tier with the lowest ``order`` and the sinks of the tier with the highest
  ``order`` are used (``tier=MS,NS``: sources of MS, sinks of NS). ``sources`` / ``sinks`` of the request win.
- ``trace`` with a tier stops at the sources of the tier (they are reported as ``Source``). ``stopAtSources=false`` goes on.
- ``validate``: a rule with the attribute ``tier`` only checks the edges and nodes of that tier (subject and counted edges);
  with ``tier`` in the request all rules are restricted this way. End types are not restricted.

Without ``tier`` nothing changes. The tiers are listed in the ``tiers`` of the NetworkServer resource. In *Carto* the
network search tool has the dropdown **Ebene**.

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
     - Larger results are cut (``exceededLimit``).
   * - ``TimeoutSeconds``
     - 120
     - Longer searches are answered with error 504.
   * - ``MaxParallel``
     - 2
     - Searches running at the same time, others wait.
   * - ``MaxLoadedShards``
     - 512
     - Shards loaded per network (0 = unlimited).
   * - ``IdleMinutes``
     - 30
     - Unused networks are dropped from memory after this time (0 = never).
