ApplyEdits Endpoint
===================

Der **ApplyEdits**-Endpunkt fügt in einem Request Features mehrerer Layer hinzu, ändert und löscht sie: alles oder
nichts. Alle Layer müssen daher in derselben Datenbank liegen, und die Datenbank muss Transaktionen unterstützen
(sonst nur ein Layer je Request). Er braucht das Recht *Edit*.

Request-Struktur
----------------

Nur POST: ``https://{server}/geojsonservice/v1/services/{folder}/{service}/applyEdits``

.. code-block:: json

    {
      "type": "ApplyEdits",
      "crs": { "type": "name", "properties": { "name": "EPSG:31256" } },
      "edits": [
        {
          "layerId": "0",
          "adds": [ { "geometry": { "type": "Point", "coordinates": [14000, 300000] }, "properties": { "NAME": "neu" } } ],
          "updates": [ { "oid": 12, "version": "vJrQNHfvTA8pQcqInIN-D0mc", "properties": { "NAME": "geändert" } } ],
          "deletes": [ 13, { "oid": 14, "version": "mSq3DUt0txWiyrSVdwjULSqU" } ]
        },
        {
          "layerId": "1",
          "adds": [ { "geometry": { "type": "Point", "coordinates": [14100, 300100] }, "properties": {} } ]
        }
      ]
    }

- **crs** (optional): das Koordinatensystem der Geometrien.
- **edits**: je Layer ein Eintrag (ein Layer nur einmal) mit **adds** (ohne **oid**), **updates** (mit **oid**) und
  **deletes** (Objekt-IDs, oder ``{ "oid": ..., "version": ... }``).
- **version** (optional, Updates und Deletes): Das Feature wird nur geändert bzw. gelöscht, wenn es noch diese Version
  hat; sonst wird der ganze Request mit ``412`` abgelehnt. Die Version ist das ``ETag`` von *OGC API - Features*
  (``GET .../ogcapi/.../items/{id}``), mit oder ohne Anführungszeichen; beide Schnittstellen verwenden denselben Wert.

Geprüft wird alles, bevor die erste Änderung geschrieben wird: erlaubte Bearbeitungen je Layer, Geometrien, Objekte
außerhalb der Definitionsabfrage des Layers oder nicht vorhanden (``404``), Versionen (``412``).

Response-Struktur
-----------------

.. code-block:: json

    {
      "type": "ApplyEditsResponse",
      "succeeded": true,
      "results": [
        {
          "layerId": "0",
          "adds": [ { "oid": 123, "version": "\"vJrQNHfvTA8pQcqInIN-D0mc\"" } ],
          "updates": [ { "oid": 12, "version": "\"cGFRmrRCTKM4KZt_CV78yW1W\"" } ],
          "deletes": [ 13, 14 ]
        }
      ]
    }

- **adds**, **updates**: die Objekt-IDs (bei neuen Features die vergebenen, 0, wenn die Datenbank sie nicht liefern
  kann) und die **version** nach der Änderung, für die nächste Änderung.
- **deletes**: die gelöschten Objekt-IDs.
