GetMap Endpoint Specification
=============================

The ``GetMap`` endpoint is used to generate a map image from one or more specified layers. The request allows specifying parameters such as bounding box, image dimensions, rotation, and output format to control the rendering of the map. The response can be either an image represented as a URL link or as a base64-encoded string.

Request Structure
-----------------

The request should be a JSON object with the following properties:

- ``type``: (string) Fixed value "GetMap", indicating the type of request.

- ``bbox``: (array) Defines the Bounding Box as `[xmin, ymin, xmax, ymax]` to specify the area to be rendered.

- ``layers``: (array) A list of layer names to be included in the map.

- ``crs``: (string) The coordinate reference system in which the bounding box is specified (e.g., "EPSG:4326").

- ``width``: (integer) The width of the requested map image in pixels.

- ``height``: (integer) The height of the requested map image in pixels.

- ``format``: (string) The desired image format for the map (e.g., "image/png", "image/jpeg").

- ``transparent``: (boolean, optional) Specifies whether the background of the map should be transparent (useful for overlaying on other maps).

- ``styles``: (array, optional) An array of style identifiers to be applied to the layers.

- ``responseFormat``: (string, optional) Specifies the desired response format. Possible values include:
  - ``"link"``: The response will include a URL link to the generated map image.
  - ``"base64"``: The response will include the map image as a base64-encoded string.

- ``dpi``: (integer, optional) The desired resolution of the map image in dots per inch (DPI). This can be used to generate high-resolution images.

- ``rotation``: (float, optional) The rotation angle of the map in degrees, measured clockwise from true north. This allows for non-north-oriented map views.

Response Structure
------------------

The response can be either of the following, and also includes metadata about the generated image:

1. ``Image URL``: A URL link to the generated map image, along with the actual bounding box, image dimensions, scale, rotation, and format.

.. code-block:: json

    {
      "type": "GetMapResponse",
      "imageUrl": "https://example.com/map.png",
      "bbox": [10.0, 50.0, 20.0, 60.0],
      "width": 800,
      "height": 600,
      "scaleDenominator": 50000,
      "rotation": 45.0,
      "format": "image/png"
    }

2. ``Base64 Encoded Image``: The image data as a base64-encoded string, along with the actual bounding box, image dimensions, scale, rotation, and format.

.. code-block:: json

    {
      "type": "GetMapResponse",
      "imageBase64": "iVBORw0KGgoAAAANSUhEUgAAA...",
      "bbox": [10.0, 50.0, 20.0, 60.0],
      "width": 800,
      "height": 600,
      "scaleDenominator": 50000,
      "rotation": 45.0,
      "format": "image/png"
    }

Example Request
---------------

.. code-block:: json

    {
      "type": "GetMap",
      "bbox": [10.0, 50.0, 20.0, 60.0],
      "layers": ["Layer1", "Layer2"],
      "crs": "EPSG:4326",
      "width": 800,
      "height": 600,
      "format": "image/png",
      "transparent": true,
      "responseFormat": "link",
      "dpi": 150,
      "rotation": 45.0
    }

Explanation of the Example
--------------------------

- ``type``: The request is of type "GetMap".
- ``bbox``: The bounding box specifies the geographic area to be rendered in the map.
- ``layers``: The map will include "Layer1" and "Layer2".
- ``crs``: The coordinate reference system used is EPSG:4326.
- ``width`` and ``height``: The map image will be 800 pixels wide and 600 pixels high.
- ``format``: The output format for the map is "image/png".
- ``transparent``: The background of the map will be transparent, allowing for easy overlaying.
- ``responseFormat``: Specifies that the response should return a link to the generated image.
- ``dpi``: The resolution of the map image is set to 150 DPI for better quality.
- ``rotation``: The map will be rotated 45 degrees clockwise from true north.

Use Cases
---------

- ``Basic Map Generation``: Generate a map from one or more layers using a specified bounding box and image dimensions.
- ``Overlay Maps``: Use the transparency option to generate map layers that can be overlaid on other maps.
- ``Custom Styling``: Apply different styles to layers to control how features are represented on the map.
- ``Image Retrieval Options``: Retrieve the generated map as a URL link or as a base64-encoded image, providing flexibility in how the image is used.
- ``High-Resolution Maps``: Use the DPI option to generate high-quality, detailed map images.
- ``Scale Information``: The response includes the scale denominator, which helps in understanding the scale at which the map was generated.
- ``Non-North-Oriented Maps``: Use the rotation option to generate maps that are not oriented to true north, which is useful for specific analyses or visualization needs.