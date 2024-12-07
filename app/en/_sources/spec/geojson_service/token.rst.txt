Token Endpoint
==============

Der **Token** Endpunkt ermöglicht die Authentifizierung und Autorisierung für die Nutzung 
der GeoJSON-API. Ein Token kann entweder über GET oder POST angefordert werden.

Request-Struktur
----------------

Die URL kann aus dem Info Endpoint Response entnommen werden, beispielsweise

- **GET**

  .. code-block::

      GET /geojsonservice/v1/token?clientId={client_id}&clientSecret={client_secret}&expireMinutes={expire_minutes}

  - **clientId**: (string) Die Client-ID zur Authentifizierung.
  - **clientSecret**: (string) Der Client-Secret zur Authentifizierung.
  - **expireMinutes** (optional): (int) Die gewünschte Gültigkeitsdauer des Tokens in Minuten (maximal gemäß Serverkonfiguration).

Response-Struktur
------------------

Der Response enthält das erzeugte Token und die zugehörigen Metadaten. Die Struktur des Responses ist wie folgt definiert:

.. code-block:: json

    {
      "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "expires_in": 600,
      "token_type": "Bearer"
    }

- **access_token**: (string) Das erzeugte Zugriffstoken.
- **expires_in**: (int) Die verbleibende Gültigkeitsdauer des Tokens in Sekunden.
- **token_type**: (string) Der Typ des Tokens, in der Regel "Bearer".

.. note::
   
   Der ``access_token`` ist allen Request im ``HTTP Header Authorize`` als **Bearer Token** übergeben werden:
   (``Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...``).

Verwendungsmöglichkeiten
------------------------

- **Authentifizierung**: Ermöglicht es Clients, Zugriff auf geschützte Endpunkte der GeoJSON-API zu erhalten.
- **Sitzungsverwaltung**: Erzeugt zeitlich begrenzte Tokens, um die Sicherheit zu erhöhen.
- **Flexible Authentifizierung**: Unterstützt sowohl GET- als auch POST-Methoden zur Token-Generierung.