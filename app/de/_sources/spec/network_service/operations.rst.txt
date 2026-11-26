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
  (Kommas innerhalb von ``[ ]`` trennen nicht). Ein Muster kann mit einem führenden ``!`` für das ganze Muster
  **negiert** werden: ``!a | b`` bedeutet alle Typen der Art außer ``a`` und ``b`` (Hilfsknoten bleiben wie bei ``*``
  außen vor). Ein Muster, das alle Typen ausschließt, ist ein Fehler.
- einem **Ort**: ``@x,y`` oder ``@x,y~radius`` = der nächstgelegene Netzwerkknoten zu diesem Ort, im Koordinatensystem
  des Netzwerks oder in ``inSR``. Das schließt **Hilfsknoten** ein, also Linienenden, die erst beim Aufbau entstehen
  (z. B. dort, wo eine *Leitung* an einem *Anlagen Verbinder* in einer Station hängt). Der Radius wird in
  Netzwerkeinheiten angegeben, Standard ist das 10-fache der Aufbau-Toleranz. Liegt kein Knoten im Radius, schlägt die
  Anfrage fehl. Das Komma in ``@x,y`` trennt die Liste nicht, z. B. ``start=@5,0, Trafo%231``. Liegen an der Stelle
  mehrere Knoten (getrennte Ebenen, siehe :ref:`network-service-connect`), wählt ``tier`` den Knoten der Ebene; bleiben
  mehrere übrig, schlägt die Anfrage fehl und nennt sie (dann per ``Layer#OID`` oder ID angeben).

Zusammengeführte Punkte werden zu dem Knoten aufgelöst, in den sie zusammengeführt wurden.
Bei ``path`` (``from``, ``to``) und ``info`` (``node``) muss die Referenz genau einen Knoten ergeben.

Linienenden werden in den Ergebnissen z. B. als ``end of Verbinder#12, Leitung#3456`` bezeichnet.

Beispiel: ``filter=![*,Anlagen Verbinder,*]`` hält einen Trace ab einem Linienende auf seinem Strang, statt zurück über
die Station zu laufen.

Kantenreferenzen
----------------

Parameter, die Kanten angeben (``disable`` bei allen Suchen und bei ``outage``), nehmen eine kommagetrennte Liste von:

- **Layer#OID**: alle Teile des Linien-Features.
- **Layer#OID/part**: ein Teil des Linien-Features.
- einer **rohen Kanten-Id** (Zahl).

Gemeinsame Parameter
--------------------

Für ``trace``, ``reach``, ``path``, ``supply`` und ``outage``:

.. list-table::
   :header-rows: 1
   :widths: 20 80

   * - Parameter
     - Beschreibung
   * - ``filter``
     - Nur Kanten dieser Typen werden verwendet (Typ-Muster).
   * - ``tier``
     - Nur diese Ebenen (Namen, durch Komma getrennt), siehe :ref:`network-service-tiers`.
   * - ``stopAtSources``
     - ``trace`` mit ``tier``: ``false`` hält nicht an den Quellen der Ebene an.
   * - ``block``
     - Knoten, die für diese Suche gesperrt werden.
   * - ``unblock``
     - Knoten, die trotz Sperre im Netzwerk durchlässig sind.
   * - ``disable``
     - Kanten (siehe *Kantenreferenzen*), die nur für diese eine Suche ausgeschaltet werden.
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
   * - ``snap``
     - ``true``: (mit Geometrie) die Enden jedes Kantenteils werden exakt auf die Position seines Von-/Nach-Knotens
       gesetzt, sodass das Ergebnis ein gültiges Netzwerk ist (z. B. für Lastfluss-Software, die ein gesnaptes Netz
       braucht). Zusammengeführte Punkte werden an der Position ihres Knotens ausgegeben. Eine Verschiebung größer als
       die Aufbau-Toleranz wird in ``messages`` gemeldet. Standard ``false``.
   * - ``outSR``
     - EPSG-Code (``4326`` oder ``epsg:4326``). Standard: Koordinatensystem des Netzwerks.
   * - ``inSR``
     - EPSG-Code der Koordinaten in ``x``/``y`` und in jeder ``@x,y``-Referenz. Standard: Koordinatensystem des
       Netzwerks. Der Radius bleibt in Netzwerkeinheiten.
   * - ``f``
     - ``json`` (Standard), ``pjson`` (eingerückt), ``geojson`` oder ``csv``. Ganz ohne Parameter: HTML-Formular.

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

outage
------

Berechnet die Versorgung mit und ohne die ausgeschalteten Kanten und meldet, welche Kunden ihre Versorgung verlieren.
Untersucht wird nur der Teil des Netzwerks, der mit den ausgeschalteten Kanten verbunden ist.

Parameter: ``disable`` (erforderlich: die auszuschaltenden Kanten), ``sources``, ``sinks`` (beide Standard:
``supplySources`` bzw. ``customers`` der Netzwerk-Konfiguration), ``filter``, ``block``, ``unblock``, ``weight`` usw.

``.../networkserver/outage?disable=Leitung%2312&f=pjson``

Ergebnis:

- Knoten-``kind`` ``Affected``: Kunden, die vorher versorgt waren, nach dem Ausschalten aber nicht mehr. Zusammengeführte
  Punkte stehen als Alias-Zeilen dabei.
- ``messages`` mit den Anzahlen: betroffen, weiterhin versorgt (Masche) und schon vorher nicht versorgt.
- Kanten-``kind`` ``Disabled`` (ausgeschaltet) und ``Dead`` (ohne Versorgung).

nearest
-------

Knoten in der Nähe eines Ortes. Jeder Knoten wird einmal geliefert, der nächste zuerst; ``cost`` ist die Entfernung.

Parameter: ``x``, ``y`` (erforderlich), ``radius`` (Netzwerkeinheiten, Standard 10-fache Aufbau-Toleranz), ``count``
(Standard 1), ``inSR``, ``outSR``, ``f``.

``.../networkserver/nearest?x=5&y=0&count=3&f=pjson``

Knoten-``kind``: ``Nearest``.

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
