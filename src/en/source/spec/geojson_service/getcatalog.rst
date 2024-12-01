GetCatalog Endpoint Specification
=================================

The **GetCatalog** endpoint provides metadata about the available folders and services within a catalog. This endpoint allows for the navigation of the catalog structure, returning folders and services contained within a specified folder.

Request Structure
-----------------

The request should be a JSON object with the following properties:

- **type**: (string) Fixed value "GetCatalog", indicating the type of request.

- **folder**: (string, optional) The path to the folder for which the catalog contents are requested. If omitted, the root folder will be queried.

Example Request
---------------

.. code-block:: json

    {
      "type": "GetCatalog",
      "folder": "root/folder1"
    }

Explanation of the Example
--------------------------

- **type**: Specifies that this is a "GetCatalog" request.
- **folder**: Specifies the path of the folder for which the contents are requested. In this case, the folder path is "root/folder1".

Response Structure
------------------

The response contains information about the folders and services available within the specified folder. It is structured as follows:

- **type**: (string) Fixed value "GetCatalogResponse", indicating the type of response.
- **folders**: (array) A list of subfolders available within the specified folder.
- **services**: (array) A list of services available within the specified folder.

Example Response
----------------

.. code-block:: json

    {
      "type": "GetCatalogResponse",
      "folders": [
        "subfolder1",
        "subfolder2"
      ],
      "services": [
        "service1",
        "service2"
      ]
    }

Explanation of the Example
--------------------------

- **type**: The response is of type "GetCatalogResponse".
- **folders**: Lists the subfolders available within the specified folder. In this example, "subfolder1" and "subfolder2" are subfolders.
- **services**: Lists the services available within the specified folder. In this example, "service1" and "service2" are services available within the folder.

Use Cases
---------

- **Catalog Navigation**: Retrieve the subfolders and services within a specified folder to navigate the catalog structure.
- **Service Discovery**: Use the response to identify the available services within a specific folder.
- **Dynamic UI Generation**: The response can be used to dynamically generate user interface components for browsing the catalog, such as folder trees or service lists.
