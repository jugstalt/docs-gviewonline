Legend Endpoint
===============

Der **Legend** Endpunkt ermöglicht das Abrufen der Legende für die einzelnen Layer eines Services. 
Die Anfrage kann entweder als GET- oder POST-Anfrage erfolgen.

Request-Struktur
----------------

Der **Legend** Endpunkt unterstützt GET- und POST-Anfragen:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/legend?width=20&height=20``

Bei einer POST-Anfrage wird der folgende Body verwendet:

.. code-block:: json

    {
      "type": "GetLegend",
      "width": 20,
      "height": 20,
      "dpi": 96
    }

- **type**: (string) Der Typ der Anfrage, in diesem Fall "GetLegend".
- **width**: (int) Die Breite eines Legendenitems in Pixeln.
- **height**: (int) Die Höhe eines Legendenitems in Pixeln.
- **dpi** (optional): (int) Die Auflösung der Legende in DPI.

Response-Struktur
------------------

Der Response enthält Informationen zu den einzelnen Layern und deren Legendenitems. 
Die Struktur des Responses ist wie folgt definiert:

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

- **type**: (string) Der Typ der Antwort, in diesem Fall "GetLegendResponse".
- **layers**: (array) Eine Liste der Layer, für die Legendeninformationen bereitgestellt werden.
  
  - **id**: (string) Die ID des Layers.
  
  - **name**: (string) Der Name des Layers.
  
  - **layerType**: (string) Der Typ des Layers, z. B. "FeatureLayer".
  
  - **minScaleDenominator** (optional): (double) Die minimale Maßstabszahl, bei der der Layer sichtbar ist.
  
  - **maxScaleDenominator** (optional): (double) Die maximale Maßstabszahl, bei der der Layer sichtbar ist.
  
  - **items**: (array) Eine Liste der Legendenitems für diesen Layer.
    
    - **label** (optional): (string) Die Beschriftung des Legendenitems.
    
    - **imageBase64** (optional): (string) Das Bild des Legendenitems als Base64-String.
    
    - **imageContentType** (optional): (string) Der MIME-Typ des Bildes, z. B. "image/png".
    
    - **width**: (int) Die Breite des Legendenitems in Pixeln.
    
    - **height**: (int) Die Höhe des Legendenitems in Pixeln.