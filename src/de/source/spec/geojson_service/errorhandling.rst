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

Der HTTP-Status der Antwort ist der **errorCode**, nur bei 498 und 499 ist er 401.

Beispiele für Fehlercodes
-------------------------  

- **400**: Bad Request - Die Anfrageparameter sind ungültig oder unvollständig.  
  - Beispiel: `"errorMessage": "Invalid request parameters."`  

- **403**: Forbidden - Dem Benutzer fehlt das Recht für den Request (z. B. *Query* oder *Edit*).

- **404**: Not Found - Der Layer (Query) oder das Objekt existiert nicht oder liegt außerhalb der Definitionsabfrage des Layers (Ändern, Löschen).  

- **412**: Precondition Failed - Ein Feature hat nicht mehr die angegebene Version (applyEdits).  

- **498**: Invalid Token - Das bereitgestellte Token ist ungültig oder abgelaufen (HTTP-Status 401).  
  - Beispiel: `"errorMessage": "The provided token is invalid."`  

- **499**: Token Required - Für die Anfrage ist ein gültiges Token erforderlich, wurde jedoch nicht bereitgestellt (HTTP-Status 401).  
  - Beispiel: `"errorMessage": "Token is required for this request."`  

- **500**: Internal Server Error - Ein interner Fehler ist auf dem Server aufgetreten.  
  - Beispiel: `"errorMessage": "internal server error (id: 3f2a9c1b)"`; die Details stehen nur im Log des Servers, unter derselben ID.  

Verwendungsmöglichkeiten
------------------------  

- **Fehleranalyse**: Verwenden Sie den Fehlercode und die Fehlermeldung, um Probleme  
  in der Anfrage zu identifizieren und zu beheben.  
- **Nutzerfeedback**: Geben Sie dem Nutzer spezifische Fehlermeldungen, um ihn bei  
  der Korrektur seiner Eingaben zu unterstützen.  
