Label Renderer
==============

Um die Features eines Layers zu Beschriften geht man ähnlich vor, wie beim *Rendering*. Im 
*Settings* Dialog für den Layer findet man links den Abschnitt ``Label Renderer``. Hier muss 
zuerst die Checkbox ``Label features for this layer`` aktiviert werden:

.. image:: img/labeling1.png

Es gibt auch hier wieder unterschiedliche *Renderer* die angeboten werden.

Simple Text Renderer
--------------------

Mit diesem Rendere können Features nach Attributwerten beschriftet werden. Das stellt den 
häufigsten Anwendungsfall dar.

Die Eigenschaften des *Renderers* teilen sich die folgende Kategorien auf:

* **Feld / Ausdruck:** Es kann nach einem Feld oder nach einem Ausdruck beschriftet werden.
  Beim Typ ``Field`` kann einfach das Feld ausgewählt werden, nach dem beschriftet werden soll:

  .. image:: img/labeling2.png
      :width: 400

  Mit ``Expression`` kann ein Ausdruck definiert werden. Wenn man auf ``Edit Expression`` klickt, 
  öffnet sich der **Expression Editor**. Dort kann ein Ausdruck angegeben werden, der aus freiem Text
  und Platzhaltern (Feldname in eckigen Klammern) für die Felder zusammengesetzt ist:

  .. image:: img/labeling3.png
      :width: 400

  Im oberen Bereich stehen die Feldnamen. Durch einen Doppelklick auf ein Feld wird es als 
  Platzhalter im Ausdruck angefügt.

  Über *Expressions* können die Feldwerte auch formatiert werden. Eine Formatierung ist für 
  Nummern- und Datumsfelder möglich. Die Syntax für einen Platzhalter mit Formatierung ist dabei 
  folgende:

    ``[Feldname:Formatierung]``, z. B. ``[Feldname:0.00]`` für eine Zahl mit zwei Nachkommastellen.

  Beispiele:

  *Standard-Formate für Zahlen:* 

  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - Zweck	
      - Format
      -	Platzhalter-Beispiel
      -	Ergebnis bei Value = 12345.678
    * - Feste Dezimalstellen 
      -	F2
      -	[BETRAG:F2]
      -	12 345,68
    * - Währung	
      - C	
      - [PREIS:C]	
      - € 12 345,68
    * - Tausendertrennzeichen, Standard	
      - N0	
      - [MENGE:N0]	
      - 12 346
    * - Prozent	
      - P1
      -	[ANTEIL:P1]
      - (Value = 0.256)	25,6 %
    * - Exponential	
      - E3	
      - [WERT:E3]	
      - 1,235E+004
    * - Hexadezimal (Integer)	
      - X8	
      - [ID:X8] 
      - (Value = 48879)	0000BEEF

    
  *Benutzerdefinierte Zahlenformate:*

  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - Zweck 
      - Format-String 
      - Beispiel 
      - Ergebnis
    * - Zwei Dezimalen, Punkt als Trennzeichen 
      -  0.00 
      - [WERT:0.00] (12.3456) 
      -  12.35
    * - Max. zwei Dezimalen 
      - #.## 
      - [WERT:#.##] (12.3) 
      - 12.3
    * - Feste Breite, führende Nullen 
      - 00000 
      - [NR:00000] (42) 
      -  00042
    * - Tausenderpunkt, zwei Stellen 
      - #,##0.00 
      - [SUMME:#,##0.00] 
      - 12 345,68 

  *Standard-Formate für Datum/Zeit:*

  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - Zweck 
      - Format -
      - Platzhalter-Beispiel 
      - Ergebnis bei Value = 2025-04-24 14:30
    * - Kurzdatum 
      - d 
      - [DATUM:d] 
      - 24.04.2025
    * - Langdatum 
      - D 
      - [DATUM:D] 
      - Donnerstag, 24. April 2025
    * - Datum + kurze Zeit 
      - g 
      - [DATUM:g] 
      - 24.04.2025 14:30
    * - ISO-8601
      - o
      - [DATUM:o]
      - 2025-04-24T14:30:00.0000000+02:00

  **Geometrie-Platzhalter: [$feature.length] / [$feature.area]:**

  Neben Feldnamen können in einem Ausdruck auch die speziellen Platzhalter ``[$feature.length]``
  (Länge) und ``[$feature.area]`` (Fläche) verwendet werden. Auch hier ist eine Formatierung wie
  bei Zahlenfeldern möglich, z. B. ``[$feature.length:F2]`` für die Länge gerundet auf zwei
  Nachkommastellen.

  .. note::

     ``[$feature.length]`` / ``[$feature.area]`` unterscheiden sich von einem Feld wie
     ``[SHAPE_LENGTH]``: Ein solches Feld kommt aus der Datenbank (z. B. einer SDE) und enthält
     einen dort gespeicherten, ggf. schon länger nicht mehr aktualisierten Wert.
     ``[$feature.length]`` und ``[$feature.area]`` werden dagegen immer live aus der aktuellen
     Geometrie berechnet.

     Das bedeutet aber auch: Bei projizierten Koordinatensystemen, die keine flächen- bzw.
     längentreue Abbildung liefern (z. B. **WebMercator** oder **WGS84**), entspricht der
     berechnete Wert **nicht** der geodätischen Länge bzw. Fläche! Für exakte Maße sollte die
     Geometrie in einem längen-/flächentreuen (bzw. für den jeweiligen Bereich geeigneten)
     Koordinatensystem vorliegen.

  **SimpleScript-Ausdrücke:**

  Neben einfachen Platzhalter-Ausdrücken unterstützt der **Expression Editor** auch sogenannte
  ``SimpleScript``-Ausdrücke. Sie erlauben bedingte Textblöcke und Textersetzungen, die weit über
  eine reine Platzhalter-Substitution hinausgehen.

  *Grundstruktur:*

  .. code-block:: text

    @@start
    <Zeile 1>
    <Zeile 2>
    ...
    @@end
    [@@replace(Suche,Ersetzung)]
    [@@replace(Suche,Ersetzung)]

  Ein Wert wird nur dann als SimpleScript interpretiert, wenn er buchstäblich mit ``@@start``
  gefolgt von einem Zeilenumbruch beginnt. Andernfalls bleibt der Text unverändert – das ist
  nützlich, falls ein Feldwert zufällig mit dem Text "@@start" beginnt.

  Zwischen ``@@start`` und ``@@end`` steht pro Zeile entweder eine Steuerzeile (``@@if(...)``,
  ``@@endif``) oder eine Inhaltszeile (normaler Text, üblicherweise mit ``[Feld]``- bzw.
  ``[Feld:Format]``-Platzhaltern). Die enthaltenen Inhaltszeilen werden mit Zeilenumbruch wieder
  zusammengefügt – aber nur zwischen tatsächlich enthaltenen Zeilen: Fällt z. B. die erste Zeile
  wegen einer falschen Bedingung weg, entsteht keine führende Leerzeile. Fehlt ``@@end`` ganz,
  bleibt der komplette Originaltext unverändert (kein Fehler, einfach keine Interpretation).

  *Bedingte Blöcke: @@if(...) / @@endif*

  Eine Inhaltszeile erscheint nur, wenn jede sie umschließende ``@@if(...)``-Bedingung wahr ist –
  Verschachtelung wirkt als UND-Verknüpfung, beliebig tief. Es gibt vier Argumentformen
  (kommagetrennt in der Klammer):

  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - Form
      - Beispiel
      - Bedeutung
    * - 1 Argument
      - ``@@if([FELD])``
      - wahr, wenn der Wert nicht leer/nicht nur Leerzeichen ist
    * - 2 Argumente
      - ``@@if([FELD],Wert)``
      - wahr, wenn exakt (case-sensitive) gleich "Wert"
    * - 2 Argumente, leer
      - ``@@if([FELD],)``
      - Trick für "Feld ist leer"
    * - 3 Argumente
      - ``@@if([FELD],op,Wert)``
      - ``op`` ∈ ``eq``/``not``/``lt``/``le``/``gt``/``ge``
    * - variabel
      - ``@@if([FELD],in,W1,W2,W3,...)``
      - wahr, wenn der Wert einem der aufgezählten entspricht

  - ``eq``/``not`` sind Gleichheit/Ungleichheit (case-sensitiver Textvergleich).
  - ``lt``/``le``/``gt``/``ge`` sind numerische Vergleiche. Beide Seiten werden als Zahl geparst
    (zuerst mit Punkt als Dezimaltrennzeichen, dann mit der aktuellen Kultur, z. B. deutschem
    Komma "123,4"); ist eine Seite keine Zahl, ist die Bedingung falsch. Unbekannte Operatoren
    ergeben immer "falsch".
  - ``in`` fasst mehrere gleichwertige Alternativen in einer einzigen Bedingung zusammen, statt
    mehrere fast identische ``@@if``-Blöcke zu benötigen.

  .. note::

     Argumente werden einfach an Kommas getrennt. Ein Feldwert, der selbst ein Komma enthält
     (z. B. ein Dezimalkomma wie "125,4"), verschiebt daher die Argumentzählung. Für die
     Operator-Formen (``eq``/``not``/``lt``/``le``/``gt``/``ge``) gibt es dafür eine
     automatische Rettung: Enthält der erste (Feld-)Wert ein Dezimalkomma, wird das wieder
     korrekt zusammengesetzt – vorausgesetzt, das vorletzte Element ist ein bekanntes
     Operator-Schlüsselwort.

  *Nachbearbeitung: @@replace(Suche,Ersetzung)*

  Nach ``@@end`` können beliebig viele ``@@replace(...)``-Zeilen folgen. Sie werden der Reihe
  nach auf das fertig zusammengebaute Ergebnis angewendet (nicht auf einzelne Zeilen), sodass
  Verkettungen möglich sind – jede Ersetzung arbeitet auf dem Ergebnis der vorherigen.

  - Exakter, case-sensitiver Teilstring-Ersatz (kein Pattern/Regex).
  - Eine leere Ersetzung (``@@replace(Suche,)``) entfernt den gefundenen Text.
  - Kein Treffer → der Text bleibt unverändert.
  - Eine Argumentanzahl ungleich 2 → die Zeile wird ignoriert.

  *Beispiele:*

  Beschriftung nach Typ, mit Präfix ``S:`` für ``Schieber`` und ``V:`` für ``Ventil``
  (Verschachtelung als UND-Verknüpfung):

  .. code-block:: text

    @@start
    @@if([TYP],Schieber)
    S: [NAME]
    @@endif
    @@if([TYP],Ventil)
    @@if([TYP],not,Schieber)
    V: [NAME]
    @@endif
    @@endif
    @@if([TYP],not,Schieber)
    @@if([TYP],not,Ventil)
    [NAME]
    @@endif
    @@endif
    @@end

  Bedingung auf "Feld ist leer" (``@@if([FELD],)``) und Nachbearbeitung mit ``@@replace``:

  .. code-block:: text

    @@start
    @@if([STPKT_NR],)
    [NORMBEZEICHNUNG]
    @@endif
    @@if([STPKT_NR])
    Nr [STPKT_NR] [NORMBEZEICHNUNG]
    @@endif
    @@end
    @@replace(Hausanschluss,HA)

  Ist ``[STPKT_NR]`` leer, wird nur ``[NORMBEZEICHNUNG]`` ausgegeben, sonst
  ``Nr <STPKT_NR> <NORMBEZEICHNUNG>``. Am Ende wird überall "Hausanschluss" durch "HA" ersetzt.

  Mehrere ``@@replace``-Anweisungen, um lange Standardwerte abzukürzen oder ganz auszublenden
  (leere Ersetzung entfernt den Suchtext):

  .. code-block:: text

    @@start
    [TYP]
    @@end
    @@replace(Hausanschluß,HA)
    @@replace(Hausanschluss,HA)
    @@replace(Sonstiger Endpunkt,)
    @@replace(Sonstiger Punkt,)
    @@replace(Sonstiges Punktobjekt,)
    @@replace(Reserve,Res.)

  *Sonstiges:*

  - Zeilenumbrüche im Skript-Quelltext (``\r\n``, ``\r``, ``\n``) werden alle gleich behandelt,
    unabhängig vom Server-Betriebssystem.
  - Alle Vergleiche außer ``lt``/``le``/``gt``/``ge`` sind exakte, case-sensitive
    Zeichenvergleiche (keine Groß-/Kleinschreibungstoleranz beim Wert selbst – nur die
    Operator-Schlüsselwörter wie ``eq``/``in`` sind case-insensitiv).

* **Verhalten:** Hier wird die Priorität des Labels angegeben:

  .. image:: img/labeling4.png
       :width: 400

  Werden mehrere Layer beschriftet, erfolgt dies in der Zeichenreihenfolge der Layer. Um die Labels 
  eines Layers hervorzuheben, kann die Priorität hier angegeben werden. Der Grund dafür ist auch, 
  dass Labels nur gezeichnet werden, wenn auch Platz auf der Karte vorhanden ist. Labels dürfen sich nicht 
  überschneiden. Daher kann es vorkommen, dass ein Label gar nicht gezeichnet wird.
  Label mit höherer Priorität haben eine höhere Chance, gezeichnet zu werden.
  Gibt man als Priorität ``Always`` an, wird es immer gezeichnet, unabhängig von Überschneidungen.
  Das kann allerdings zu unleserlichen Beschriftungen führen, wenn zu viele Texte übereinander liegen.

  ``How many labels`` gibt an, wie oft ein Text vorkommen darf:

   * ``One per feature``: Jedes Feature wird genau einmal beschriftet.
   * ``One per part``: Ist das Feature ein *Multipart*-Feature, wird jeder Teil des Features beschriftet.
   * ``One per name``: Gibt es mehrere Features, die mit demselben Text beschriftet werden, 
     wird nur das erste beschriftet. Es kommen in der Karte dann keine doppelten Texte (für diesen Layer)
     vor.

* **Symbol / Kartografie:** Hier kann das Labeling-Symbol (Schriftart) eingestellt werden:

  .. image:: img/labeling5.png
      :width: 400

  Ähnlich wie bei der Feature-Symbolik gibt es auch hier einen **Symbol Composer** für Textsymbole:

  .. image:: img/labeling6.png 
      :width: 400

  Es gibt eine ``Gallery`` mit vordefinierten Symbolen. Über den ``Stack`` können unterschiedliche 
  Symboltypen hinzugefügt werden:

  * ``Simple Text``: Nur der Text, keine Umrandung
  * ``Glowing Text``: Text mit farbiger Umrandung
  * ``Blockout Text``: Text in farbigem Rechteck

  .. note:: 

     **Glowing** und **Blockout** Text erhöhen die Lesbarkeit von Text, weil sie sich besser 
     vom Hintergrund abheben. Auf Hintergründen wie Luftbildern sind normale Texte oft nur 
     schwer lesbar.

* **Placement / Placement Priority:** Für Punkt- und Linienthemen kann es sinnvoll sein, die Position der 
  Beschriftung festzulegen. Dies kann über ``Placement`` gesteuert werden:
  
  .. image:: img/labeling7.png
      :width: 400

  Der mittlere Punkt würde bedeuten, dass das Label direkt mittig auf den Punkt oder die Linie gesetzt wird.
  Über die Priorität können alternative Positionen vergeben werden. Kann das Label aus 
  Platzgründen nicht gezeichnet werden, werden die weiteren vorgegebenen Positionen der Reihe nach 
  angewendet, bis ein positives Ergebnis erzielt wird.

Scale Dependent/Group Layerrenderer
-----------------------------------

Hier können, wie zuvor beim **Scale Dependent (Feature) Renderer**, Gruppen von *Label Renderern*
definiert werden. So können Layer beispielsweise in unterschiedlichen Maßstäben unterschiedlich 
beschriftet werden. 

Beispielsweise könnte man Länder in kleinen Maßstäben mit einem Länderkürzel beschriften.
Zoomt man weiter in die Karte hinein, wird mit dem vollen Ländernamen beschriftet.

Chart Renderer
--------------

Hier werden anstelle von Text Diagramme (Charts) in die Karte eingefügt:

.. image:: img/labeling8.png

* **Verhalten:** Hier kann der Diagrammtyp angegeben werden (Pie, Bars, Stack).

* **Chart Data:** Hier können die Felder in den Bereich ``Chart Fields`` gezogen werden.
  Aus den Feldwerten dieser Felder wird das Diagramm erstellt.
  (Hinweis: das erste Feld muss auf die Überschrift (``Chart Fields``) gezogen werden.)
  Möchte man ein Feld wieder entfernen, kann es einfach zurück nach links in die Liste gezogen werden.

* **Size:** Charts können eine fixe Größe haben, oder die Größe kann abhängig von der Gesamtsumme sein.
  Dazu gibt man einen (Summen-)Wert ein und eine Größe in Pixel. Die Größe der Diagramme wird dann
  relativ zu diesen Werten berechnet.
