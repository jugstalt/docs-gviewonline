Query Endpoint
==============  

Der **Query**-Endpunkt ermöglicht das Abfragen von Features eines bestimmten Layers,  
basierend auf verschiedenen räumlichen und attributiven Filtern.  

Request-Struktur
----------------  

Der **Query**-Endpunkt unterstützt GET- und POST-Anfragen. Der Body der Anfrage wird durch das  
folgende Objekt definiert:  

.. code-block:: json

    {
      "type": "GetFeatures",
      "command": "select",  // "select", "distinct", "countOnly", "IdsOnly"
      "outFields": ["field1", "field2"],
      "returnGeometry": "geometry",  // "none", "geometry", "bbox"
      "outCRS": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "orderByFields": ["field1 ASC", "field2 DESC"],
      "objectIds": ["1", "2", "3"],
      "spatialFilter": {
        "crs": {
          "type": "name",
          "properties": {
            "name": "EPSG:4326"
          }
        },
        // geometry OR bbox
        "geometry": {
          "type": "Polygon",
          "coordinates": [
            [
              [10.0, 50.0],
              [20.0, 50.0],
              [20.0, 60.0],
              [10.0, 60.0],
              [10.0, 50.0]
            ]
          ]
        },
        "bbox": {
          "minX": 10.0,
          "minY": 50.0,
          "maxX": 20.0,
          "maxY": 60.0
        },
        "operator": "intersects"  // "within", "intersects", "contains", "intersectsBBox"
      },
      "filter": {
        "whereClause": "field1 = @value1 AND field2 > @value2",
        "parameters": {
          "value1": "someValue",
          "value2": 100
        }
      },
      "limit": 100,
      "offset": 0
    }

- **type**: (string) Der Typ der Anfrage, in diesem Fall "GetFeatures".  
- **command**: (string) Der Befehl, z. B. "select", "distinct", "countOnly" oder "IdsOnly".  
- **outFields**: (array) Eine Liste der Felder, die in der Antwort zurückgegeben werden sollen.  
- **returnGeometry**: (string) Gibt an, ob und welche Geometrieinformationen zurückgegeben werden sollen: "none", "geometry" oder "bbox".  
- **outCRS** (optional): (object) Das Koordinatenreferenzsystem für die Ausgabe.  
- **orderByFields** (optional): (array) Die Felder, nach denen die Ergebnisse sortiert werden sollen.  
- **objectIds** (optional): (array) Eine Liste von Objekt-IDs, die abgefragt werden sollen.  
- **spatialFilter** (optional): (object) Ein räumlicher Filter, um die Abfrage auf einen bestimmten Bereich zu beschränken.  
- **filter** (optional): (object) Ein attributiver Filter zur Einschränkung der Ergebnisse.  
- **limit** (optional): (int) Die maximale Anzahl an Features, die zurückgegeben werden soll.  
- **offset** (optional): (int) Der Offset für die Paginierung.  

Response-Struktur
------------------  

Der Response enthält die abgefragten Features im GeoJSON-Format.  
Die Struktur des Responses ist wie folgt definiert:  

.. code-block:: json

    {
      "type": "FeatureCollection",
      "crs": {
        "type": "name",
        "properties": {
          "name": "EPSG:4326"
        }
      },
      "features": [
        {
          "type": "Feature",
          "geometry": {
            "type": "Point",
            "coordinates": [10.0, 50.0]
          },
          "properties": {
            "field1": "value1",
            "field2": 123
          }
        }
      ]
    }

- **type**: (string) Der Typ der Antwort, in diesem Fall "FeatureCollection".  
- **crs** (optional): (object) Das Koordinatenreferenzsystem der Ausgabe.  
- **features**: (array) Eine Liste der abgefragten Features, jedes Feature  
  enthält Geometrie- und Attributinformationen.  
