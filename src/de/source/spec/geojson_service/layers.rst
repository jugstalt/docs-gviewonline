Layers Endpoint
===============

Der **Layers**-Endpunkt liefert alles, was ein Client braucht, um Formulare und Legenden zu bauen: Ausdehnung,
Beschreibung, erlaubte Bearbeitungen, die Eigenschaften mit Typ, Länge und Wertebereich, und den Renderer.

Request-Struktur
----------------

Nur GET:

- ``https://{server}/geojsonservice/v1/services/{folder}/{service}/layers``: alle Layer
- ``https://{server}/geojsonservice/v1/services/{folder}/{service}/layers/{layerId}``: ein Layer (``404``, wenn es ihn
  nicht gibt)

- **crs** (optional): das Koordinatensystem der Ausdehnung, z. B. ``crs=EPSG:31256``; Standard wie bei den Capabilities.

Response-Struktur
-----------------

.. code-block:: json

    {
      "type": "GetLayersResponse",
      "layers": [
        {
          "id": "0",
          "parentId": "3",
          "name": "Bezirke",
          "layerType": "FeatureLayer",
          "defaultVisibility": true,
          "geometryType": "Polygon",
          "minScaleDenominator": 0,
          "maxScaleDenominator": 0,
          "description": "...",
          "extent": { "minX": -514573.9, "minY": 138504.0, "maxX": 61824.1, "maxY": 432276.0 },
          "crs": { "type": "name", "properties": { "name": "EPSG:31256" } },
          "editOperations": [ "insert", "update", "delete" ],
          "properties": [
            {
              "name": "BEZIRK", "aliasname": "Bezirk", "type": "String",
              "length": 255, "nullable": true, "editable": true,
              "domain": { "type": "values", "values": [ "Wien", "Graz" ] }
            }
          ],
          "renderer": {
            "type": "categories",
            "field": "BEZIRK",
            "classes": [
              {
                "label": "Wien", "value": "Wien",
                "symbol": {
                  "type": "fill", "color": [176, 107, 248, 85], "outlineColor": [0, 0, 0, 255], "width": 1,
                  "legendImageBase64": "iVBORw0..."
                }
              }
            ],
            "default": null
          }
        }
      ]
    }

- **layerType**: ``FeatureLayer``, ``RasterLayer``, ``GroupLayer`` oder ``Layer``.
- **extent**, **crs**: die Ausdehnung der Daten (nur mit dem Recht *Map*).
- **editOperations**: ``insert``, ``update``, ``delete``: was der Dienst bei diesem Layer erlaubt (nur mit dem Recht *Edit*).
- **properties** (nur mit dem Recht *Query* oder *Edit*): wie in den Capabilities, zusätzlich **length**, **nullable**,
  **editable** und **domain**: ``range`` (**min**, **max**), ``values`` (**values**) oder ``dictionary``
  (**dictionary**: Beschriftung → Wert).
- **renderer** (nur mit dem Recht *Map*): eine vereinfachte Darstellung, damit ein Client abgefragte Features selbst
  einfärben kann:

  - **type**: ``single`` (ein Symbol), ``categories`` (ein Symbol je Wert von **field**), ``classes`` (ein Symbol je
    Bereich **min** bis **max** von **field**) oder ``other`` (nur Beschriftungen und Legendenbilder).
  - **classes**: Beschriftung, Wert bzw. Bereich und **symbol**; **default** ist das Symbol aller anderen Werte.
  - **symbol**: **type** (``point``, ``line``, ``fill``, ``text``, ``other``), Farben als ``[r, g, b, a]`` (0–255),
    **width** (Linien- bzw. Umrissbreite), **size** (Punkte) und **legendImageBase64**, das Legendenbild als PNG, auch
    für Symbole ohne einfache Form.
