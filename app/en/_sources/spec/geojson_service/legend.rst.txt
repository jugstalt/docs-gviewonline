Legend Endpoint
===============  

The **Legend** endpoint allows for retrieving the legend for individual layers of a service.  
The request can be made as either a GET or POST request.  

Request Structure
-----------------

The **Legend** endpoint supports GET and POST requests:  

``https://{server}/geojsonservice/v1/services/{folder}/{service}/legend?width=20&height=20``  

For a POST request, the following body is used:  

.. code-block:: json

    {
      "type": "GetLegend",
      "width": 20,
      "height": 20,
      "dpi": 96
    }

- **type**: (string) The type of request, in this case "GetLegend".  
- **width**: (int) The width of a legend item in pixels.  
- **height**: (int) The height of a legend item in pixels.  
- **dpi** (optional): (int) The resolution of the legend in DPI.  

Response Structure
------------------  

The response contains information about individual layers and their legend items.  
The structure of the response is defined as follows:  

.. code-block:: json

    {
      "type": "GetLegendResponse",
      "layers": [
        {
          "id": "layer1",
          "name": "Layer 1",
          "layerType": "FeatureLayer",
          "minScaleDenominator": 1000,
          "maxScaleDenominator": 5000,
          "items": [
            {
              "label": "Gebäude",
              "imageBase64": "iVBORw0KGgoAAAANSUhEUg...",
              "imageContentType": "image/png",
              "width": 20,
              "height": 20
            }
          ]
        }
      ]
    }

- **type**: (string) The type of response, in this case "GetLegendResponse".  
- **layers**: (array) A list of layers for which legend information is provided.  
    
  - **id**: (string) The ID of the layer.  
  - **name**: (string) The name of the layer.  
  - **layerType**: (string) The type of layer, e.g., "FeatureLayer".  
  - **minScaleDenominator** (optional): (double) The minimum scale at which the layer is visible.  
  - **maxScaleDenominator** (optional): (double) The maximum scale at which the layer is visible.  
  - **items**: (array) A list of legend items for this layer.  
    
    - **label** (optional): (string) The label of the legend item.  
    - **imageBase64** (optional): (string) The image of the legend item as a Base64 string.  
    - **imageContentType** (optional): (string) The MIME type of the image, e.g., "image/png".  
    - **width**: (int) The width of the legend item in pixels.  
    - **height**: (int) The height of the legend item in pixels.  
