.. _server-wfs:

WFS Schnittstelle
=================

Jeder Kartendienst des *gView Server* ist auch ein **OGC Web Feature Service** (WFS).
Unterstützt werden die Versionen **2.0.0**, **1.1.0** und **1.0.0**. Der WFS ist lesend:
Transaktionen (WFS-T) und Locking werden nicht angeboten. Zum Bearbeiten von Features dienen
**OGC API Features** und **GeoServices REST**.

Der WFS verwendet dieselbe Feature-Abfrage wie **OGC API Features**. Beide Schnittstellen liefern
deshalb dieselben Layer, Felder und Features.

Adresse
-------

.. code-block:: text

   https://{server}/ogc/{folder@service}?SERVICE=WFS&REQUEST=GetCapabilities

Ohne ``VERSION`` (oder ``ACCEPTVERSIONS``) antwortet der Server mit der neuesten Version 2.0.0.
Auf der Seite eines Dienstes (**Services**) steht der Link im Tab **OGC Standards**.

Unterstützte Operationen:

- ``GetCapabilities``, ``DescribeFeatureType``, ``GetFeature`` (alle Versionen, GET und POST)
- ``ListStoredQueries``, ``DescribeStoredQueries`` und die Stored Query ``GetFeatureById`` (2.0.0)

Feature Types
-------------

