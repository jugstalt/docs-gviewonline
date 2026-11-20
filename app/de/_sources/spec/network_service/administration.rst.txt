.. _network-service-administration:

Zugriff, Aufbau und Konfiguration
=================================

Zugriff
-------

- Der Dienst benötigt das Recht ``network``, das pro Benutzer in den Sicherheitseinstellungen vergeben wird.
- In der Dienstliste erscheint ``NetworkServer`` als Typ nur, wenn das Netzwerk aufgebaut ist **und** der Benutzer das
  Recht hat. Die HTML-Seite des MapServers zeigt dann den Button *NetworkServer*.

Speicherort
-----------

``{services-folder}/networks/{folder}/{service}.network``

Der Ordner kann mit ``networks-folder`` in ``_config/mapserver.json`` geändert werden. Das Netzwerk wird mit dem Dienst
gelöscht. Die Einstellung ``directory`` der Netzwerk-Konfiguration gilt nur für *gView Carto* und *gView.Cmd*,
der Server ignoriert sie.

Netzwerk aufbauen
-----------------

- **Server-Verwaltung:** Netzwerk-Symbol des Dienstes, **Build** (Hintergrundjob mit Log, nur für *manage*-Benutzer).
  Reports: ``build.log``, ``duplicates.json``, ``merged.json``, ``diagnostics.json``, ``conflicts.json``.
- **Kommandozeile:** ``gView.Cmd --command Network -u build -mxl map.mxl``.
- Oder ein fertig gebautes Netzwerkverzeichnis an den Speicherort kopieren.

Das gebaute Netzwerkverzeichnis enthält zusätzlich ``spatial.idx`` (alle Knoten, Linienenden und zusammengeführten
Punkte mit ihrer Position) und ``edges.idx``. **Früher gebaute Netzwerke müssen neu gebaut werden**, sonst schlagen
``@x,y``-Referenzen, ``nearest``, Kantenreferenzen und ``outage`` mit "build the network again" fehl.

Netzwerk-Konfiguration
----------------------

Neben den Aufbau-Einstellungen hat die Netzwerk-Konfiguration (beim Aufbau in ``network.json`` geschrieben) zwei
Knoten-Muster, die ``outage`` als Standard verwendet:

- ``supplySources``: die Versorgungsquellen, z. B. ``[*,Trafo,*]``.
- ``customers``: die Kunden, z. B. ``[*,Kunde,*]``.

In *gView Carto* werden sie auf der Seite des Netzwerk-Moduls, Tab **General**, Karte **Supply** gesetzt.

Metadaten aktualisieren
-----------------------

Versorgungsquellen, Kunden, Regeln und Ebenen hängen nicht von den Daten ab. Um sie zu ändern, ohne die Shards neu zu bauen:

- **Carto:** Seite des Netzwerk-Moduls, Tab **Ebenen**, **Refresh metadata**.
- **Kommandozeile:** ``gView.Cmd --command Network -u refresh-metadata -mxl map.mxl`` (oder ``-config net.json``, optional ``-output <Netzwerkverzeichnis>``).

Es werden nur ``supplySources``, ``customers``, ``rules`` und ``tiers`` in ``network.json`` neu geschrieben (die Ebenen-Muster
werden gegen die Typen des gebauten Netzwerks geprüft). Der Server öffnet das Netzwerk neu, sobald sich ``network.json`` ändert.

.. _network-service-tiers:

Ebenen (Tiers)
--------------

Eine Ebene schränkt Suchen auf einen Teil des Netzes ein, z. B. Hoch- (HS), Mittel- (MS) oder Niederspannung (NS). Die
Netzwerk-Konfiguration hat den Abschnitt ``tiers`` (beim Aufbau und mit *Refresh metadata* in ``network.json`` geschrieben):

