Layers Endpoint
===============

The **Layers** endpoint returns everything a client needs to build forms and legends: extent, description, the edits
allowed, the properties with type, length and domain, and the renderer.

Request Structure
-----------------

GET only:

- ``https://{server}/geojsonservice/v1/services/{folder}/{service}/layers``: all layers
- ``https://{server}/geojsonservice/v1/services/{folder}/{service}/layers/{layerId}``: one layer (``404`` if it doesn't
  exist)

- **crs** (optional): the coordinate system of the extent, e.g. ``crs=EPSG:31256``; default as in the capabilities.

Response Structure
------------------

.. code-block:: json

    {
      "type": "GetLayersResponse",
      "layers": [
        {
          "id": "0",
          "parentId": "3",
          "name": "Bezirke",
          "layerType": "FeatureLayer",
          "defaultVisibility": true,
          "geometryType": "Polygon",
          "minScaleDenominator": 0,
          "maxScaleDenominator": 0,
          "description": "...",
          "extent": { "minX": -514573.9, "minY": 138504.0, "maxX": 61824.1, "maxY": 432276.0 },
          "crs": { "type": "name", "properties": { "name": "EPSG:31256" } },
          "editOperations": [ "insert", "update", "delete" ],
          "properties": [
            {
              "name": "BEZIRK", "aliasname": "Bezirk", "type": "String",
              "length": 255, "nullable": true, "editable": true,
              "domain": { "type": "values", "values": [ "Wien", "Graz" ] }
            }
          ],
          "renderer": {
            "type": "categories",
            "field": "BEZIRK",
            "classes": [
              {
                "label": "Wien", "value": "Wien",
                "symbol": {
                  "type": "fill", "color": [176, 107, 248, 85], "outlineColor": [0, 0, 0, 255], "width": 1,
                  "legendImageBase64": "iVBORw0..."
                }
              }
            ],
            "default": null
          }
        }
      ]
    }

- **layerType**: ``FeatureLayer``, ``RasterLayer``, ``GroupLayer`` or ``Layer``.
- **extent**, **crs**: the extent of the data (only with the right *Map*).
- **editOperations**: ``insert``, ``update``, ``delete``: what the service allows for this layer (only with the right *Edit*).
- **properties** (only with the right *Query* or *Edit*): as in the capabilities, plus **length**, **nullable**,
  **editable** and **domain**: ``range`` (**min**, **max**), ``values`` (**values**) or ``dictionary``
  (**dictionary**: label → value).
- **renderer** (only with the right *Map*): a simplified form, so a client can color queried features itself:

  - **type**: ``single`` (one symbol), ``categories`` (a symbol per value of **field**), ``classes`` (a symbol per
    range **min** to **max** of **field**) or ``other`` (only labels and legend images).
  - **classes**: label, value or range and **symbol**; **default** is the symbol of all other values.
  - **symbol**: **type** (``point``, ``line``, ``fill``, ``text``, ``other``), colors as ``[r, g, b, a]`` (0–255),
    **width** (line or outline width), **size** (points) and **legendImageBase64**, the legend image as PNG, also for
    symbols without a simple form.
