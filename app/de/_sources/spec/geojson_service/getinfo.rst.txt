GetInfo Endpoint Specification
==============================

The **GetInfo** endpoint provides basic information about the service, including version information and the URL for obtaining an authentication token if required.

Request Structure
-----------------

The request should be a JSON object with the following properties:

- **type**: (string) Fixed value "GetInfo", indicating the type of request.

Example Request
---------------

.. code-block:: json

    {
      "type": "GetInfo"
    }

Explanation of the Example
--------------------------

- **type**: Specifies that this is a "GetInfo" request.

Response Structure
------------------

The response contains information about the service version and the URL for obtaining an authentication token. It is structured as follows:

- **type**: (string) Fixed value "GetInfoResponse", indicating the type of response.
- **version**: (string) The version of the service.
- **getTokenUrl**: (string) The URL where clients can obtain an authentication token.

Example Response
----------------

.. code-block:: json

    {
      "type": "GetInfoResponse",
      "version": "1.0.0",
      "getTokenUrl": "https://example.com/getToken"
    }

Explanation of the Example
--------------------------

- **type**: The response is of type "GetInfoResponse".
- **version**: Specifies the version of the service, in this example "1.0.0".
- **getTokenUrl**: Provides the URL where an authentication token can be obtained.

Use Cases
---------

- **Service Information Retrieval**: Obtain basic information about the service, such as the version and the URL for obtaining an authentication token.
- **Authentication Integration**: Use the token URL to facite secure access to the service.