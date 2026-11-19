Info Endpoint
=============  

Der **Info**-Endpunkt liefert grundlegende Informationen über die GeoJSON-API,  
einschließlich der unterstützten Endpunkte, der Version und der Token-Einstellungen.  

Request-Struktur
----------------

Der **Info**-Endpunkt unterstützt ausschließlich GET-Anfragen mit der folgenden URL:  

.. code-block::

    GET /geojsonservice/v1/info

Response-Struktur
------------------  

Der Response enthält allgemeine Informationen über die API und ist wie folgt aufgebaut:

.. code-block:: json

    {
      "type": "GetInfoResponse",
      "version": "1.0.0",
      "tokenMaxExpireMinutes": 10,
      "endPoints": {
        "token": [
          {
            "method": "GET",
            "url": "https://localhost:44331/geojsonservice/v1/token?clientId={client_id}&clientSecret={client_secret}&expireMinutes={expire_minutes}"
          },
          {
            "method": "POST",
            "url": "https://localhost:44331/geojsonservice/v1/token",
            "contentType": "application/x-www-form-urlencoded",
            "body": "{client_id}&clientSecret={client_secret}&expireMinutes={expire_minutes}"
          }
        ],
        "services": [
          {
            "method": "GET",
            "url": "https://localhost:44331/geojsonservice/v1/services"
          }
        ]
      }
    }

- **type**: (string) Der Typ der Antwort, in diesem Fall "GetInfoResponse".  
- **version**: (string) Die Version der API.  
- **tokenMaxExpireMinutes**: (int) Die maximale Gültigkeitsdauer eines Tokens in Minuten.  
- **endPoints**: (object) Eine Liste der unterstützten Endpunkte mit Details zu Methoden, URLs und optionalen Parametern.  
    
  - **token**: (array) Endpunkte für die Token-Generierung.  
      
    - **method**: (string) Die HTTP-Methode, z. B. "GET" oder "POST".  
    - **url**: (string) Die URL für den Token-Endpunkt.  
    - **contentType** (optional): (string) Der MIME-Typ des Inhalts, z. B. "application/x-www-form-urlencoded".  
    - **body** (optional): (string) Der Body der Anfrage, wenn POST verwendet wird.  
    
  - **services**: (array) Endpunkte für den Zugriff auf die Services.  
      
    - **method**: (string) Die HTTP-Methode, z. B. "GET".  
    - **url**: (string) Die URL für den Zugriff auf die Services.  

Verwendungsmöglichkeiten
------------------------  

- **API-Dokumentation**: Ermöglicht es Nutzern, unterstützte Endpunkte und deren  
  Details direkt zu entdecken.  
- **Token-Verwaltung**: Bietet Informationen über die maximale Gültigkeitsdauer von  
  Tokens und die Token-Endpunkte.  
