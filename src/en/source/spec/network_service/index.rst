Network Service Specification
=============================

The **NetworkServer** is a resource of every map service of the *gView Server*. It offers searches on a
*SpatialNET* network (e.g. a low-voltage grid) that was built from the layers of the map
(*NetworkModule* of the map, see *gView Carto*).

The interface is modelled on the resources of the ArcGIS REST API (``.../services/{service}/{Resource}``,
parameter ``f=json|pjson``, HTML form in the browser). It is a **gView API** and **not compatible** with the
Esri *Utility Network Service*.

Functions:

* **Network Description:** figures, layers, weights, types and rules of the network.
* **Trace:** connectivity from start nodes up to blocked nodes.
* **Reach:** cost-limited search (shortest-path tree).
* **Path:** shortest path between two nodes.
* **Supply:** supply of sinks by sources.
* **Outage:** customers that lose their supply when edges are switched off.
* **Nearest:** nodes near a location.
* **Info:** data of a node and its neighbours.
* **Validate:** check of the network rules.

Structure of the API links
--------------------------

``{service}`` is the service name, optionally with folder (``{folder}/{service}``).

**Network Description**

- **GET**

  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver``

**Operations**

- **GET/POST**

  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/trace``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/reach``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/path``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/supply``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/outage``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/nearest``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/info``
  - ``https://{server}/geoservices/rest/services/[{folder}/]{service}/networkserver/validate``

Without the parameter ``f`` every resource returns an HTML page or a form to try it out.
Parameters are sent as query string, form fields or JSON body (``application/json``).

Examples (service ``sqlite/net``):

.. code-block:: text

    /geoservices/rest/services/sqlite/net/networkserver?f=pjson
    /geoservices/rest/services/sqlite/net/networkserver/trace?start=Trafo%2336&f=geojson&outSR=4326
    /geoservices/rest/services/sqlite/net/networkserver/reach?start=Trafo%2336&maxCost=250&f=pjson

.. toctree::
   :maxdepth: 1
   :caption: Table of contents:

   description
   operations
   result
   administration
