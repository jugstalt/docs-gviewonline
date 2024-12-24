Info Endpoint
=============  

The **Info** endpoint provides basic information about the GeoJSON API,  
including supported endpoints, version, and token settings.  

Request Structure
-----------------

The **Info** endpoint supports only GET requests using the following URL:  

.. code-block::

    GET /geojsonservice/v1/info

Response Structure
------------------  

The response contains general information about the API and is structured as follows: 

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

- **type**: (string) The type of response, in this case "GetInfoResponse".  
- **version**: (string) The version of the API.  
- **tokenMaxExpireMinutes**: (int) The maximum validity period of a token in minutes.  
- **endPoints**: (object) A list of supported endpoints with details on methods, URLs, and optional parameters.  
    
  - **token**: (array) Endpoints for token generation.  
      
    - **method**: (string) The HTTP method, e.g., "GET" or "POST".  
    - **url**: (string) The URL for the token endpoint.  
    - **contentType** (optional): (string) The MIME type of the content, e.g., "application/x-www-form-urlencoded".  
    - **body** (optional): (string) The request body when POST is used.  
    
  - **services**: (array) Endpoints for accessing services.  
      
    - **method**: (string) The HTTP method, e.g., "GET".  
    - **url**: (string) The URL for accessing services.  

Use Cases
---------  

- **API Documentation**: Allows users to discover supported endpoints and their  
  details directly.  
- **Token Management**: Provides information about the maximum validity period of  
  tokens and the token endpoints.  
