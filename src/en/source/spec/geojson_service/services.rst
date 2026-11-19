Services Endpoint
=================  

The **Services** endpoint provides a list of all available geospatial data services on the server or within a specific folder.  
This endpoint allows users to browse existing services and get an overview of the available structure.  

Request Structure
-----------------

The **Services** endpoint supports only GET requests and can be accessed using the following URLs:  

- **Services in the Root Folder**  
  - ``https://{server}/geojsonservice/v1/services``  
      
    - Returns a list of all available services and folders on the server.  

- **Services in a Specific Folder**  
  - ``https://{server}/geojsonservice/v1/services/{folder}``  
      
    - Returns all services within the specified folder.  

Response Structure
------------------  

The response contains information about existing folders and services.  
The structure of the response is defined as follows:  

- **type**: (string) Fixed value "GetServicesResponse" indicating the type of response.  
- **folders**: (array) A list of subfolders available on the server or within the specified folder.  
- **services**: (array) A list of services available on the server or within the specified folder.  

Example Response
----------------

.. code-block:: json

    {
      "type": "GetServicesResponse",
      "folders": [
        "folder1",
        "folder2"
      ],
      "services": [
        "service1",
        "service2"
      ]
    }

Explanation of the Example
--------------------------  

- **type**: Indicates the type of response, in this case "GetServicesResponse".  
- **folders**: Contains a list of folders that are available. In this example, "Maps" and "TrafficData" are two existing folders.  
- **services**: Contains a list of services that are available. In this example, "WeatherMaps" and "PopulationDensity" are two available services.  

Use Cases
---------  

- **Service Discovery**: Get an overview of all services available on the server to decide which data to use.  
- **Catalog Navigation**: Browse through the available folders to find the desired data structure.  
