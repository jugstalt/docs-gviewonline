GetFeatures Endpoint Specification
==================================

The ``GetFeatures`` endpoint is used to retrieve features from a specified layer, allowing for complex attribute and spatial queries. The response is typically in GeoJSON format, returning the requested features as a FeatureCollection. The following describes the request parameters and their usage.

Request Structure
-----------------

The request should be a JSON object with the following properties:

- ``type``: (string) Fixed value "GetFeatures", indicating the type of request.

- ``layer``: (string) The name of the layer from which features are requested.

- ``crs``: (string) The coordinate reference system in which the geometries and Bounding Box (BBOX) are specified (e.g., "EPSG:4326").

- ``bbox``: (array, optional) Defines the Bounding Box as `[xmin, ymin, xmax, ymax]` to retrieve features within the specified area.

- ``spatialFilter``: (object, optional) Used to perform spatial queries. Includes:
  
  - ``geometry``: (GeoJSON object) The geometry used as a spatial filter (e.g., a polygon to define an area of interest).
  - ``operator``: (string) Defines the spatial relationship between the geometry and the features. Possible values include:
    
    - ``within``: Select features entirely within the geometry.
    - ``intersects``: Select features that intersect with the geometry.
    - ``contains``: Select features that contain the geometry.

- ``filter``: (object, optional) Used to filter features based on their attributes. The filter object is designed to support complex logical combinations and consists of:
  
  - ``logic``: (string) The logical operator to combine conditions, either "AND" or "OR".
  - ``conditions``: (array) An array of conditions or nested filter groups. Each condition can be:
    
    - ``property``: (string) The attribute field to filter on.
    - ``operator``: (string) The comparison operator. Supported operators include:
      
      - ``equals``: The attribute must equal the given value.
      - ``not_equals``: The attribute must not equal the given value.
      - ``greater_than``: The attribute must be greater than the given value.
      - ``less_than``: The attribute must be less than the given value.
      - ``like``: A string matching pattern, e.g., "%Berlin%".
      - ``in``: The attribute value must be in the provided list of values (e.g., `"in": ["Europe", "Asia"]`).
  
  - **Nested logic** groups can also be used to allow for combinations of "AND" and "OR" logic.

- ``limit``: (integer, optional) The maximum number of features to return, allowing for control over large datasets.

- ``offset``: (integer, optional) Used for paging through results, specifying the starting point (e.g., to begin with the 100th result).

- ``distinct``: (boolean, optiona) Flag to indicate if distinct values should be returned

Example Request
---------------

.. code-block:: json

    {
      "type": "GetFeatures",
      "layer": "Layer1",
      "crs": "EPSG:4326",
      "bbox": [10.0, 50.0, 20.0, 60.0],
      "filter": {
        "logic": "AND",
        "conditions": [
          {
            "logic": "OR",
            "conditions": [
              {
                "property": "name",
                "operator": "equals",
                "value": "Berlin"
              },
              {
                "property": "population",
                "operator": "greater_than",
                "value": 1000000
              }
            ]
          },
          {
            "property": "region",
            "operator": "in",
            "value": ["Europe", "Asia"]
          }
        ]
      },
      "spatialFilter": {
        "geometry": {
          "type": "Polygon",
          "coordinates": [
            [
              [13.0, 52.0], [14.0, 52.0], [14.0, 53.0], [13.0, 53.0], [13.0, 52.0]
            ]
          ]
        },
        "operator": "intersects"
      },
      "limit": 100,
      "offset": 0
    }

Explanation of the Example
--------------------------

- ``type``: The request is of type "GetFeatures".
- ``layer``: The layer from which features are being requested is "Layer1".
- ``crs``: The request uses the EPSG:4326 coordinate reference system.
- ``bbox``: The bounding box defines the area of interest.
- ``filter``: A combination of "AND" and "OR" logic is used to select features. Features are either named "Berlin" or have a population greater than 1,000,000 and must also be in "Europe" or "Asia".
- ``spatialFilter``: The spatial filter defines a polygon and retrieves features that intersect with it.
- ``limit`` and ``offset``: Used for pagination, limiting the number of returned features to 100, starting from the 0th feature.

Use Cases
---------

- ``Attribute Filtering``: Allows for querying based on feature properties, such as names or numerical attributes.
- ``Spatial Filtering``: Enables spatial queries based on geometries, including operations like "within", "contains", or "intersects".
- ``Complex Queries``: Supports combining different logical operations to create complex queries for feature selection.