Jeder sichtbare Layer mit Features wird ein Feature Type mit dem Namen ``gv:c{LayerId}``
(Namespace ``http://www.gViewGIS.com/server``), z. B. ``gv:c0``. Das ist dieselbe Id wie die
Collection in **OGC API Features**. Layer in Gruppenlayern mit Checkbox-Stil werden nicht angeboten.

Beim Abfragen werden ``c0``, ``gv:c0`` und auch ``wfs:c0`` (so standen die Namen in den
Capabilities älterer gView-Versionen) akzeptiert.

Die Objekt-Id eines Features ist die ``gml:id`` (GeoJSON: ``id``) in der Form ``c{LayerId}.{ObjektId}``,
z. B. ``c0.12``. Sie wird nicht als eigenes Attribut ausgegeben. Die Geometrie-Eigenschaft heißt wie das
Geometriefeld der Daten (z. B. ``SHAPE``).

Ausgabeformate
--------------

======================================== ===================== =====================
``OUTPUTFORMAT``                         Versionen             Bemerkung
======================================== ===================== =====================
``application/gml+xml; version=3.2``     2.0.0                 Standard für 2.0.0
``text/xml; subtype=gml/3.1.1``          2.0.0, 1.1.0          Standard für 1.1.0
``text/xml; subtype=gml/2.1.2``          alle                  Standard für 1.0.0 (``GML2``)
``application/json``                     alle                  GeoJSON
======================================== ===================== =====================

Für GeoJSON werden auch ``application/geo+json``, ``json`` und ``geojson`` akzeptiert. Die Antwort ist
eine *FeatureCollection* mit ``numberMatched``, ``numberReturned``, ``timeStamp`` und ``links``
(nächste/vorherige Seite).

Koordinatensysteme und Achsenreihenfolge
----------------------------------------

Ohne ``SRSNAME`` liefert der WFS die Geometrien im Standard-Koordinatensystem des Dienstes
(``DefaultSRS``/``DefaultCRS`` in den Capabilities, das Koordinatensystem der Karte).
Weitere Koordinatensysteme (``OtherSRS``/``OtherCRS``) kommen aus den **WFS Export Metadaten** des Dienstes.

Die Reihenfolge der Koordinaten hängt davon ab, wie das Koordinatensystem geschrieben wird
(wie bei GeoServer, so erwarten es QGIS und ArcGIS):

=================================================== ========== ==============================================
Schreibweise                                        Version    Reihenfolge
=================================================== ========== ==============================================
beliebig                                            1.0.0      immer x/y (Ost/Nord, Länge/Breite)
``EPSG:4326``, ``EPSG:31256``                       1.1, 2.0   x/y
``urn:ogc:def:crs:EPSG::4326``                      1.1, 2.0   wie die EPSG-Definition (4326: Breite/Länge)
``http://www.opengis.net/def/crs/EPSG/0/31256``     1.1, 2.0   wie die EPSG-Definition (31256: Nord/Ost)
GeoJSON                                             alle       immer x/y
=================================================== ========== ==============================================

Die Regel gilt gleich für die Ausgabe, für ``BBOX`` und für Geometrien in Filtern. Das ``srsName`` der Antwort
wird in derselben Schreibweise wie im Request zurückgegeben. Die Capabilities von 1.1.0 und 2.0.0 nennen
die Koordinatensysteme in der URN-Schreibweise.

Parameter von GetFeature
------------------------

Die Parameter (KVP) sind nicht case-sensitiv und werden URL-dekodiert.

- ``TYPENAMES`` (2.0.0) bzw. ``TYPENAME``: ein oder mehrere Feature Types, durch Komma getrennt.
- ``COUNT`` (2.0.0) bzw. ``MAXFEATURES``: höchstens so viele Features. Die **MaxRecordCount** des Dienstes
  begrenzt immer.
- ``STARTINDEX``: Blättern (erste Seite = 0).
- ``RESULTTYPE=hits``: nur die Anzahl (1.1.0 ``numberOfFeatures``, 2.0.0 ``numberMatched``).
- ``SRSNAME``: Koordinatensystem der Ausgabe.
- ``BBOX=minx,miny,maxx,maxy[,crs]``: Ausschnitt in der Achsenreihenfolge des Koordinatensystems (siehe oben).
- ``FILTER``: Filter Encoding 1.0/1.1 oder FES 2.0 (siehe unten).
- ``RESOURCEID`` (2.0.0) bzw. ``FEATUREID``: Features über ihre Id, z. B. ``RESOURCEID=c0.12,c0.13``
  (``TYPENAMES`` ist dann optional).
- ``SORTBY``: z. B. ``SORTBY=NAME DESC,ORT`` (1.1.0 auch ``NAME D``).
- ``PROPERTYNAME``: nur diese Eigenschaften. Die Geometrie wird nur geliefert, wenn sie in der Liste steht.
- ``OUTPUTFORMAT``: siehe oben.

``BBOX``, ``FILTER`` und ``RESOURCEID`` können nicht kombiniert werden. Ein Filter kann einen ``BBOX``-Operator
enthalten. Alle Parameter gibt es auch als XML-Request (POST).

Beispiel:

.. code-block:: text

   ...?SERVICE=WFS&VERSION=2.0.0&REQUEST=GetFeature&TYPENAMES=gv:c2&COUNT=100
      &SRSNAME=urn:ogc:def:crs:EPSG::4326&BBOX=47.03,15.40,47.10,15.48,urn:ogc:def:crs:EPSG::4326

Filter
------

Filter werden als Filter Encoding 1.0/1.1 (Namespace ``ogc``) oder FES 2.0 (Namespace ``fes``) angegeben,
Eigenschaften als ``PropertyName`` oder ``ValueReference`` (mit oder ohne ``gv:``-Prefix).

- Vergleiche: ``PropertyIsEqualTo``, ``…NotEqualTo``, ``…LessThan``, ``…GreaterThan``,
  ``…LessThanOrEqualTo``, ``…GreaterThanOrEqualTo`` (auch mit ``matchCase="false"``),
  ``PropertyIsLike`` (mit ``wildCard``, ``singleChar``, ``escapeChar``), ``PropertyIsBetween``,
  ``PropertyIsNull``, ``PropertyIsNil``
- Logik: ``And``, ``Or``, ``Not``
- Räumlich: ``BBOX``, ``Intersects``, ``Within``, ``Contains`` mit GML 2, 3.1 oder 3.2 Geometrien
- Ids: ``ResourceId``, ``GmlObjectId``, ``FeatureId``

Einschränkungen:

- Pro Filter ist **ein** räumlicher Operator erlaubt, und zwar auf oberster Ebene oder in einem ``And``
  auf oberster Ebene (z. B. Attributfilter **und** ``BBOX``). Räumliche Operatoren in ``Or``/``Not``,
  ``DWithin``, ``Touches``, zeitliche Operatoren und Funktionen werden mit ``OptionNotSupported`` abgelehnt.
- Gefiltert werden können Text- und Zahlenfelder. Datums- und Ja/Nein-Felder nur bei Datenquellen,
  deren SQL-Dialekt bekannt ist (z. B. PostGIS, SQL Server, SQLite/GeoPackage).
- Werte werden in den Typ des Feldes umgewandelt. Ein Text für ein Zahlenfeld ist ein Fehler.

Anzahl und Blättern
-------------------

WFS 2.0.0 liefert in ``numberMatched`` die Anzahl aller Treffer und bei GET-Requests die Links
``next`` und ``previous`` für die nächste und vorherige Seite. Dafür wird eine zusätzliche Count-Abfrage
gemacht. Bei sehr großen Datenbanken kann sie abgeschaltet werden (``numberMatched="unknown"``):

- für den ganzen Server in der ``mapserver.json``:

  .. code-block:: json

     "mapserver-defaults": {
       "wfsNumberMatched": false
     }

- für einen Dienst in den **WFS Export Metadaten** (``NumberMatched: true|false``; leer = Server-Einstellung).

``RESULTTYPE=hits`` zählt immer.

GetFeatureById
--------------

.. code-block:: text

   ...?SERVICE=WFS&VERSION=2.0.0&REQUEST=GetFeature
      &STOREDQUERY_ID=urn:ogc:def:query:OGC-WFS::GetFeatureById&ID=c2.5

Die Antwort ist das einzelne Feature (ohne *FeatureCollection*), mit ``OUTPUTFORMAT=application/json``
ein GeoJSON-*Feature*. Gibt es die Id nicht, antwortet der Server mit ``404`` und ``NotFound``.

Fehler
------

Fehler werden als OWS *ExceptionReport* (1.0.0: *ServiceExceptionReport*) mit ``exceptionCode``
und ``locator`` beantwortet, z. B. ``InvalidParameterValue`` mit ``locator="typeNames"``.
Bei 2.0.0 hat die Antwort den passenden HTTP-Status (400, 404, 501, 500). Interne Fehler enthalten nur
eine allgemeine Meldung mit einer Fehler-Id, die Details stehen im Log des Servers.

Clients
-------

- **QGIS:** Verbindung mit der GetCapabilities-URL anlegen, Version *Maximum* oder *2.0*.
  QGIS blättert mit ``STARTINDEX``/``COUNT`` und verwendet die Achsenreihenfolge der URN.
- **ArcGIS Pro:** WFS-Verbindung mit der GetCapabilities-URL (2.0.0 oder 1.1.0).
- In der :ref:`server-testmap` kann der WFS eines Dienstes direkt im Browser ausprobiert werden,
  mit allen Requests im Request-Log.
