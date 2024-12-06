(Service)Capabilities Endpoint
==============================

Der **Capabilities** Endpunkt liefert detaillierte Informationen zu einem bestimmten Geodaten-Service, 
einschließlich unterstützter Operationen, Layer und deren Eigenschaften.

- **GET,POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/capabilities``
    
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/capabilities``
    
Request-Struktur
----------------

Der **Capabilities** Endpunkt unterstützt GET- und POST-Anfragen und hat die folgende Struktur:

``https://{server}/geojsonservice/v1/services/{folder}/{service}/capabilities?crs=epsg:4326``

Bei POST-Anfragen muss im Body folgendes übergeben werden:

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

- **crs** (optional): Das verwendete Koordinatenreferenzsystem. Falls nicht angegeben, 
- wird das Standard-Koordinatensystem verwendet.


Response-Struktur
------------------

Der Response enthält detaillierte Informationen zum angefragten Service. Die Struktur des Responses
ist wie folgt definiert:

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
          "maxFeaturesLimit": 0,
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/legend",
          "httpMethods": [
            "GET"
          ]
        },
        {
          "name": "query",
          "maxFeaturesLimit": 1000,
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/{layerId}",
          "httpMethods": [
            "GET",
            "POST"
          ]
        },
        {
          "name": "features",
          "url": "https://{server}/geojsonservice/v1/services/{folder}/{service}/{layerId}",
          "httpMethods": [
            "POST",
            "PUT",
            "DELETE"
          ]
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
      "units": "meters",
      "layers": [
        {
          "id": "layer1",
          "name": "Layer 1",
          "layerType": "FeatureLayer",
          "defaultVisibility": true,
          "geometryType": "Point",
          "minScaleDenominator": 1000,
          "maxScaleDenominator": 5000,
          "styles": [
            {
              "name": "Default Style",
              "title": "Standard"
            }
          ],
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

- **type**: Der Typ der Antwort, in diesem Fall "GetServiceCapabilitiesResponse".
- **mapTitle**: Der Titel der Karte, die durch den Service bereitgestellt wird.
- **description**: Eine Beschreibung des Services.
- **copyright**: Copyright-Informationen für die Daten.
- **supportedRequests**: Eine Liste der unterstützten Anfragen, die der Service ermöglicht.
- **crs**: Das verwendete Koordinatenreferenzsystem.
- **fullExtent**: Die maximale Ausdehnung der Daten.
- **initialExtent**: Die anfängliche Ausdehnung der Daten.
- **units**: Die Maßeinheit der Karte (z. B. "meters").
- **layers**: Informationen zu den Layern, die vom Service bereitgestellt werden.

**LayerInfo**
-------------

Das **LayerInfo**-Objekt beschreibt die verfügbaren Layer innerhalb eines Services:

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
      "styles": [
        {
          "name": "Default Style",
          "title": "Standard"
        }
      ],
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

- **id**: Eine eindeutige ID für den Layer.
- **parentId**: Die ID des übergeordneten Layers, falls vorhanden.
- **name**: Der Name des Layers.
- **layerType**: Der Typ des Layers, z. B. "FeatureLayer", "RasterLayer", "GroupLayer".
- **defaultVisibility**: Gibt an, ob der Layer standardmäßig sichtbar ist.
- **geometryType**: Der Geometrietyp des Layers, z. B. "Point" oder "Polygon".
- **minScaleDenominator** / **maxScaleDenominator**: Maßstabsbeschränkungen für die Sichtbarkeit des Layers.
- **styles**: Eine Liste von Stilen, die auf den Layer angewendet werden können.
- **supportedOperations**: Eine Liste der unterstützten Operationen, z. B. ["query", "features.post", "features.put", "features.delete"].
- **properties**: Eine Liste der Eigenschaften des Layers, einschließlich Name, Alias und Typ.

**LayerProperty**
-----------------

Das **LayerProperty**-Objekt beschreibt eine Eigenschaft eines Layers:

.. code-block:: json

    {
      "name": "property1",
      "aliasname": "Property 1",
      "type": "String",
      "isPrimaryKey": true
    }

- **name**: Der interne Name der Eigenschaft.
- **aliasname**: Der Aliasname, der in der Benutzeroberfläche angezeigt wird.
- **type**: Der Datentyp der Eigenschaft, z. B. "String", "Integer".
- **isPrimaryKey**: Gibt an, ob diese Eigenschaft der Primärschlüssel des Layers ist.