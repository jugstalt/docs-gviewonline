Query Endpoint
==============  

The **Query** endpoint allows querying features of a specific layer,  
based on various spatial and attribute filters.  

Request Structure
-----------------

The **Query** endpoint supports GET and POST requests. The body of the request is defined by the  
following object:  

.. code-block:: json

    {
      "type": "GetFeatures",
      "command": "select",  // "select", "distinct", "countOnly", "IdsOnly"
      "outFields": ["field1", "field2"],
      "returnGeometry": "geometry",  // "none", "geometry", "bbox"
      "outCRS": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "orderByFields": ["field1 ASC", "field2 DESC"],
      "objectIds": ["1", "2", "3"],
      "spatialFilter": {
        "crs": {
          "type": "name",
          "properties": {
            "name": "EPSG:4326"
          }
        },
        // geometry OR bbox
        "geometry": {
          "type": "Polygon",
          "coordinates": [
            [
              [10.0, 50.0],
              [20.0, 50.0],
              [20.0, 60.0],
              [10.0, 60.0],
              [10.0, 50.0]
            ]
          ]
        },
        "bbox": {
          "minX": 10.0,
          "minY": 50.0,
          "maxX": 20.0,
          "maxY": 60.0
        },
        "operator": "intersects"  // "within", "intersects", "contains", "intersectsBBox"
      },
      "filter": {
        "whereClause": "field1 = @value1 AND field2 > @value2",
        "parameters": {
          "value1": "someValue",
          "value2": 100
        }
      },
      "limit": 100,
      "offset": 0
    }

- **type**: (string) The type of request, in this case "GetFeatures".  
- **command**: (string) The command, e.g., "select", "distinct", "countOnly", or "IdsOnly".  
- **outFields**: (array) A list of fields to be returned in the response.  
- **returnGeometry**: (string) Indicates whether and which geometry information should be returned: "none", "geometry", or "bbox".  
- **outCRS** (optional): (object) The coordinate reference system for the output.  
- **orderByFields** (optional): (array) The fields by which the results should be sorted.  
- **objectIds** (optional): (array) A list of object IDs to be queried.  
- **spatialFilter** (optional): (object) A spatial filter to limit the query to a specific area.  
- **filter** (optional): (object) An attribute filter to restrict the results.  
- **limit** (optional): (int) The maximum number of features to return.  
- **offset** (optional): (int) The offset for pagination.  

Attribute filter: SQL or CQL2
-----------------------------

**filter** can hold a SQL expression, a CQL2 filter or both (both must apply):

.. code-block:: text

    "filter": {
      "whereClause": "BEZIRK_ID = @id",
      "parameters": { "id": 101 }
    }

    "filter": { "cql2": "NAME = 'Wien' AND EW > 1000" }

    "filter": { "cql2": { "op": "=", "args": [ { "property": "NAME" }, "Wien" ] } }

- **whereClause**: SQL expression in the syntax of the data source. Parameters are written as ``@name`` in the
  expression, the key in **parameters** with or without ``@``. Numbers and booleans become literals, texts SQL strings
  (quotes doubled). The expression is checked against SQL injection.
- **cql2**: a CQL2 filter. A string is CQL2-Text, an object CQL2-JSON. Supported are comparisons, ``LIKE``,
  ``BETWEEN``, ``IN``, ``IS NULL``, ``AND``/``OR``/``NOT`` and ``CASEI`` (case-insensitive); dates and booleans only
  for data sources with a known SQL dialect. CQL2 does not depend on the database: gView translates the filter into
  SQL itself, unknown fields are rejected with 400.

GET parameters
--------------

A query can also be sent as GET request, e.g.:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/query/0?outFields=NAME,EW&filter=EW%20%3E%201000&limit=100``

- **command**: ``select`` (default), ``distinct``, ``countOnly``, ``idsOnly``
- **outFields**: field names, comma separated (default ``*``)
- **returnGeometry**: ``none`` (default), ``geometry``, ``bbox``
- **outCRS**: ``EPSG:4326`` or ``4326``
- **orderBy**: e.g. ``NAME,EW DESC``
- **objectIds**: object ids, comma separated
- **where**: SQL expression (like **whereClause**)
- **filter**, **filter-lang**: CQL2 filter, ``cql2-text`` (default) or ``cql2-json``
- **bbox**, **bbox-crs**, **spatialOperator**: spatial filter ``minx,miny,maxx,maxy``
- **limit**, **offset**: paging

Response Structure
------------------  

The response contains the queried features in GeoJSON format.  
The structure of the response is defined as follows:  

.. code-block:: json

    {
      "type": "FeatureCollection",
      "crs": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Point",
            "coordinates": [10.0, 50.0]
          },
          "properties": {
            "field1": "value1",
            "field2": 123
          }
        }
      ],
      "exceededLimit": false
    }

- **type**: (string) The type of response, in this case "FeatureCollection".  
- **crs** (optional): (object) The coordinate reference system of the output.  
- **features**: (array) A list of queried features, each feature  
  contains geometry and attribute information.  
- **exceededLimit**: (bool) ``true`` if there are more features than returned (**limit** or the max. number of
  the service). The next page starts at **offset** plus the number of features.
- Each feature also has **oid**, the object id.

The other commands have their own responses:

- ``countOnly``: ``{ "type": "FeaturesCountOnlyResponse", "count": 117 }``
- ``idsOnly``: ``{ "type": "FeaturesIdsOnlyResponse", "objectIdFieldName": "OBJECTID", "objectIds": [1, 2, 3] }``
- ``distinct``: ``{ "type": "FeaturesDistinct", "distinctField": "LAND", "distinctValues": ["Wien", "Tirol"] }``

**objectIds** also applies together with a spatial filter; ``distinct`` can't be combined with a spatial filter. A
spatial filter holds either **geometry** or **bbox**.
