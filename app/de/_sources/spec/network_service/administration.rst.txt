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
