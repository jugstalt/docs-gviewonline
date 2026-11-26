Features Endpoint
=================  

The **Features** endpoint allows creating, updating, and deleting features  
in a specific layer. The HTTP methods POST, PUT, and DELETE are used for this purpose.  

Request Structure
-----------------  

- **POST** and **PUT** (Insert/Update Features)  

  For a POST or PUT request, the following body is used:  

  .. code-block:: json

      {
        "type": "EditFeatures",
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

  - **type**: (string) The type of request, in this case "EditFeatures".  
  - **crs** (optional): (object) The coordinate reference system used.  
  - **features**: (array) A list of features to be added or updated.  

- **DELETE** (Delete Features)  

  For a DELETE request, the following body is used:  

  .. code-block:: json

      {
        "type": "EditFeatures",
        "crs": {
          "type": "name",
          "properties": {
            "name": "EPSG:4326"
          }
        },
        "objectIds": [1, 2, 3]
      }

  - **type**: (string) The type of request, in this case "EditFeatures".  
  - **crs** (optional): (object) The coordinate reference system used.  
  - **objectIds**: (array) A list of object IDs to be deleted.  

Response Structure
------------------  

The response contains information about the success of the operation. The structure of the response is  
defined as follows:

.. code-block:: json

    {
      "succeeded": true,
      "statement": "INSERT", // INSERT,UPDATE,DELETE
      "count": 3,
      "objectIds": [12, 13, 14]
    }

- **succeeded**: (bool) Indicates whether the operation was successful.  
- **statement**: (string) The executed database statement.  
- **count**: (int) The number of affected features.  
- **objectIds**: (array) The object ids of the features in the order of the request; for ``INSERT`` the new ids
  (0 if the database can't report them).

Inserts, updates and deletes run in one transaction if the database supports it: all features or none. Features to
update give their id in **oid**. Updates and deletes only touch objects the layer shows: if the layer has a definition
query, an id outside of it (or one that doesn't exist) is an error ``404``.
