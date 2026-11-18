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
