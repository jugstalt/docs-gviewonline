Map Endpoint
============

Der **GetMap** Endpunkt ermöglicht das Abrufen einer Karte basierend auf den angegebenen Parametern 
wie Layern, Bounding Box (BBox), Größe, Format und anderen Eigenschaften.

Request-Struktur
----------------

Der **GetMap** Endpunkt unterstützt sowohl GET- als auch POST-Anfragen. 

Beispiel für eine GET-Anfrage:

``https://..../map?bbox=10,10,20,20&crs=epsg:4326&laysers=0,1,2&format=png&width=800&height=600&responseFormat=image``

Die POST-Anfrage wird durch das folgende Objekt (BODY) definiert:

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

- **layers**: (array) Eine Liste von Layer-IDs, die auf der Karte angezeigt werden sollen.
  
  - Handelt es sich bei einem Layer um einen Gruppenlayer, betrifft die Sichtbarkeit alle Layer in der Gruppe.
  
  - Services haben eine Default-Sichtbarkeitsschaltung, die auch in den Capabilities des Services
    ersichtlich ist. Wenn **layers** `null` ist, wird die Default-Sichtbarkeit verwendet.
  
  - Layer können zur Default-Sichtbarkeit hinzugefügt oder entfernt werden, indem ein Vorzeichen 
    verwendet wird, z. B. `["+0", "-3"]`. Das Vorzeichen ``+`` fügt den Layer hinzu, 
    das Vorzeichen ``-`` entfernt den Layer.
  
  - Wenn Vorzeichen verwendet werden, müssen alle Layer-IDs ein Vorzeichen haben.
  
  - Ohne Vorzeichen wird genau die übergebene Sichtbarkeit verwendet und die Default-Sichtbarkeit 
    des Services wird komplett überschrieben.

- **bbox**: (object) Die Bounding Box der Karte, die die geographische Ausdehnung definiert.
- **crs** (optional): (object) Das verwendete Koordinatenreferenzsystem.
- **width**: (int) Die Breite des angeforderten Kartenbildes in Pixeln.
- **height**: (int) Die Höhe des angeforderten Kartenbildes in Pixeln.
- **format**: (string) Das Format des Kartenbildes, z. B. "png" oder "jpg".
- **transparent**: (bool) Gibt an, ob der Hintergrund transparent sein soll.
- **rotation** (optional): (double) Die Rotation der Karte in Grad.
- **dpi** (optional): (int) Die Auflösung des Bildes in DPI.
- **responseFormat**: (string) Das Format des Responses, z. B. "Url", "Base64", "Image".

Response-Struktur
------------------

Der Response enthält die angeforderte Karte oder Informationen dazu. Die Struktur des Responses ist wie folgt definiert:

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

- **type**: (string) Der Typ der Antwort, in diesem Fall "GetMapResponse".
- **imageUrl** (optional): (string) URL zum abgerufenen Kartenbild, falls der Response als URL angefordert wurde.
- **imageBase64** (optional): (string) Das Kartenbild im Base64-Format, falls dies angefordert wurde.
- **bbox**: (object) Die Bounding Box der Karte.
- **crs**: (object) Das verwendete Koordinatenreferenzsystem.
- **width**: (int) Die Breite des zurückgegebenen Kartenbildes in Pixeln.
- **height**: (int) Die Höhe des zurückgegebenen Kartenbildes in Pixeln.
- **scaleDenominator**: (double) Der Maßstab der Karte.
- **rotation** (optional): (double) Die Rotation der Karte in Grad.
- **contentType**: (string) Der MIME-Typ des zurückgegebenen Kartenbildes, z. B. "image/png".

Hinweis zum ResponseFormat
--------------------------

- **Url:** wird im Response die **imageUrl** zurückgegeben.
- **Base64:** wird das Bild im Base64-Format in **imageBase64** zurückgegeben.
- **Image:** ist der Response direkt das Bild im Binärformat.