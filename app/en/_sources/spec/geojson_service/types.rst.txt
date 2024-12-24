Bounding Box, Coordinate Reference System, and Geometry
=======================================================

The following objects are fundamental components of the REST interface and are used in  
various requests and responses to describe spatial boundaries, coordinate references,  
and geometric shapes.

**BBox (Bounding Box)**

The **BBox** object describes a rectangle that encloses the extent of a feature or a collection  
of features. It is used to define spatial boundaries.

- **MinX**: (double) The minimum X-coordinate (e.g., longitude).  
- **MinY**: (double) The minimum Y-coordinate (e.g., latitude).  
- **MaxX**: (double) The maximum X-coordinate (e.g., longitude).  
- **MaxY**: (double) The maximum Y-coordinate (e.g., latitude).  

Example:  

.. code-block:: json

    {
      "MinX": 10.0,
      "MinY": 50.0,
      "MaxX": 20.0,
      "MaxY": 60.0
    }

.. note::  

    In GET requests, the BBox can also be simplified as a URL parameter in the following form:  

    ``&bbox=10,50,20,60``

**CRS (Coordinate Reference System)**

The **CRS** object describes the coordinate reference system used to interpret the geometry.  
It contains information about how the spatial data should be interpreted,  
particularly regarding the coordinate system in use.

- **Type**: (string) The type of coordinate reference system. By default, "name" is used.  
- **Properties**: (dictionary) A dictionary with additional properties of the coordinate reference system.  
  Typically, an EPSG code such as "EPSG:4326" is used.  

Example:

.. code-block:: json

    {
      "Type": "name",
      "Properties": {
        "name": "EPSG:4326"
      }
    }

.. note::  

    In GET requests, the CRS can also be simplified as a URL parameter in the following form:  

    ``&crs=epsg:4326``  

.. note::  

    CRS is always an optional parameter in all requests. If no value is provided,  
    WGS84 (EPSG:4326) is assumed as the default value for GeoJson.  

**Geometry**  

The **Geometry** object describes the geometric shape of a feature and is based  
on the GeoJSON standard. It contains the following attributes:  

- **Type**: (string) The geometry type, e.g., "Point", "LineString", "Polygon", "MultiPoint",  
  "MultiLineString", "MultiPolygon" or "Unknown".  
- **Coordinates**: (array) The coordinates defining the geometry. The exact structure  
  depends on the geometry type and can include points, lines, or polygons.  

Example:  

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

The **GeometryType** enum describes the possible geometry types that can be used  
in a Geometry object:  

- **Point**: A point.  
- **LineString**: A line.  
- **Polygon**: A polygon.  
- **MultiPoint**: A collection of points.  
- **MultiLineString**: A collection of lines.  
- **MultiPolygon**: A collection of polygons.  
