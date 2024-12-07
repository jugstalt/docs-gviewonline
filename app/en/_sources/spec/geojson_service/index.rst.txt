GeoJson Service Specification
=============================

Allgemeine Beschreibung der Schnittstelle
-----------------------------------------

Der GeoJson Service bietet eine leistungsfähige und flexible API zur Bereitstellung 
von Geodaten im GeoJSON-Format. 
Das Ziel der Schnittstelle ist es, Entwicklern eine einfache Möglichkeit zu geben, 
Geodaten abzufragen, zu visualisieren und zu manipulieren. 
Die API orientiert sich an etablierten Standards und ermöglicht es, räumliche Daten 
sowohl über GET- als auch POST-, PUT- und DELETE-Anfragen zu konsumieren und zu ändern.

Die Motivation hinter der Entwicklung dieser Schnittstelle liegt in der zunehmenden 
Nachfrage nach offenen und interoperablen Geodatenformaten. 
GeoJSON ist ein weit verbreitetes, leichtgewichtiges Format, das sowohl Menschen als auch 
Maschinen eine einfache Interpretation von Geodaten ermöglicht. 
Durch die Nutzung dieser API können Entwickler Kartenanwendungen, 
Analysen und andere Geodaten-gestützte Anwendungen auf einfache und effiziente Weise realisieren.

Struktur der API-Links
----------------------

Die API folgt einer konsistenten URL-Struktur, die es einfach macht, 
die gewünschten Informationen abzurufen oder Änderungen vorzunehmen. 
Im Folgenden sind die verschiedenen Endpunkte der API aufgelistet, 
zusammen mit einer kurzen Beschreibung ihrer Funktion.

**Services**

- **GET**
  
  - ``https://{server}/geojsonservice/v1/services``
  
    Liefert eine Liste aller verfügbaren Geodaten-Services auf dem Server.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}``
    
    Gibt alle Services innerhalb des angegebenen Ordners zurück.

**Capabilities**

- **GET**
  - ``https://{server}/geojsonservice/v1/services/{service}/capabilities``
    
    Liefert die Eigenschaften und Informationen des spezifizierten Services.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/capabilities``
    
    Gibt die Eigenschaften und Informationen des spezifizierten Services innerhalb eines bestimmten Ordners zurück.

**Map**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/map``
    
    Generiert eine Karte basierend auf dem angegebenen Service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/map``
    
    Generiert eine Karte für einen Service innerhalb eines bestimmten Ordners.

**Legend**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/legend``
    
   Liefert die Legende für den spezifizierten Service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/legend``
    
   Liefert die Legende für einen Service innerhalb eines bestimmten Ordners.

**Query**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/query/{layerId}``
    
   Führt eine Abfrage auf dem angegebenen Layer innerhalb des Services aus.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/query/{layerId}``
    
   Führt eine Abfrage auf dem Layer eines Services innerhalb eines bestimmten Ordners aus.

**Features**

- **POST/PUT/DELETE**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/features/{layerId}``
    
   Fügt Features hinzu, aktualisiert oder löscht Features in einem bestimmten Layer des Services.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/features/{layerId}``
    
   Fügt Features hinzu, aktualisiert oder löscht Features in einem bestimmten Layer eines Services innerhalb eines bestimmten Ordners.

**Token**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/Token``
    
   Liefert ein Token zur Authentifizierung oder führt die Authentifizierung durch.


.. toctree::
   :maxdepth: 1
   :caption: Table of contents:

   types
   info
   services
   capabilities
   map
   legend
   query
   features
   token
   errorhandling