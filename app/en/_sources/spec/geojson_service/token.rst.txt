Token Endpoint
==============  

The **Token** endpoint enables authentication and authorization for using  
the GeoJSON API. A token can be requested via GET or POST.  

Request Structure
-----------------

The URL can be retrieved from the Info endpoint response, for example  

- **GET**  

  .. code-block::

      GET /geojsonservice/v1/token?clientId={client_id}&clientSecret={client_secret}&expireMinutes={expire_minutes}

  - **clientId**: (string) The client ID for authentication.  
  - **clientSecret**: (string) The client secret for authentication.  
  - **expireMinutes** (optional): (int) The desired validity period of the token in minutes (maximum as per server configuration).  

- **POST** (form, ``application/x-www-form-urlencoded``)

  .. code-block::

      POST /geojsonservice/v1/token
      clientId={client_id}&clientSecret={client_secret}&expireMinutes={expire_minutes}

Response Structure
------------------  

The response contains the generated token and its associated metadata.  
The structure of the response is defined as follows:  

.. code-block:: json

    {
      "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "expires_in": 600,
      "token_type": "Bearer"
    }

- **access_token**: (string) The generated access token.  
- **expires_in**: (int) The remaining validity period of the token in seconds.  
- **token_type**: (string) The type of token, typically "Bearer".  

.. note::  
   
   The ``access_token`` must be included in all requests in the HTTP header ``Authorization`` as a **Bearer Token**:  
   (``Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...``).  

   An expired or invalid token is rejected with ``errorCode`` 498 (HTTP status 401): the client requests a new token.
   Bearer tokens of other issuers (e.g. set by a proxy) are ignored; the request then runs without a user.
   Errors of the token endpoint itself (unknown **clientId** or **clientSecret**) are answered with HTTP status 400.

Use Cases
---------  

- **Authentication**: Allows clients to access protected endpoints of the GeoJSON API.  
- **Session Management**: Generates time-limited tokens to enhance security.  
- **Flexible Authentication**: Supports both GET and POST methods for token generation.  
