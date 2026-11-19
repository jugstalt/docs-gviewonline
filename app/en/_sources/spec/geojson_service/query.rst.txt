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
      ]
    }

- **type**: (string) The type of response, in this case "FeatureCollection".  
- **crs** (optional): (object) The coordinate reference system of the output.  
- **features**: (array) A list of queried features, each feature  
  contains geometry and attribute information.  
