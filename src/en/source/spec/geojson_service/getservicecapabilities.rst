GetServiceCapabilities Endpoint Specification
=============================================

The ``GetServiceCapabilities`` endpoint provides metadata about the available layers and services offered by the API. This endpoint returns details about the supported requests, layer properties, and other capabilities that are available for interaction.

Request Structure
-----------------

The request for ``GetServiceCapabilities`` is straightforward. It does not require any specific parameters apart from specifying the request type.

- ``type``: (string) Fixed value "GetServiceCapabilities", indicating the type of request.

Example Request
---------------

.. code-block:: json

    {
      "type": "GetServiceCapabilities"
    }

Response Structure
------------------

The response contains metadata about the service, including supported requests and information about all available layers. The response is structured as follows:

- ``type``: (string) Fixed value "GetServiceCapabilitiesResponse".
- ``supportedRequests``: (array) A list of requests that are supported by the service. Each request includes:
  
  - ``name``: (string) The name of the request (e.g., "GetMap", "GetFeatures").
  - ``description``: (string) A brief description of the request.
  - ``properties``: (object, optional) Additional information about the capabilities of the request. Each request may include specific properties such as:
    
    - For ``GetMap``:
      
      - ``maxImageWidth``: (integer) The maximum allowable width of the generated map image in pixels.
      - ``maxImageHeight``: (integer) The maximum allowable height of the generated map image in pixels.
      - ``supportedFormats``: (array) A list of supported image formats (e.g., ["image/png", "image/jpeg"]).
    
    - For ``GetFeatures``:
      
      - ``maxFeaturesLimit``: (integer) The maximum number of features that can be returned in a single request.

- ``layers``: (array) A list of available layers, where each layer includes:
  
  - ``id``: (string) A unique identifier for the layer.
  - ``name``: (string) The name of the layer.
  - ``minScaleDenominator``: (float, optional) The minimum scale denominator at which the layer should be visible.
  - ``maxScaleDenominator``: (float, optional) The maximum scale denominator at which the layer should be visible.
  - ``styles``: (array, optional) An array of styles available for this layer. Each style includes:
    
    - ``id``: (string) The style identifier.
    - ``title``: (string) A descriptive title for the style.

  - ``properties``: (array) An array of property objects, each describing an attribute of the layer. Each property includes:
    
    - ``name``: (string) The name of the property.
    - ``type``: (string) The data type of the property. Possible values include:
      
      - ``string``: Text values.
      - ``integer``: Whole numbers.
      - ``float``: Decimal numbers.
      - ``boolean``: True or false values.
      - ``date``: Date values.
    
    - ``isPrimaryKey``: (boolean, optional) Indicates whether this property is the primary key (e.g., ObjectID) for the layer.

  - ``geometryType``: (string) The type of geometry associated with the layer (e.g., "Point", "LineString", "Polygon").

Example Response
----------------

.. code-block:: json

    {
      "type": "GetServiceCapabilitiesResponse",
      "supportedRequests": [
        {
          "name": "GetMap",
          "description": "Generates a map image from one or more specified layers.",
          "properties": {
            "maxImageWidth": 4096,
            "maxImageHeight": 4096,
            "supportedFormats": ["image/png", "image/jpeg"]
          }
        },
        {
          "name": "GetFeatures",
          "description": "Retrieves features from a specified layer based on attribute or spatial queries.",
          "properties": {
            "maxFeaturesLimit": 1000
          }
        }
      ],
      "layers": [
        {
          "id": "layer1",
          "name": "Layer 1",
          "minScaleDenominator": 1000,
          "maxScaleDenominator": 50000,
          "styles": [
            {
              "id": "default",
              "title": "Default Style"
            },
            {
              "id": "highlight",
              "title": "Highlight Style"
            }
          ],
          "properties": [
            {
              "name": "name",
              "type": "string"
            },
            {
              "name": "population",
              "type": "integer",
              "isPrimaryKey": false
            }
          ],
          "geometryType": "Polygon"
        },
        {
          "id": "layer2",
          "name": "Layer 2",
          "minScaleDenominator": 500,
          "maxScaleDenominator": 10000,
          "styles": [
            {
              "id": "default",
              "title": "Default Style"
            }
          ],
          "properties": [
            {
              "name": "elevation",
              "type": "float",
              "isPrimaryKey": false
            }
          ],
          "geometryType": "LineString"
        }
      ]
    }

Explanation of the Example
--------------------------

- ``type``: The response is of type "GetServiceCapabilitiesResponse".
- ``supportedRequests``: Lists the supported requests. In this example, "GetMap" and "GetFeatures" are supported, with a brief description of each.
  
  - ``properties``: Provides additional details about the capabilities of each request. For ``GetMap``, it includes maximum image size and supported formats. For ``GetFeatures``, it specifies the maximum number of features that can be returned.
- ``layers``: Provides details about each layer available through the service.
  
  - ``id`` and ``name``: Identify the layer.
  - ``minScaleDenominator`` and ``maxScaleDenominator``: Define the scale range for visibility of the layer.
  - ``styles``: Lists available styles for the layer, including identifiers and descriptive titles.
  - ``properties``: Lists the attributes of the layer, with each property's name, data type, and an optional indicator if it is the primary key.
  - ``geometryType``: Indicates the type of geometry associated with the layer.

Use Cases
---------

- ``Service Discovery``: Retrieve metadata about all available layers and their capabilities.
- ``Client Applications``: Use the response to dynamically create requests based on available layers, properties, and supported formats.
- ``Visualization Planning``: Understand the scale range and styling options for each layer to customize the map display.