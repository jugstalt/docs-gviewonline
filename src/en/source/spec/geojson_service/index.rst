GeoJson Service Specification
=============================

Services 
--------

**GET**

https://www.geojsonserver.org/geojsonservice/v1/services
https://www.geojsonserver.org/geojsonservice/v1/services/folder

Capabilities
------------

https://www.geojsonserver.org/geojsonservice/v1/services/service/capabilities
https://www.geojsonserver.org/geojsonservice/v1/services/folder/service/capabilities

Map 
---

**GET/POST**

https://www.geojsonserver.org/geojsonservice/v1/services/service/map
https://www.geojsonserver.org/geojsonservice/v1/services/folder/service/map

Legend 
------

**GET/POST**

https://www.geojsonserver.org/geojsonservice/v1/services/{service}/legend
https://www.geojsonserver.org/geojsonservice/v1/services/{folder}/{service}/legend

Query 
-----

**GET/POST**

https://www.geojsonserver.org/geojsonservice/v1/services/{service}/query/{0}
https://www.geojsonserver.org/geojsonservice/v1/services/{folder}/{service}/query/{0}

Features 
--------

**POST/PUT/DELETE**

https://www.geojsonserver.org/geojsonservice/v1/services/{service}/features/{0}
https://www.geojsonserver.org/geojsonservice/v1/services/{folder}/{service}/features/{0

.. toctree::
   :maxdepth: 1
   :caption: Table of contents:

   getinfo
   getcatalog
   getservicecapabilities
   getmap
   getfeatures