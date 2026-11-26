Bounding Box, Coordinate Reference System, and Geometry
=======================================================

The following objects are fundamental components of the REST interface and are used in  
various requests and responses to describe spatial boundaries, coordinate references,  
and geometric shapes.

**BBox (Bounding Box)**

The **BBox** object describes a rectangle that encloses the extent of a feature or a collection  
of features. It is used to define spatial boundaries.

- **minX**: (double) The minimum X-coordinate (e.g., longitude).  
- **minY**: (double) The minimum Y-coordinate (e.g., latitude).  
- **maxX**: (double) The maximum X-coordinate (e.g., longitude).  
- **maxY**: (double) The maximum Y-coordinate (e.g., latitude).  

Example:  

.. code-block:: json

    {
      "minX": 10.0,
      "minY": 50.0,
      "maxX": 20.0,
      "maxY": 60.0
    }

.. note::  

    In GET requests, the BBox can also be simplified as a URL parameter in the following form:  

    ``&bbox=10,50,20,60``

**CRS (Coordinate Reference System)**

The **CRS** object describes the coordinate reference system used to interpret the geometry.  
It contains information about how the spatial data should be interpreted,  
particularly regarding the coordinate system in use.

- **type**: (string) The type of coordinate reference system. By default, "name" is used.  
- **properties**: (dictionary) A dictionary with additional properties of the coordinate reference system.  
  Typically, an EPSG code such as "EPSG:4326" is used.  

Example:

.. code-block:: json

    {
      "type": "name",
      "properties": {
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

- **type**: (string) The geometry type, e.g., "Point", "LineString", "Polygon", "MultiPoint",  
  "MultiLineString", "MultiPolygon" or "Unknown".  
- **coordinates**: (array) The coordinates defining the geometry. The exact structure  
  depends on the geometry type and can include points, lines, or polygons.  

Example:  

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

The **GeometryType** enum describes the possible geometry types that can be used  
in a Geometry object:  

- **Point**: A point.  
- **LineString**: A line.  
- **Polygon**: A polygon.  
- **MultiPoint**: A collection of points.  
- **MultiLineString**: A collection of lines.  
- **MultiPolygon**: A collection of polygons.

**Feature**

A feature as in GeoJSON (RFC 7946), with the object id as **oid**:

.. code-block:: json

    {
      "type": "Feature",
      "oid": 12,
      "bbox": { "minX": 16.48, "minY": 47.79, "maxX": 16.59, "maxY": 47.88 },
      "geometry": { "type": "Point", "coordinates": [16.53, 47.84] },
      "properties": { "NAME": "Eisenstadt" }
    }

- **type**: always ``"Feature"``.
- **oid**: the object id.
- **bbox**: the extent of the geometry as a **BBox** object (in responses with a geometry or with
  ``returnGeometry=bbox``).
- **geometry**, **properties**: the geometry and the attributes.

.. note::

    The server writes all names in camelCase (``minX``, ``type``, ``properties``). In requests the names are
    case-insensitive.
