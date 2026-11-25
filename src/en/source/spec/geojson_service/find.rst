Find Endpoint
=============

The **Find** endpoint searches a text in the text fields of several layers, case-insensitive. It needs the right
*Query*.

Request Structure
-----------------

GET or POST:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/find?searchText=eisen&layers=0,1&returnGeometry=centroid``

.. code-block:: json

    {
      "type": "Find",
      "searchText": "eisen",
      "searchFields": [ "BEZIRK", "NAME" ],
      "layers": [ "0", "1" ],
      "contains": true,
      "returnGeometry": "geometry",
      "outCRS": { "type": "name", "properties": { "name": "EPSG:4326" } },
      "limit": 10
    }

- **searchText**: the text to search.
- **searchFields** (optional): the fields to search (text fields only); without, all text fields of a layer.
- **layers** (optional): the layer ids; without, all queryable layers.
- **contains**: ``true`` (default): the field contains the text; ``false``: the field is the text.
- **returnGeometry**, **outCRS**, **maxAllowableOffset**, **geometryPrecision**: as with :doc:`query`.
- **limit** (optional): max. features per layer.

A layer only returns features it shows (the definition query of the layer). The search is a CQL2 filter
(``CASEI(field) LIKE CASEI('%text%')``), translated into SQL for each data source.

Response Structure
------------------

As with :doc:`identify`, with ``"type": "FindResponse"``: one entry per layer with hits (**layerId**, **layerName**,
**featureCollection** with **exceededLimit**).
