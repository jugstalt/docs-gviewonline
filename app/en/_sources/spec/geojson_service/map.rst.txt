Map Endpoint
============  

The **GetMap** endpoint allows for retrieving a map based on specified parameters  
such as layers, bounding box (BBox), size, format, and other properties.  

Request Structure
-----------------

The **GetMap** endpoint supports both GET and POST requests.  

Example of a GET request:  

``https://..../map?bbox=10,10,20,20&crs=epsg:4326&layers=0,1,2&format=png&width=800&height=600&responseFormat=image``  

The POST request is defined by the following object (BODY):  

.. code-block:: json

    {
      "type": "GetMap",
      "layers": ["0", "1"],
      "bbox": {
        "minX": 10.0,
        "minY": 50.0,
        "maxX": 20.0,
        "maxY": 60.0
      },
      "crs": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "width": 800,
      "height": 600,
      "format": "png",
      "transparent": false,
      "rotation": 0.0,
      "dpi": 96,
      "responseFormat": "Url"
    }

- **layers**: (array) A list of layer IDs to be displayed on the map.  
    
  - If a layer is a group layer, visibility applies to all layers within the group.  
  - Services have a default visibility setting, which can also be seen in the service's capabilities.  
    If **layers** is `null`, the default visibility is used.  
  - Layers can be added to or removed from the default visibility by using a prefix,  
    e.g., `["+0", "-3"]`. The ``+`` prefix adds the layer, while the ``-`` prefix removes it.  
  - When using prefixes, all layer IDs must have a prefix.  
  - Without prefixes, the exact visibility passed is applied, fully overriding the service's  
    default visibility.  

- **bbox**: (object) The bounding box of the map, defining the geographical extent.  
- **crs** (optional): (object) The coordinate reference system used.  
- **width**: (int) The width of the requested map image in pixels.  
- **height**: (int) The height of the requested map image in pixels.  
- **format**: (string) The format of the map image, e.g., "png" or "jpg".  
- **transparent**: (bool) Indicates whether the background should be transparent.  
- **rotation** (optional): (double) The rotation of the map in degrees.  
- **dpi** (optional): (int) The resolution of the image in DPI.  
- **responseFormat**: (string) The format of the response, e.g., "Url", "Base64", "Image".  

Response Structure
------------------  

The response contains the requested map or related information.  
The structure of the response is defined as follows:  

.. code-block:: json

    {
      "type": "GetMapResponse",
      "imageUrl": "https://{server}/path/to/generated/map.png",
      "imageBase64": null,
      "bbox": {
        "minX": 10.0,
        "minY": 50.0,
        "maxX": 20.0,
        "maxY": 60.0
      },
      "crs": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "width": 800,
      "height": 600,
      "scaleDenominator": 50000,
      "rotation": 0.0,
      "contentType": "image/png"
    }

- **type**: (string) The type of response, in this case "GetMapResponse".  
- **imageUrl** (optional): (string) URL to the retrieved map image if the response was requested as a URL.  
- **imageBase64** (optional): (string) The map image in Base64 format if this was requested.  
- **bbox**: (object) The bounding box of the map.  
- **crs**: (object) The coordinate reference system used.  
- **width**: (int) The width of the returned map image in pixels.  
- **height**: (int) The height of the returned map image in pixels.  
- **scaleDenominator**: (double) The scale of the map.  
- **rotation** (optional): (double) The rotation of the map in degrees.  
- **contentType**: (string) The MIME type of the returned map image, e.g., "image/png".  

Note on ResponseFormat
----------------------  

- **Url:** The **imageUrl** is returned in the response.  
- **Base64:** The image is returned in Base64 format in **imageBase64**.  
- **Image:** The response directly returns the image in binary format.  
