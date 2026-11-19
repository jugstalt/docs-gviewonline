Services Endpunkt
=================  

Der **Services**-Endpunkt liefert eine Liste aller verfügbaren Geodaten-Services auf dem Server oder innerhalb eines bestimmten Ordners.  
Dieser Endpunkt ermöglicht es, die vorhandenen Services zu durchsuchen und eine Übersicht über die verfügbare Struktur zu erhalten.  

Request-Struktur
----------------  

Der **Services**-Endpunkt unterstützt nur GET-Anfragen und kann mit den folgenden URLs aufgerufen werden:  

- **Services im Root-Ordner**  
  - ``https://{server}/geojsonservice/v1/services``  
      
    - Liefert eine Liste aller verfügbaren Services und Ordner auf dem Server.  

- **Services in einem bestimmten Ordner**  
  - ``https://{server}/geojsonservice/v1/services/{folder}``  
      
    - Gibt alle Services innerhalb des angegebenen Ordners zurück.  

Response-Struktur
------------------  

Der Response enthält Informationen über die vorhandenen Ordner und Services.  
Die Struktur des Responses ist wie folgt definiert:  

- **type**: (string) Fester Wert "GetServicesResponse", der den Typ der Antwort angibt.  
- **folders**: (array) Eine Liste von Unterordnern, die auf dem Server oder innerhalb des angegebenen Ordners verfügbar sind.  
- **services**: (array) Eine Liste der Services, die auf dem Server oder im angegebenen Ordner verfügbar sind.  

Beispiel Response
-----------------

.. code-block:: json

    {
      "type": "GetServicesResponse",
      "folders": [
        "folder1",
        "folder2"
      ],
      "services": [
        "service1",
        "service2"
      ]
    }

Erläuterung des Beispiels
-------------------------  

- **type**: Gibt den Typ der Antwort an, in diesem Fall "GetServicesResponse".  
- **folders**: Enthält eine Liste von Ordnern, die verfügbar sind. In diesem Beispiel sind "Landkarten" und "Verkehrsdaten" zwei vorhandene Ordner.  
- **services**: Enthält eine Liste der Services, die verfügbar sind. In diesem Beispiel sind "Wetterkarten" und "Bevölkerungsdichte" zwei verfügbare Services.  

Verwendungsmöglichkeiten
------------------------  

- **Service-Discovery**: Erhalten Sie eine Übersicht über alle auf dem Server verfügbaren Services, um zu entscheiden, welche Daten verwendet werden sollen.  
- **Katalognavigation**: Navigieren Sie durch die verfügbaren Ordner, um die gewünschte Datenstruktur zu finden.  