.. code-block:: json

   "tiers": [
     { "name": "MS", "order": 2, "filter": "Mittelspannung.*", "sources": "[*,Umspannwerk,*]", "sinks": "[*,Trafo,*]" },
     { "name": "NS", "order": 3, "filter": "Niederspannung.*", "sources": "[*,Trafo,*]", "sinks": "[*,Kunde,*]", "block": "[*,Schalter,*]" }
   ]

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Feld
     - Bedeutung
   * - ``name``
     - Name der Ebene, eindeutig, kein Komma. Wird als Parameter ``tier`` verwendet.
   * - ``order``
     - Rang: niedriger ist weiter oben (HS vor MS vor NS).
   * - ``filter``
     - Kantentypen der Ebene (Typ-Muster, siehe unten).
   * - ``nodes``
     - Optionales Knoten-Muster: Knoten, die zur Ebene gehören, obwohl keine ihrer Kanten sie berührt.
   * - ``sources``, ``sinks``
     - Knotenreferenzen oder Muster. Sie ersetzen ``supplySources`` / ``customers``, wenn die Ebene durchsucht wird
       (leer: die globalen).
   * - ``block``
     - Knoten, die in jeder Suche auf der Ebene gesperrt sind.

**Zugehörigkeit.** Kanten gehören über ihren Typ zu einer Ebene. Ein Knoten gehört zu jeder Ebene, deren Kanten ihn berühren;
ein Trafo zwischen Mittel- und Niederspannung liegt also in beiden.

**Muster-Syntax** (auch für ``filter``): ``|`` ist *oder*, ``&`` ist *und* (bindet stärker als ``|``), ``!`` verneint einen Term.
``Mittelspannung.* & !Mittelspannung.Freileitung | Niederspannung.*``. Ein führendes ``!`` in einem Muster ohne ``&`` und ohne
weiteres ``!`` verneint wie bisher das ganze Muster (``!a | b`` = alles außer a und b).

**Suchen** (``trace``, ``reach``, ``path``, ``supply``, ``outage``, ``validate``) nehmen ``tier`` (Namen, durch Komma getrennt):

- Verwendet werden die Kantentypen der Ebenen, geschnitten mit ``filter``. Die Sperren der Ebenen werden zu ``block`` addiert.
- ``supply`` / ``outage``: es gelten die Quellen der Ebene mit dem kleinsten ``order`` und die Senken der Ebene mit dem
  größten ``order`` (``tier=MS,NS``: Quellen von MS, Senken von NS). ``sources`` / ``sinks`` der Anfrage haben Vorrang.
- ``trace`` mit Ebene hält an den Quellen an, die die Ebene selbst nennt (Ergebnis ``Source``), nicht an den globalen Versorgungsquellen. ``stopAtSources=false`` läuft weiter.
- ``validate``: eine Regel mit dem Attribut ``tier`` prüft nur die Kanten und Knoten dieser Ebene (Subjekt und gezählte Kanten);
  mit ``tier`` in der Anfrage werden alle Regeln so eingeschränkt. Die Endtypen einer Kantenregel brauchen keinen Schnitt: die Enden
  sind Enden von Kanten der Ebene und gehören damit unabhängig von ihrem Typ zur Ebene. Bleibt einer Regel nichts zu prüfen (z. B.
  Regel-Ebene ``NS`` und ``tier=MS`` in der Anfrage), wird sie übersprungen und das Ergebnis sagt es.

Ohne ``tier`` ändert sich nichts. Die Ebenen stehen in ``tiers`` der NetworkServer-Ressource. In *Carto* hat das
Netzwerk-Suchwerkzeug das Dropdown **Ebene**.

Konfiguration (appsettings, Abschnitt ``Network``)
--------------------------------------------------

.. list-table::
   :header-rows: 1
   :widths: 25 15 60

   * - Einstellung
     - Standard
     - Bedeutung
   * - ``MaxResultNodes``
     - 50000
     - Größere Ergebnisse werden abgeschnitten (``exceededLimit``).
   * - ``TimeoutSeconds``
     - 120
     - Längere Suchen werden mit Fehler 504 beantwortet.
   * - ``MaxParallel``
     - 2
     - Gleichzeitig laufende Suchen, weitere warten.
   * - ``MaxLoadedShards``
     - 512
     - Geladene Shards pro Netzwerk (0 = unbegrenzt).
   * - ``IdleMinutes``
     - 30
     - Ungenutzte Netzwerke werden nach dieser Zeit aus dem Speicher entfernt (0 = nie).
