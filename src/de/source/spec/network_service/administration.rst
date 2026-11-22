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

- **Carto:** Seite des Netzwerk-Moduls, Tab **Tiers**, **Refresh metadata**.
- **Kommandozeile:** ``gView.Cmd --command Network -u refresh-metadata -mxl map.mxl`` (oder ``-config net.json``, optional ``-output <Netzwerkverzeichnis>``).

Es werden nur ``supplySources``, ``customers``, ``rules`` und ``tiers`` in ``network.json`` neu geschrieben (die Ebenen und
Muster werden gegen die Typen des gebauten Netzwerks geprüft). ``separateTiers`` und ``connectRules`` wirken beim Aufbau:
nach einer Änderung muss das Netzwerk neu gebaut werden. Der Server öffnet das Netzwerk neu, sobald sich ``network.json`` ändert.

.. _network-service-tiers:

Ebenen (Tiers)
--------------

Eine Ebene schränkt Suchen auf einen Teil des Netzes ein, z. B. Hoch- (HS), Mittel- (MS) oder Niederspannung (NS).
**Die Ebene eines Typs ist der Wert seiner ersten Facette** (z. B. ``Ebene``): ``[Mittelspannung,Leitung,Kabel]`` gehört
zur Ebene ``Mittelspannung``. Jeder Wert der ersten Facette, den Kantentypen verwenden, ist eine Ebene, auch ohne Eintrag.
In *Carto* ergibt eine Gruppe je Ebene den Wert über die Typ-Vorlage ``{group-name}`` der ersten Facette.

Der Abschnitt ``tiers`` der Netzwerk-Konfiguration (beim Aufbau und mit *Refresh metadata* in ``network.json``
geschrieben) ergänzt nur Einstellungen:

.. code-block:: json

   "tiers": [
     { "name": "Mittelspannung", "order": 2, "sources": "[*,Umspannwerk,*]", "sinks": "[*,Trafo,*]" },
     { "name": "Niederspannung", "order": 3, "sources": "[*,Trafo,*]", "sinks": "[*,Kunde,*]", "block": "[*,Schalter,*]" }
   ]

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Feld
     - Bedeutung
   * - ``name``
     - Ein Wert der ersten Facette. Wird als Parameter ``tier`` verwendet.
   * - ``order``
     - Rang: niedriger ist weiter oben (HS vor MS vor NS). Leer: die Nummer des Facettenwerts.
   * - ``sources``, ``sinks``
     - Knotenreferenzen oder Muster. Sie ersetzen ``supplySources`` / ``customers``, wenn die Ebene durchsucht wird
       (leer: die globalen). Mit ``separateTiers`` dürfen Linienenden der Ebene an Knoten dieser Typen anschließen.
   * - ``block``
     - Knoten, die in jeder Suche auf der Ebene gesperrt sind.

**Zugehörigkeit.** Kanten gehören zur Ebene ihres Typs. Ein Knoten gehört zur Ebene seines Typs und zu jeder Ebene, deren
Kanten ihn berühren; ein Trafo zwischen Mittel- und Niederspannung liegt also in beiden.

**Muster-Syntax:** ``[Mittelspannung,*,*]`` hat einen Eintrag je Facette (``*`` = beliebig). ``|`` ist *oder*, ``&`` ist
*und* (bindet stärker als ``|``), ``!`` verneint einen Term: ``[Mittelspannung,*,*] & ![*,*,Freileitung] | [Niederspannung,*,*]``.
Ein führendes ``!`` in einem Muster ohne ``&`` und ohne weiteres ``!`` verneint das ganze Muster (``!a | b`` = alles außer a
und b). ``untyped`` sind die Hilfsknoten (Linienenden ohne Punkt); mit getrennten Ebenen hat jede Ebene ihren eigenen
Hilfsknotentyp, z. B. ``untyped & [Niederspannung,*,*]``. Die Kurzform ``Mittelspannung.*`` wird weiter gelesen.

**Suchen** (``trace``, ``reach``, ``path``, ``supply``, ``outage``, ``validate``) nehmen ``tier`` (Namen, durch Komma getrennt):

- Verwendet werden die Kantentypen der Ebenen, geschnitten mit ``filter``. Die Sperren der Ebenen werden zu ``block`` addiert.
- ``supply`` / ``outage``: es gelten die Quellen der Ebene mit dem kleinsten ``order`` und die Senken der Ebene mit dem
  größten ``order`` (``tier=MS,NS``: Quellen von MS, Senken von NS). ``sources`` / ``sinks`` der Anfrage haben Vorrang.
- ``trace`` mit Ebene hält an den Quellen an, die die Ebene selbst nennt (Ergebnis ``Source``), nicht an den globalen Versorgungsquellen. ``stopAtSources=false`` läuft weiter.
- ``validate``: eine Regel mit dem Attribut ``tier`` prüft nur die Kanten und Knoten dieser Ebene (Subjekt und gezählte Kanten);
  mit ``tier`` in der Anfrage werden alle Regeln so eingeschränkt. Die Endtypen einer Kantenregel brauchen keinen Schnitt: die Enden
  sind Enden von Kanten der Ebene und gehören damit unabhängig von ihrem Typ zur Ebene. Bleibt einer Regel nichts zu prüfen (z. B.
  Regel-Ebene ``NS`` und ``tier=MS`` in der Anfrage), wird sie übersprungen und das Ergebnis sagt es.
