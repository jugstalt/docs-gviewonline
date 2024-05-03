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
