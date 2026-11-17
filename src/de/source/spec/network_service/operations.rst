Operationen
===========

Knotenreferenzen
----------------

Parameter, die Knoten angeben (``start``, ``from``, ``to``, ``sources``, ``sinks``, ``node``, ``block``, ``unblock``),
nehmen eine kommagetrennte Liste von:

- **Layer#OID**, z. B. ``Trafo#5929`` oder ``Niederspannung/Kasten#23660`` (Layer-Name oder Pfad; ist der Name nicht
  eindeutig, muss der Pfad verwendet werden). In URLs wird ``#`` als ``%23`` übergeben: ``start=Trafo%235929``.
- einer **rohen Knoten-Id** (Zahl).
- einem **Typ-Muster**, z. B. ``[*,Trafo,*]`` oder ``Niederspannung.Kasten``: alle Knoten dieses Typs
  (Kommas innerhalb von ``[ ]`` trennen nicht).

Zusammengeführte Punkte werden zu dem Knoten aufgelöst, in den sie zusammengeführt wurden.
Bei ``path`` (``from``, ``to``) und ``info`` (``node``) muss die Referenz genau einen Knoten ergeben.

Gemeinsame Parameter
--------------------

Für ``trace``, ``reach``, ``path`` und ``supply``:

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Parameter
     - Beschreibung
   * - ``filter``
     - Nur Kanten dieser Typen werden verwendet (Typ-Muster).
   * - ``block``
     - Knoten, die für diese Suche gesperrt werden.
   * - ``unblock``
     - Knoten, die trotz Sperre im Netzwerk durchlässig sind.
   * - ``weight``
     - Gewicht für die Kosten. Standard ``length`` (Meter), weitere siehe *Network Description*.
   * - ``maxCost``
     - Kostengrenze (Dezimalzahl, ``.`` oder ``,``). Vor allem für ``reach``.

Für alle Operationen mit Ergebnis (bei ``info`` nur ``f``):

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Parameter
     - Beschreibung
   * - ``geometry``
     - ``false``: Kanten nur als gerade Linie zwischen ihren Knoten. Standard: die tatsächlichen Linienteile.
   * - ``attributes``
     - ``true``: liefert zusätzlich die Felder der Original-Features (ohne Geometrie) für Knoten, zusammengeführte Punkte,
       Kanten und Verletzungen. Erfordert zusätzlich zum Recht ``network`` das Recht ``query``, sonst wird die Anfrage
       abgelehnt (forbidden). Ohne ``attributes`` ändert sich nichts.
   * - ``outFields``
     - Kommagetrennte Feldnamen, Standard ``*`` (alle Felder). Wird pro Layer angewendet: Jeder Layer liefert nur die
       angeforderten Felder, die er hat (Groß-/Kleinschreibung egal). Unbekannte Namen werden ohne Fehler ignoriert,
       ein Layer ohne eines der Felder liefert ``{}``. Das Id-Feld nur, wenn es angefordert wird (die OID steht in ``oid``).
   * - ``outSR``
     - EPSG-Code (``4326`` oder ``epsg:4326``). Standard: Koordinatensystem des Netzwerks.
   * - ``f``
     - ``json``, ``pjson`` (eingerückt), ``geojson`` oder ``csv``. Ohne ``f``: HTML-Formular.

trace
-----

Verfolgt die **Konnektivität** ab den Startknoten, bis zu gesperrten Knoten. Liefert alle erreichbaren Knoten und alle
Kanten; es gibt keine Kostengrenze.

Parameter: ``start`` (erforderlich), ``filter``, ``block``, ``unblock``.

``.../networkserver/trace?start=Trafo%2336&f=geojson&outSR=4326``

Mit Attributen: ``.../networkserver/trace?start=Trafo%2336&attributes=true&outFields=E_TP&f=pjson``

Knoten-``kind``: ``Reached``, ``Blocked``.

reach
-----

**Kostenbegrenzte** Suche ab den Startknoten: Die Suche endet bei ``maxCost`` (gemessen in ``weight``). Liefert den
Kürzeste-Wege-Baum: jeder Knoten mit seinen Kosten (Entfernung vom Start) und die Kanten des Baums.

Parameter: ``start`` (erforderlich), ``maxCost``, ``weight``, ``filter``, ``block``, ``unblock``.

``.../networkserver/reach?start=Trafo%2336&maxCost=250&f=pjson``

Knoten-``kind``: ``Start``, ``Reached``, ``Blocked``.

*Unterschied zu trace:* ``trace`` folgt dem Netzwerk, solange es verbunden und nicht gesperrt ist; ``reach`` bricht bei
``maxCost`` ab und liefert die Kosten je Knoten.

path
----

Kürzester Weg zwischen zwei Knoten. Gibt es keinen, ist ``success`` = ``false``. ``cost`` enthält die Gesamtkosten,
die Knoten die Kosten entlang des Weges.

Parameter: ``from``, ``to`` (erforderlich), ``weight``, ``filter``, ``block``, ``unblock``.

``.../networkserver/path?from=Trafo%2336&to=Niederspannung/Kasten%2323660&f=json``

Knoten-``kind``: ``From``, ``Via``, ``To``.

supply
------

Prüft, von welchen Quellen die Senken versorgt werden. Je Senke ein Knoten mit ``kind`` ``Supplied`` (genau eine Quelle),
``NotSupplied`` oder ``Multiple``; ``source`` nennt die Quellen.

Parameter: ``sources``, ``sinks`` (erforderlich), ``weight``, ``filter``, ``block``, ``unblock``.

info
----

Daten eines Knotens: Typ, Position, Shard, gesperrt, zusammengeführte Punkte, angrenzende Kanten und Nachbarknoten
(``kind`` ``Node``, ``Neighbor``).

Parameter: ``node`` (erforderlich), ``f`` (``json`` oder ``pjson``).

validate
--------

Prüft die Regeln der Netzwerk-Konfiguration (``network.json``) über das gesamte Netzwerk. ``success`` ist ``false``, wenn
eine Regel der Schwere ``Error`` verletzt ist (Warnungen zählen nicht) oder es keine Regeln gibt.

Parameter: ``rules`` (Regel-Ids, kommagetrennt; optional, Standard: alle Regeln), ``geometry``, ``outSR``, ``f``.

``.../networkserver/validate?f=csv``
