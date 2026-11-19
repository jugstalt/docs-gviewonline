GeoJson Service Spezifikation
=============================

Allgemeine Beschreibung der Schnittstelle
-----------------------------------------

Der GeoJson Service bietet eine leistungsfähige und flexible API zur Bereitstellung von Geodaten im GeoJSON-Format.  
Das Ziel der Schnittstelle ist es, Entwicklern eine einfache Möglichkeit zu geben, Geodaten abzufragen, zu visualisieren und zu manipulieren.  
Die API orientiert sich an etablierte Standards und ermöglicht es, räumliche Daten sowohl über GET- als auch POST-, PUT- und DELETE-Anfragen zu konsumieren und zu ändern.

Die Motivation hinter der Entwicklung dieser Schnittstelle war es, eine möglichst einfache, aber auch leistungsfähige Methode für den Zugriff auf Geodaten zu schaffen.  
Bestehende Schnittstellen wie **WMS** sind zwar sehr einfach, bieten jedoch nur begrenzte Möglichkeiten beim Abfragen von Geodaten.  
**WFS** ermöglicht das Abfragen von Geodaten, allerdings basiert der Standard auf XML, was die Umsetzung in Webumgebungen mit JavaScript aufwändig macht.  
Die **GeoServices**-Schnittstelle von ESRI ist eine REST-Schnittstelle, die ebenfalls einen einfachen Zugriff auf Geodaten erlaubt.  
Allerdings ist die Syntax in Bezug auf *Features* eher proprietär.

Mit **GeoJson Services** soll ein Mittelweg zwischen den bestehenden Schnittstellen geschaffen werden, der die Einfachheit von **WMS**, die Leistungsfähigkeit von **WFS(-T)** und moderne Technologien wie **ESRI GeoServices** vereint.  
Die Schnittstelle ist als **REST**-API mit *JSON-Requests und JSON-Responses* konzipiert.  
Die Syntax soll an **GeoJSON** erinnern.  
Insbesondere sollen *Features* im **GeoJSON**-Format bereitgestellt und übertragen werden, da dieser Standard von vielen *Web-Mapping-Frameworks* nativ unterstützt wird.

Die Schnittstelle soll folgende Funktionen anbieten:

* **Catalog:** Auflistung der verfügbaren GeoServices.

* **Capabilities:** Anzeige der Fähigkeiten eines GeoServices.

* **Mapping:** Erstellung von Kartenansichten für einen definierten Bereich mit beliebiger Layer-Sichtbarkeit innerhalb des Dienstes.

* **Query:** Abfrage von Geodaten aus den angebotenen Diensten mittels Attribut- und/oder räumlichen Filtern.

* **Edit:** Erstellung/Bearbeitung/Löschung von Geodaten.

* **Security:** GeoServices sollten authentifiziert aufgerufen werden können, um Clients spezifische Berechtigungen (Edit/Query) zu erteilen.  
  Hierzu muss den Requests ein **Bearer Token** im **Authorization Header** mitgegeben werden.

Struktur der API-Links
----------------------

Die API folgt einer konsistenten URL-Struktur, die das Abrufen von Informationen oder das Vornehmen von Änderungen vereinfacht.  
Im Folgenden sind die verschiedenen API-Endpunkte aufgeführt, zusammen mit einer kurzen Beschreibung ihrer Funktion.

**Services**

- **GET**
  
  - ``https://{server}/geojsonservice/v1/services``
  
    Gibt eine Liste aller verfügbaren Geodaten-Services auf dem Server zurück.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}``
    
    Gibt alle Services innerhalb des angegebenen Ordners zurück.

**Capabilities**

- **GET**
  - ``https://{server}/geojsonservice/v1/services/{service}/capabilities``
    
    Gibt die Eigenschaften und Informationen des angegebenen Services zurück.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/capabilities``
    
    Gibt die Eigenschaften und Informationen eines Services innerhalb eines bestimmten Ordners zurück.

**Map**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/map``
    
    Erstellt eine Karte basierend auf dem angegebenen Service.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/map``
    
    Erstellt eine Karte für einen Service innerhalb eines bestimmten Ordners.

**Legend**

- **GET/POST**
  
  - ``https://{server}/geojsonservice/v1/services/{service}/legend``
    
    Gibt die Legende für den angegebenen Service zurück.
  
  - ``https://{server}/geojsonservice/v1/services/{folder}/{service}/legend``
    
    Gibt die Legende für einen Service innerhalb eines bestimmten Ordners zurück.

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