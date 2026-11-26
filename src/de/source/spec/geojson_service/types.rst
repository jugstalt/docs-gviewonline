Bounding Box, Koordinatenreferenzsystem und Geometrie
=====================================================

Die folgenden Objekte sind grundlegende Bestandteile der REST-Schnittstelle und werden in  
verschiedenen Requests und Responses verwendet, um räumliche Begrenzungen, Koordinatenreferenzen  
und geometrische Formen zu beschreiben.

**BBox (Bounding Box)**

Das **BBox**-Objekt beschreibt ein Rechteck, das die Ausdehnung eines Features oder einer Sammlung  
von Features umschließt. Es wird verwendet, um räumliche Begrenzungen festzulegen.

- **minX**: (double) Die minimale X-Koordinate (z. B. Längengrad).  
- **minY**: (double) Die minimale Y-Koordinate (z. B. Breitengrad).  
- **maxX**: (double) Die maximale X-Koordinate (z. B. Längengrad).  
- **maxY**: (double) Die maximale Y-Koordinate (z. B. Breitengrad).  

Beispiel:

.. code-block:: json

    {
      "minX": 10.0,
      "minY": 50.0,
      "maxX": 20.0,
      "maxY": 60.0
    }

.. note::  

    In GET-Requests kann die BBox auch vereinfacht als URL-Parameter in folgender Form übergeben werden:  

    ``&bbox=10,50,20,60``

**CRS (Coordinate Reference System)**

Das **CRS**-Objekt beschreibt das Koordinatenreferenzsystem, das zur Interpretation der  
Geometrie verwendet wird.  
Es enthält Informationen darüber, wie die räumlichen Daten interpretiert werden sollen,  
insbesondere hinsichtlich des verwendeten Koordinatensystems.

- **type**: (string) Der Typ des Koordinatenreferenzsystems. Standardmäßig wird "name" verwendet.  
- **properties**: (dictionary) Ein Wörterbuch mit zusätzlichen Eigenschaften des Koordinatenreferenzsystems.  
  In der Regel wird ein EPSG-Code verwendet, wie "EPSG:4326".  

Beispiel:

.. code-block:: json

    {
      "type": "name",
      "properties": {
        "name": "EPSG:4326"
      }
    }

.. note::  

    In GET-Requests kann CRS auch vereinfacht als URL-Parameter in folgender Form übergeben werden:  

    ``&crs=epsg:4326`` oder ``&crs=4326``; im Body ``"name": "EPSG:4326"`` oder ``"name": "4326"``  

.. note::  

    CRS ist in allen Requests immer ein optionaler Parameter. Wird kein Wert übergeben, wird  
    WGS84 (EPSG:4326) als Standardwert für GeoJson angenommen.

    In einem geographischen Koordinatensystem (z. B. EPSG:4326) müssen die Breiten der Geometrien eines Requests
    (räumlicher Filter, Identify, Editieren) zwischen -90 und 90 liegen, sonst wird der Request mit ``400`` abgelehnt:
    projizierte Koordinaten brauchen ihr eigenes **crs**. Längen über ±180 sind erlaubt (Karten über die Datumsgrenze).  

**Geometry**  

Das **Geometry**-Objekt beschreibt die geometrische Form eines Features und basiert  
auf dem GeoJSON-Standard. Es enthält folgende Attribute:  

- **type**: (string) Der Geometrietyp, z. B. "Point", "LineString", "Polygon", "MultiPoint",  
  "MultiLineString", "MultiPolygon" oder "Unknown".  
- **coordinates**: (array) Die Koordinaten, die die Geometrie definieren. Der genaue Aufbau  
  hängt vom Geometrietyp ab und kann Punkte, Linien oder Polygone enthalten.  

Beispiel: 

.. code-block:: json

    {
      "type": "Polygon",
      "coordinates": [
        [
          [10.0, 50.0],
          [20.0, 50.0],
          [20.0, 60.0],
          [10.0, 60.0],
          [10.0, 50.0]
        ]
      ]
    }

**GeometryType**  

Der **GeometryType**-Enum beschreibt die möglichen Geometrietypen, die in einem Geometry-Objekt  
verwendet werden können:  

- **Point**: Ein Punkt.  
- **LineString**: Eine Linie.  
- **Polygon**: Ein Polygon.  
- **MultiPoint**: Eine Sammlung von Punkten.  
- **MultiLineString**: Eine Sammlung von Linien.  
- **MultiPolygon**: Eine Sammlung von Polygonen.

**Feature**

Ein Feature wie in GeoJSON (RFC 7946), mit der Objekt-ID als **oid**:

.. code-block:: json

    {
      "type": "Feature",
      "oid": 12,
      "bbox": { "minX": 16.48, "minY": 47.79, "maxX": 16.59, "maxY": 47.88 },
      "geometry": { "type": "Point", "coordinates": [16.53, 47.84] },
      "properties": { "NAME": "Eisenstadt" }
    }

- **type**: immer ``"Feature"``.
- **oid**: die Objekt-ID.
- **bbox**: die Ausdehnung der Geometrie als **BBox**-Objekt (in Responses mit Geometrie oder mit
  ``returnGeometry=bbox``).
- **geometry**, **properties**: die Geometrie und die Attribute.

.. note::

    Der Server schreibt alle Namen in camelCase (``minX``, ``type``, ``properties``). In Requests spielt die
    Groß- und Kleinschreibung der Namen keine Rolle.
