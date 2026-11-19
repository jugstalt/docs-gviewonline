GeoServices REST Interface
==============================

**GeoServices REST** is an interface developed by ESRI for the ArcGIS Server.
**gView.Server** supports this interface (*MapServer* and *FeatureServer*). Since this interface 
offers various tools on the web interface to test a **gView.Server** service, a dedicated menu 
item is provided for it:

.. image:: img/geoservices1.png 

**GeoServices REST** is a *RESTful API* where commands are sent to the server using 
HTTP GET and POST requests, typically returning a JSON document.

On the web interface, these JSON documents can be displayed. For better readability,
YAML is chosen as the display standard instead of JSON.

The overarching JSON file returns the **Folders** and services of the first level:

.. image:: img/geoservices2.png

Hyperlinks are highlighted as buttons in the YAML. Additionally, some listings (Services) are 
grouped according to defined attribute values (``Type: MapServer``, ``Type: FeatureServer``).

Clicking on one of the hyperlinks takes you to the next view. The YAML representation 
for a ``MapServer`` service looks something like this:

.. image:: img/geoservices3.png 

Here, further links to individual layers are available, where details about the fields are 
listed, for example.

Above the YAML representation, there are further link-buttons for some types.
For ``MapServer`` types, for example, ``ExportMap`` is interesting. With an ``ExportMap`` request,
a map image for a specific area can be fetched. Clicking on ``ExportMap``
opens a form in which the desired values for the request can be entered:

.. image:: img/geoservices4.png

The output format can be switched from ``json`` to ``pjson`` (where ``p`` stands for *Pretty*,
which ensures better readability of the result). Confirming the form with ``Submit``
yields approximately the following result:

.. image:: img/geoservices5.png

The server creates an image and stores it in the *output directory*. The returned URL
can be copied from the JSON and pasted into the browser's address bar. If everything has worked, a
map image should be displayed.

The same result should be obtained if one steps back and specifies the value ``image`` 
for ``OutputFormat`` in the form. This eliminates the intermediate step via the
JSON document:

.. image:: img/geoservices6.png

.. note::
   By default, the form displays the largest possible view. If no themes are visible at this scale, 
   the result might also be an empty image.
   Additionally, the server returns the default layer visibility if no visibility 
   is specified in the form.

In the same way, queries on layers can also be tested
(go to *Layer YAML* and click on ``Query``).

.. note::
   The parameters correspond to the GeoServices REST specification. 
   This can be referenced at ESRI, for example, 
   at https://developers.arcgis.com/rest/services-reference/enterprise/export-map.htm
