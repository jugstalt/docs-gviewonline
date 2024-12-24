Features Endpoint
=================  

Der **Features**-Endpunkt ermöglicht das Erstellen, Aktualisieren und Löschen von Features  
in einem bestimmten Layer. Dafür werden die HTTP-Methoden POST, PUT und DELETE verwendet.  

Request-Struktur
----------------  

- **POST** und **PUT** (Einfügen/Update von Features)  

  Bei einer POST- oder PUT-Anfrage wird der folgende Body verwendet:  

  .. code-block:: json

      {
        "type": "EditFeatures",
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

  - **type**: (string) Der Typ der Anfrage, in diesem Fall "EditFeatures".  
  - **crs** (optional): (object) Das verwendete Koordinatenreferenzsystem.  
  - **features**: (array) Eine Liste der Features, die hinzugefügt oder aktualisiert werden sollen.  

- **DELETE** (Löschen von Features)  

  Bei einer DELETE-Anfrage wird der folgende Body verwendet:  

  .. code-block:: json

      {
        "type": "EditFeatures",
        "crs": {
          "type": "name",
          "properties": {
            "name": "EPSG:4326"
          }
        },
        "objectIds": [1, 2, 3]
      }

  - **type**: (string) Der Typ der Anfrage, in diesem Fall "EditFeatures".  
  - **crs** (optional): (object) Das verwendete Koordinatenreferenzsystem.  
  - **objectIds**: (array) Eine Liste von Objekt-IDs, die gelöscht werden sollen.  

Response-Struktur
------------------  

Der Response enthält Informationen über den Erfolg der Operation. Die Struktur des Responses ist  
wie folgt definiert:  

.. code-block:: json

    {
      "succeeded": true,
      "statement": "INSERT", // INSERT,UPDATE,DELETE
      "count": 3
    }

- **succeeded**: (bool) Gibt an, ob die Operation erfolgreich war.  
- **statement**: (string) Das ausgeführte Datenbank-Statement.  
- **count**: (int) Die Anzahl der betroffenen Features.  
