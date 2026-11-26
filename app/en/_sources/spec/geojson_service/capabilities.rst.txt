(Service)Capabilities Endpoint
==============================  

The **Capabilities** endpoint provides detailed information about a specific geospatial service,  
including supported operations, layers, and their properties.  

- **GET, POST**  
   
  - ``https://{server}/geojsonservice/v1/services/{service}/capabilities``  

  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/capabilities``  

Request Structure
-----------------

The **Capabilities** endpoint supports GET and POST requests and follows the structure below:  

``https://{server}/geojsonservice/v1/services/{folder}/{service}/capabilities?crs=epsg:4326``  

For POST requests, the following must be passed in the body:  
 


.. code-block:: json

    {
      "type": "GetServiceCapabilities",
      "crs": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      }
    }

- **crs** (optional): The coordinate reference system used. If not specified,  
  WGS84 (EPSG:4326) is used; **fullExtent** and **initialExtent** are given in it.  

Response Structure
------------------  

The response contains detailed information about the requested service.  
The structure of the response is defined as follows:  

.. code-block:: json

    {
      "type": "GetServiceCapabilitiesResponse",
      "mapTitle": "",
      "description": "",
      "copyright": "",
      "supportedRequests": [
        {
          "name": "map",
          "maxImageWidth": 8128,
          "maxImageHeight": 8128,
          "supportedFormats": [
            "png",
            "jpg"
          ],
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/map",
          "httpMethods": [
            "GET",
            "POST"
          ]
        },
        {
          "name": "legend",
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/legend",
          "httpMethods": [ "GET", "POST" ]
        },
        {
          "name": "layers",
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/layers",
          "httpMethods": [ "GET" ]
        },
        {
          "name": "find",
          "maxFeaturesLimit": 1000,
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/find",
          "httpMethods": [ "GET", "POST" ]
        },
        {
          "name": "identify",
          "maxFeaturesLimit": 1000,
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/identify",
          "httpMethods": [ "GET", "POST" ]
        },
        {
          "name": "query",
          "maxFeaturesLimit": 1000,
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/query/{layerId}",
          "httpMethods": [ "GET", "POST" ]
        },
        {
          "name": "features",
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/features/{layerId}",
          "httpMethods": [ "POST", "PUT", "DELETE" ]
        },
        {
          "name": "applyEdits",
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/applyEdits",
          "httpMethods": [ "POST" ]
        }
      ],
      "crs": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "fullExtent": {
        "minX": 10.0,
        "minY": 50.0,
        "maxX": 20.0,
        "maxY": 60.0
      },
      "initialExtent": {
        "minX": 10.0,
        "minY": 50.0,
        "maxX": 15.0,
        "maxY": 55.0
      },
      "units": "Meters",
      "layers": [
        {
          "id": "layer1",
          "name": "Layer 1",
          "layerType": "FeatureLayer",
          "defaultVisibility": true,
          "geometryType": "Point",
          "minScaleDenominator": 1000,
          "maxScaleDenominator": 5000,
          "supportedOperations": ["query", "features.post", "features.put", "features.delete"],
          "properties": [
            {
              "name": "property1",
              "aliasname": "Property 1",
              "type": "String",
              "isPrimaryKey": true
            }
          ]
        }
      ]
    }

- **type**: The type of response, in this case "GetServiceCapabilitiesResponse".  
- **mapTitle**: The title of the map provided by the service.  
- **description**: A description of the service.  
- **copyright**: Copyright information for the data.  
- **supportedRequests**: The requests of the service the user has the rights for, with URL, HTTP methods and limits (**maxFeaturesLimit**: max. features per request). Clients ignore names they don't know.  
- **crs**: The coordinate reference system used.  
- **fullExtent**: The maximum extent of the data.  
- **initialExtent**: The initial extent of the data.  
- **units**: The unit of the map in **crs**, e.g. "Meters" or "DecimalDegrees".  
- **layers**: Information about the layers provided by the service.  

**LayerInfo**  
-------------  

The **LayerInfo** object describes the available layers within a service:  

.. code-block:: json

    {
      "id": "layer1",
      "parentId": null,
      "name": "Layer 1",
      "layerType": "FeatureLayer",
      "defaultVisibility": true,
      "geometryType": "Point",
      "minScaleDenominator": 1000,
      "maxScaleDenominator": 5000,
      "supportedOperations": ["query", "features.post", "features.put", "features.delete"],
      "properties": [
        {
          "name": "property1",
          "aliasname": "Property 1",
          "type": "String",
          "isPrimaryKey": true
        }
      ]
    }

- **id**: A unique ID for the layer.  
- **parentId**: The ID of the parent layer, if applicable.  
- **name**: The name of the layer.  
- **layerType**: The type of layer, e.g., "FeatureLayer", "RasterLayer", "GroupLayer".  
- **defaultVisibility**: Indicates whether the layer is visible by default.  
- **geometryType**: The geometry type of the layer, e.g., "Point" or "Polygon".  
- **minScaleDenominator** / **maxScaleDenominator**: Scale constraints for layer visibility.  
- **supportedOperations**: A list of supported operations, e.g., ["query", "features.post", "features.put", "features.delete"].  
- **properties**: A list of the layer’s properties, including name, alias, and type.  

**LayerProperty**  
-----------------  

The **LayerProperty** object describes a property of a layer:  

.. code-block:: json

    {
      "name": "property1",
      "aliasname": "Property 1",
      "type": "String",
      "isPrimaryKey": true
    }

- **name**: The internal name of the property.  
- **aliasname**: The alias name displayed in the user interface.  
- **type**: The data type of the property, e.g., "String", "Integer".  
- **isPrimaryKey**: Indicates whether this property is the primary key of the layer.  
