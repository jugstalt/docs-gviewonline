Error Handling
==============  

Der **Error Handling**-Abschnitt beschreibt die Struktur der Fehlermeldungen,  
die zurückgegeben werden, wenn eine Anfrage fehlschlägt. Bei einem Fehler wird  
ein **ErrorResponse** zurückgegeben, der Details über den Fehler enthält.  

Error Response Struktur
-----------------------  

Die Struktur des **ErrorResponse** ist wie folgt definiert:  

.. code-block:: json

    {
      "type": "ErrorResponse",
      "errorCode": 400,
      "errorMessage": "Invalid request parameters."
    }

- **type**: (string) Der Typ der Antwort, in diesem Fall "ErrorResponse".  
- **errorCode**: (int) Der Fehlercode, der den Typ des Fehlers beschreibt.  
- **errorMessage**: (string) Eine Beschreibung des aufgetretenen Fehlers.  

Beispiele für Fehlercodes
-------------------------  

- **400**: Bad Request - Die Anfrageparameter sind ungültig oder unvollständig.  
  - Beispiel: `"errorMessage": "Invalid request parameters."`  

- **498**: Invalid Token - Das bereitgestellte Token ist ungültig oder abgelaufen.  
  - Beispiel: `"errorMessage": "The provided token is invalid."`  

- **499**: Token Required - Für die Anfrage ist ein gültiges Token erforderlich, wurde jedoch nicht bereitgestellt.  
  - Beispiel: `"errorMessage": "Token is required for this request."`  

- **500**: Internal Server Error - Ein interner Fehler ist auf dem Server aufgetreten.  
  - Beispiel: `"errorMessage": "An unexpected error occurred on the server."`  

Verwendungsmöglichkeiten
------------------------  

- **Fehleranalyse**: Verwenden Sie den Fehlercode und die Fehlermeldung, um Probleme  
  in der Anfrage zu identifizieren und zu beheben.  
- **Nutzerfeedback**: Geben Sie dem Nutzer spezifische Fehlermeldungen, um ihn bei  
  der Korrektur seiner Eingaben zu unterstützen.  
