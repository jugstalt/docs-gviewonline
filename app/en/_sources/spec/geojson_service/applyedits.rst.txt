ApplyEdits Endpoint
===================

The **ApplyEdits** endpoint adds, updates and deletes features of several layers in one request: all or nothing. So
all layers must be in one database, and the database must support transactions (otherwise one layer per request). It
needs the right *Edit*.

Request Structure
-----------------

POST only: ``https://{server}/geojsonservice/v1/services/{folder}/{service}/applyEdits``

.. code-block:: json

    {
      "type": "ApplyEdits",
      "crs": { "type": "name", "properties": { "name": "EPSG:31256" } },
      "edits": [
        {
          "layerId": "0",
          "adds": [ { "geometry": { "type": "Point", "coordinates": [14000, 300000] }, "properties": { "NAME": "new" } } ],
          "updates": [ { "oid": 12, "version": "vJrQNHfvTA8pQcqInIN-D0mc", "properties": { "NAME": "changed" } } ],
          "deletes": [ 13, { "oid": 14, "version": "mSq3DUt0txWiyrSVdwjULSqU" } ]
        },
        {
          "layerId": "1",
          "adds": [ { "geometry": { "type": "Point", "coordinates": [14100, 300100] }, "properties": {} } ]
        }
      ]
    }

- **crs** (optional): the coordinate system of the geometries.
- **edits**: one entry per layer (a layer only once) with **adds** (without **oid**), **updates** (with **oid**) and
  **deletes** (object ids, or ``{ "oid": ..., "version": ... }``).
- **version** (optional, updates and deletes): the feature is only changed or deleted if it still has this version;
  otherwise the whole request is rejected with ``412``. The version is the ``ETag`` of *OGC API - Features*
  (``GET .../ogcapi/.../items/{id}``), with or without quotes; both interfaces use the same value.

Everything is checked before the first edit is written: the edits allowed per layer, geometries, objects outside the
definition query of the layer or not existing (``404``), versions (``412``).

Response Structure
------------------

.. code-block:: json

    {
      "type": "ApplyEditsResponse",
      "succeeded": true,
      "results": [
        {
          "layerId": "0",
          "adds": [ { "oid": 123, "version": "\"vJrQNHfvTA8pQcqInIN-D0mc\"" } ],
          "updates": [ { "oid": 12, "version": "\"cGFRmrRCTKM4KZt_CV78yW1W\"" } ],
          "deletes": [ 13, 14 ]
        }
      ]
    }

- **adds**, **updates**: the object ids (for new features the new ones, 0 if the database can't report them) and the
  **version** after the edit, for the next edit.
- **deletes**: the deleted object ids.
