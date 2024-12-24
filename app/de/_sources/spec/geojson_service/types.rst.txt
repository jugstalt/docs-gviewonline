Bounding Box, Koordinatenreferenzsystem und Geometrie
=====================================================

Die folgenden Objekte sind grundlegende Bestandteile der REST-Schnittstelle und werden in  
verschiedenen Requests und Responses verwendet, um räumliche Begrenzungen, Koordinatenreferenzen  
und geometrische Formen zu beschreiben.

**BBox (Bounding Box)**

Das **BBox**-Objekt beschreibt ein Rechteck, das die Ausdehnung eines Features oder einer Sammlung  
von Features umschließt. Es wird verwendet, um räumliche Begrenzungen festzulegen.

- **MinX**: (double) Die minimale X-Koordinate (z. B. Längengrad).  
- **MinY**: (double) Die minimale Y-Koordinate (z. B. Breitengrad).  
- **MaxX**: (double) Die maximale X-Koordinate (z. B. Längengrad).  
- **MaxY**: (double) Die maximale Y-Koordinate (z. B. Breitengrad).  

Beispiel:

.. code-block:: json

    {
      "MinX": 10.0,
      "MinY": 50.0,
      "MaxX": 20.0,
      "MaxY": 60.0
    }

.. note::  

    In GET-Requests kann die BBox auch vereinfacht als URL-Parameter in folgender Form übergeben werden:  

    ``&bbox=10,50,20,60``

**CRS (Coordinate Reference System)**

Das **CRS**-Objekt beschreibt das Koordinatenreferenzsystem, das zur Interpretation der  
Geometrie verwendet wird.  
Es enthält Informationen darüber, wie die räumlichen Daten interpretiert werden sollen,  
insbesondere hinsichtlich des verwendeten Koordinatensystems.

- **Type**: (string) Der Typ des Koordinatenreferenzsystems. Standardmäßig wird "name" verwendet.  
- **Properties**: (dictionary) Ein Wörterbuch mit zusätzlichen Eigenschaften des Koordinatenreferenzsystems.  
  In der Regel wird ein EPSG-Code verwendet, wie "EPSG:4326".  

Beispiel:

.. code-block:: json

    {
      "Type": "name",
      "Properties": {
        "name": "EPSG:4326"
      }
    }

.. note::  

    In GET-Requests kann CRS auch vereinfacht als URL-Parameter in folgender Form übergeben werden:  

    ``&crs=epsg:4326``  

.. note::  

    CRS ist in allen Requests immer ein optionaler Parameter. Wird kein Wert übergeben, wird  
    WGS84 (EPSG:4326) als Standardwert für GeoJson angenommen.  

**Geometry**  

Das **Geometry**-Objekt beschreibt die geometrische Form eines Features und basiert  
auf dem GeoJSON-Standard. Es enthält folgende Attribute:  

- **Type**: (string) Der Geometrietyp, z. B. "Point", "LineString", "Polygon", "MultiPoint",  
  "MultiLineString", "MultiPolygon" oder "Unknown".  
- **Coordinates**: (array) Die Koordinaten, die die Geometrie definieren. Der genaue Aufbau  
  hängt vom Geometrietyp ab und kann Punkte, Linien oder Polygone enthalten.  

Beispiel: 

.. code-block:: json

    {
      "Type": "Polygon",
      "Coordinates": [
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
