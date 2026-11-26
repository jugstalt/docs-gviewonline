.. _server-wfs:

WFS interface
=============

Every map service of *gView Server* is also an **OGC Web Feature Service** (WFS).
The versions **2.0.0**, **1.1.0** and **1.0.0** are supported. The WFS is read only:
transactions (WFS-T) and locking are not offered. To edit features use
**OGC API Features** or **GeoServices REST**.

The WFS uses the same feature query as **OGC API Features**, so both interfaces return
the same layers, fields and features.

Address
-------

.. code-block:: text

   https://{server}/ogc/{folder@service}?SERVICE=WFS&REQUEST=GetCapabilities

Without ``VERSION`` (or ``ACCEPTVERSIONS``) the server answers with the newest version 2.0.0.
The link is on the page of a service (**Services**), tab **OGC Standards**.

Supported operations:

- ``GetCapabilities``, ``DescribeFeatureType``, ``GetFeature`` (all versions, GET and POST)
- ``ListStoredQueries``, ``DescribeStoredQueries`` and the stored query ``GetFeatureById`` (2.0.0)

Feature types
-------------

Every visible layer with features becomes a feature type named ``gv:c{LayerId}``
(namespace ``http://www.gViewGIS.com/server``), e.g. ``gv:c0``. This is the same id as the
collection in **OGC API Features**. Layers inside group layers with checkbox style are not offered.

Queries accept ``c0``, ``gv:c0`` and also ``wfs:c0`` (the names in the capabilities of older
gView versions).

The object id of a feature is the ``gml:id`` (GeoJSON: ``id``) in the form ``c{LayerId}.{ObjectId}``,
e.g. ``c0.12``. It is not written as an attribute of its own. The geometry property is named like the
geometry field of the data (e.g. ``SHAPE``).

Output formats
--------------

======================================== ===================== =====================
``OUTPUTFORMAT``                         Versions              Remark
======================================== ===================== =====================
``application/gml+xml; version=3.2``     2.0.0                 default for 2.0.0
``text/xml; subtype=gml/3.1.1``          2.0.0, 1.1.0          default for 1.1.0
``text/xml; subtype=gml/2.1.2``          all                   default for 1.0.0 (``GML2``)
``application/json``                     all                   GeoJSON
======================================== ===================== =====================

For GeoJSON ``application/geo+json``, ``json`` and ``geojson`` are accepted too. The response is a
*FeatureCollection* with ``numberMatched``, ``numberReturned``, ``timeStamp`` and ``links``
(next/previous page).

Coordinate systems and axis order
---------------------------------

Without ``SRSNAME`` the WFS returns the geometries in the default coordinate system of the service
(``DefaultSRS``/``DefaultCRS`` in the capabilities, the coordinate system of the map).
More coordinate systems (``OtherSRS``/``OtherCRS``) come from the **WFS export metadata** of the service.

The order of the coordinates depends on how the coordinate system is written
(as in GeoServer, which is what QGIS and ArcGIS expect):

=================================================== ========== ==============================================
Notation                                            Version    Order
=================================================== ========== ==============================================
any                                                 1.0.0      always x/y (east/north, longitude/latitude)
``EPSG:4326``, ``EPSG:31256``                       1.1, 2.0   x/y
``urn:ogc:def:crs:EPSG::4326``                      1.1, 2.0   as defined by EPSG (4326: latitude/longitude)
``http://www.opengis.net/def/crs/EPSG/0/31256``     1.1, 2.0   as defined by EPSG (31256: north/east)
GeoJSON                                             all        always x/y
=================================================== ========== ==============================================

The rule applies to the output, to ``BBOX`` and to geometries in filters alike. The ``srsName`` of the
response uses the notation of the request. The capabilities of 1.1.0 and 2.0.0 list the coordinate
systems in URN notation.

Parameters of GetFeature
------------------------

Parameter names (KVP) are not case sensitive, values are URL decoded.

- ``TYPENAMES`` (2.0.0) or ``TYPENAME``: one or more feature types, separated by commas.
- ``COUNT`` (2.0.0) or ``MAXFEATURES``: at most this many features. The **MaxRecordCount** of the service
  always limits.
- ``STARTINDEX``: paging (first page = 0).
- ``RESULTTYPE=hits``: only the number (1.1.0 ``numberOfFeatures``, 2.0.0 ``numberMatched``).
- ``SRSNAME``: coordinate system of the output.
- ``BBOX=minx,miny,maxx,maxy[,crs]``: extent in the axis order of the coordinate system (see above).
- ``FILTER``: Filter Encoding 1.0/1.1 or FES 2.0 (see below).
- ``RESOURCEID`` (2.0.0) or ``FEATUREID``: features by id, e.g. ``RESOURCEID=c0.12,c0.13``
  (``TYPENAMES`` is optional then).
- ``SORTBY``: e.g. ``SORTBY=NAME DESC,ORT`` (1.1.0 also ``NAME D``).
- ``PROPERTYNAME``: only these properties. The geometry is only returned if it is in the list.
- ``OUTPUTFORMAT``: see above.

``BBOX``, ``FILTER`` and ``RESOURCEID`` can't be combined. A filter can contain a ``BBOX`` operator.
All parameters exist as XML request (POST) too.

Example:

.. code-block:: text

   ...?SERVICE=WFS&VERSION=2.0.0&REQUEST=GetFeature&TYPENAMES=gv:c2&COUNT=100
      &SRSNAME=urn:ogc:def:crs:EPSG::4326&BBOX=47.03,15.40,47.10,15.48,urn:ogc:def:crs:EPSG::4326

Filters
-------

Filters are written as Filter Encoding 1.0/1.1 (namespace ``ogc``) or FES 2.0 (namespace ``fes``),
properties as ``PropertyName`` or ``ValueReference`` (with or without the ``gv:`` prefix).

- Comparisons: ``PropertyIsEqualTo``, ``…NotEqualTo``, ``…LessThan``, ``…GreaterThan``,
  ``…LessThanOrEqualTo``, ``…GreaterThanOrEqualTo`` (also with ``matchCase="false"``),
  ``PropertyIsLike`` (with ``wildCard``, ``singleChar``, ``escapeChar``), ``PropertyIsBetween``,
  ``PropertyIsNull``, ``PropertyIsNil``
- Logic: ``And``, ``Or``, ``Not``
- Spatial: ``BBOX``, ``Intersects``, ``Within``, ``Contains`` with GML 2, 3.1 or 3.2 geometries
- Ids: ``ResourceId``, ``GmlObjectId``, ``FeatureId``

Limitations:

- A filter can have **one** spatial operator, at the top level or in an ``And`` at the top level
  (e.g. an attribute filter **and** ``BBOX``). Spatial operators in ``Or``/``Not``, ``DWithin``,
  ``Touches``, temporal operators and functions are rejected with ``OptionNotSupported``.
- Text and number fields can be filtered. Date and yes/no fields only for data sources with a known
  SQL dialect (e.g. PostGIS, SQL Server, SQLite/GeoPackage).
- Values are converted to the type of the field. A text for a number field is an error.

Counting and paging
-------------------

WFS 2.0.0 returns the number of all matches in ``numberMatched`` and, for GET requests, the links
``next`` and ``previous`` to the next and previous page. This costs an additional count query.
For very large databases it can be switched off (``numberMatched="unknown"``):

- for the whole server in ``mapserver.json``:

  .. code-block:: json

     "mapserver-defaults": {
       "wfsNumberMatched": false
     }

- for one service in the **WFS export metadata** (``NumberMatched: true|false``; empty = server setting).

``RESULTTYPE=hits`` always counts.

GetFeatureById
--------------

.. code-block:: text

   ...?SERVICE=WFS&VERSION=2.0.0&REQUEST=GetFeature
      &STOREDQUERY_ID=urn:ogc:def:query:OGC-WFS::GetFeatureById&ID=c2.5

The response is the single feature (without *FeatureCollection*), with ``OUTPUTFORMAT=application/json``
a GeoJSON *Feature*. If the id doesn't exist, the server answers with ``404`` and ``NotFound``.

Errors
------

Errors are answered as OWS *ExceptionReport* (1.0.0: *ServiceExceptionReport*) with ``exceptionCode``
and ``locator``, e.g. ``InvalidParameterValue`` with ``locator="typeNames"``.
For 2.0.0 the response has the matching HTTP status (400, 404, 501, 500). Internal errors only contain
a general message with an error id; the details are in the log of the server.

Clients
-------

- **QGIS:** create a connection with the GetCapabilities URL, version *Maximum* or *2.0*.
  QGIS pages with ``STARTINDEX``/``COUNT`` and uses the axis order of the URN.
- **ArcGIS Pro:** WFS connection with the GetCapabilities URL (2.0.0 or 1.1.0).
- In the :ref:`server-testmap` the WFS of a service can be tried directly in the browser,
  with all requests in the request log.
