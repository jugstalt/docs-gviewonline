Identify Endpoint
=================

The **Identify** endpoint returns the features of several layers at a place: at a point (with a tolerance in pixels)
or along a line or in a polygon. It needs the right *Query*.

Request Structure
-----------------

GET or POST:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/identify?geometry=15155,300038&crs=31256&mapExtent=-200000,200000,100000,450000&imageWidth=800&imageHeight=600``

.. code-block:: json

    {
      "type": "Identify",
      "geometry": { "type": "Point", "coordinates": [15155.4, 300038.1] },
      "crs": { "type": "name", "properties": { "name": "EPSG:31256" } },
      "layerOption": "visible",
      "layers": [ "-4" ],
      "tolerance": 3,
      "mapExtent": { "minX": -200000, "minY": 200000, "maxX": 100000, "maxY": 450000 },
      "imageWidth": 800,
      "imageHeight": 600,
      "dpi": 96,
      "returnGeometry": "geometry",
      "outCRS": { "type": "name", "properties": { "name": "EPSG:4326" } },
      "limit": 10
    }

- **geometry**: a point, a line or a polygon (GeoJSON). With GET ``x,y`` or a GeoJSON geometry.
- **crs**: the coordinate system of **geometry** and **mapExtent**.
- **layerOption**: ``visible`` (default): the visible layers drawn at the scale of the map; ``top``: only the topmost
  of them with hits; ``all``: every queryable layer, regardless of visibility and scale.
- **layers** (optional): layer ids as with GetMap: none = the default visibility, ids = exactly these (a group shows all
  its layers), ``+id``/``-id`` = add to or remove from the default visibility.
- **tolerance**: tolerance around a point in pixels (default 3).
- **mapExtent**, **imageWidth**, **imageHeight**, **dpi**: the map the client shows. They give the scale and the
  tolerance in map units. Required for ``visible`` and ``top``.
- **returnGeometry**: ``geometry`` (default), ``bbox``, ``centroid`` or ``none``; **maxAllowableOffset** and **geometryPrecision** as with :doc:`query`.
- **outCRS** (optional): the coordinate system of the results; default **crs**.
- **limit** (optional): max. features per layer (default and maximum: the max. number of the service).

A layer only returns features it shows (the definition query of the layer).

Response Structure
------------------

.. code-block:: json

    {
      "type": "IdentifyResponse",
      "results": [
        {
          "layerId": "0",
          "layerName": "Bezirke",
          "featureCollection": {
            "type": "FeatureCollection",
            "crs": { "type": "name", "properties": { "name": "EPSG:4326" } },
            "features": [ { "type": "Feature", "oid": 1, "geometry": { "...": "..." }, "properties": { "BEZIRK": "Eisenstadt (Stadt)" } } ],
            "exceededLimit": false
          }
        }
      ]
    }

- **results**: one entry per layer with hits, the topmost layer first.
- **featureCollection**: as with :doc:`query`, with **exceededLimit** if the layer has more hits than **limit**.
