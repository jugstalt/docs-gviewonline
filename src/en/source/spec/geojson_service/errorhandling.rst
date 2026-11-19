Error Handling
==============  

The **Error Handling** section describes the structure of error messages  
returned when a request fails. In the event of an error,  
an **ErrorResponse** is returned containing details about the error.  

Error Response Structure
------------------------  

The structure of the **ErrorResponse** is defined as follows:  

.. code-block:: json

    {
      "type": "ErrorResponse",
      "errorCode": 400,
      "errorMessage": "Invalid request parameters."
    }

- **type**: (string) The type of response, in this case "ErrorResponse".  
- **errorCode**: (int) The error code describing the type of error.  
- **errorMessage**: (string) A description of the error that occurred.  

Examples of Error Codes
-----------------------  

- **400**: Bad Request - The request parameters are invalid or incomplete.  
  - Example: `"errorMessage": "Invalid request parameters."`  

- **498**: Invalid Token - The provided token is invalid or expired.  
  - Example: `"errorMessage": "The provided token is invalid."`  

- **499**: Token Required - A valid token is required for the request but was not provided.  
  - Example: `"errorMessage": "Token is required for this request."`  

- **500**: Internal Server Error - An internal error occurred on the server.  
  - Example: `"errorMessage": "An unexpected error occurred on the server."`  

Use Cases
---------  

- **Error Analysis**: Use the error code and message to identify and resolve issues  
  in the request.  
- **User Feedback**: Provide users with specific error messages to help them  
  correct their input.  