- ``@x,y``: liegen mehrere Knoten an der Stelle (getrennte Ebenen), gilt der Knoten der Ebene.

Ohne ``tier`` ändert sich nichts. Die Ebenen stehen in ``tiers`` der NetworkServer-Ressource. In *Carto* hat das
Netzwerk-Suchwerkzeug das Dropdown **Ebene**.

.. _network-service-connect:

Verbinden und Teilen beim Aufbau
--------------------------------

Ohne weitere Angaben verbindet der Aufbau alles, was innerhalb der Toleranz liegt: Linienenden und Punkte werden zu einem
Knoten, und ein Knoten am Inneren einer Linie teilt sie. Werden die Ebenen in verschiedenen Abteilungen gepflegt, liegen
Elemente aber oft nur zufällig aufeinander, z. B. eine Niederspannungsmuffe auf einem Stützpunkt einer Mittelspannungsleitung.
Diese Einstellungen der Netzwerk-Konfiguration steuern das:

.. code-block:: json

   "separateTiers": true,
   "nodesOnLine": "ignore",
   "lineEndsOnLine": "report",
   "connectRules": [
     { "at": "inside", "nodes": "[Niederspannung,Trenner,*]", "edges": "[Niederspannung,*,*]", "action": "connect" }
   ]

- ``separateTiers`` (Standard ``false``): verbunden und geteilt wird nur innerhalb einer Ebene. Linienenden einer Ebene
  schließen zusätzlich an ihre ``sources`` an (Typ-Muster, z. B. der Trafo der Niederspannung). Elemente ohne Ebene
  verbinden sich mit allem. Echte Knoten verschiedener Ebenen an derselben Stelle sind kein Konflikt, sondern zwei Knoten;
  Linienenden verschiedener Ebenen an derselben Stelle ergeben zwei Hilfsknoten (ein Übergang zwischen Ebenen braucht
  einen echten Knoten). Darf ein Linienende an mehrere Knoten einer Stelle anschließen, werden sie wie mit
  ``nodeConflicts`` zusammengeführt (``priorityByToc``: der Layer weiter oben im TOC gewinnt, die anderen werden Aliase).
- ``connectRules``: die erste passende Regel entscheidet, ohne Treffer die Ebenen (oder: alles verbindet sich).
- ``nodesOnLine`` und ``lineEndsOnLine`` (``connect``, ``ignore`` oder ``report``, Standard ``connect``): ob ein echter
  Knoten bzw. ein Linienende (T-Stoß) am Inneren einer Linie sie teilt, wenn keine Regel passt und – mit ``separateTiers`` –
  beide in derselben Ebene liegen. Ein nicht geteilter T-Stoß bleibt unverbunden; mit ``report`` stehen die Stellen im
  Aufbau-Log.

Die Entscheidung beim Teilen: erste passende Regel, dann eine andere Ebene (``ignore``), dann ``nodesOnLine`` /
``lineEndsOnLine``. Beim Anschließen an Linienenden: erste passende Regel, dann die ``sources`` der Ebene, dann die Ebenen.

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Feld
     - Bedeutung
   * - ``at``
     - ``end``: ein Linienende trifft einen Knoten (anschließen?). ``inside``: ein Knoten liegt am Inneren einer Linie
       (teilen?); der Knoten ist ein echter Knoten oder der Hilfsknoten eines T-Stoßes (``untyped``). ``any`` (Standard): beides.
   * - ``nodes``
     - Knotentypen (Muster), z. B. ``[Niederspannung,Trenner,*]`` oder ``untyped & [Niederspannung,*,*]``.
   * - ``edges``
     - Kantentypen (Muster).
   * - ``action``
     - ``connect`` (verbinden bzw. teilen), ``ignore`` (nicht verbinden, still) oder ``report`` (nicht verbinden und im
       Aufbau melden: ``ConnectionRefused`` / ``SplitRefused`` mit Ort und beiden Objekten, wahrscheinlich ein Datenfehler).

Das Beispiel beschreibt ein Netz, dessen Linien schon an den echten Knoten aufgeteilt sind: nur Trenner teilen eine
Niederspannungslinie, andere Knoten teilen nichts, T-Stöße werden gemeldet. In *Carto*: Seite des Netzwerk-Moduls, Tab
**Connections** (oben *Separate tiers* und die beiden Standards, darunter die Regeln). Im Muster-Editor wählt der Schalter
**untyped** einer Zeile die Hilfsknoten dieser Typen (``untyped & [Niederspannung,*,*]``); **Text** zeigt das ganze Muster
als Text, z. B. für ``&`` und ``!``. Nach einer Änderung muss das Netzwerk neu gebaut werden.

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
