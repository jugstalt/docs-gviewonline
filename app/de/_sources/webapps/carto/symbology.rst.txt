Darstellung und Symbolik
========================

Hier wird beschrieben, wie die Darstellung einzelner Layer angepasster werden kann. In unserem Beispiel 
sollen Gemeinden orange und die Straßen in gelb und rot erscheinen. Der einfachste Weg, 
um ein Symbol zu ändern, ist ein Doppelklick auf das entsprechende Legendensymbol im TOC.
Damit öffnet sich eine Maske, die die wichtigsten Eigenschaften des Symbols anzeigt:

.. image:: img/symbology1.png
   :width: 260

Bei flächenhaften Symbolen sind das beispielsweise:

* ``Color``: Die Farbe des Symbols (Klick auf das Rechteck öffnet die Farbpalette)
* ``Width``: Die Breite der Linie
* ``Dashstyle``: Der Style der Line (durchgezogen, strichliert, ...)
* ``Opacity``: Die Deckkraft der Fläche
* ``Smoothing``: Der Glättungsmodus (None, Antialias)

Im oberen Bereich der Maske kann ein Label für das Legendensymbol vergeben werden. Darunter ist die 
*Vorschau*, die sich sofort ändert, wenn eine Eigenschaft geändert wird. Daneben sind drei Buttons
mit folgenden Tooltips:

* ``Create new random symbol``: Erstellt ein zufälliges neues Symbol
* ``Undo``: Macht den ``Create new random symbol`` Befehl wieder rückgängig machen
* ``Symbol Composer``: Über den **Symbol Composer** können komplexere Symbole erstellt werden. 
   Der **Symbol Composer** kann ebenfalls geöffnet werden, wenn auf die *Vorschau* geklickt wird.

Um die Änderungen zu Übernehmen, kann man man die Maske mit ``Done`` schließen.

Symbol Composer
---------------

Der *Symbol Composer* gestaltet sich für alle Symbole ähnlich. 

.. image:: img/symbology2.png 
   :width: 260

Links werden die Symbolebenen und die Vorschau angezeigt.
Rechts können die Attribute wie Farbe, Linienstärke usw. für ein Symbol angegeben werden. 
Die Attribute werden in der Regel thematisch gruppiert. Durch einen Klick auf eine Kategorie
können die Attribute bearbeite werden.

In der Kategorie ``Outline Symbol`` können die Eigenschaften der Umrandungslinie der Fläche 
bestimmt werden. Hier werden wieder die gängigsten Attribute angezeigt:

.. image:: img/symbology3.png 
    
Manche Attribute können über Eingabefelder und Auswahllisten geändert werden, andere bieten einen 
oder mehrere Buttons an: 
Bei ``Color`` wird durch einen Klick auf die Farbe die Farbpalette geöffnet.
Der Button ``Edit: Line Symbol`` öffnet den **Symbol Composer** für 
die Umrandungslinie. Möchte man keine Umrandungslinie, wird das Linien Symbol mit 
``No Symbol`` entfernt. 

Im oberen Bereich den **Symbol Composer** befindet sich die ``Gallery``. Öffnet man die 
Galerie durch einen einen Klick, werden vordefinierte Symbol für den aktuellen Geometrie Typ 
angezeigt:

.. image:: img/symbology4.png

Klick man auf eine Kachel, wird diese als neues Symbol übernommen. Möchte man das angezeigt 
Symbol nur in ein Stapel der Symbol übernehmen, muss man auf das ``+`` Symbol klicken, das erscheint,
wenn man die Maus über die Kachel bewegt.

Bevor wir den *Symbol Stapel* (Symbol Stack) beschreiben, ändern wir die Farbe für die 
Fläche auf Orange und schließen den Dialog mit ``Apply``.

Wurde der **Symbol Composer** über den TOC geöffnet, muss die Symbolmaske im TOC danach mit 
``Done`` bestätigt werden. Danach wird die Karte mit dem geänderten Symbol neu gezeichnet.

Beim Linensymbol für den *Streets* Layer kann gleich vorgegangen werden, um den 
**Symbol Composer** zu öffnen:

.. image:: img/symbology5.png

Ziel ist es eine gelbe Linie mit rotem Rand als Symbol zu erstellen. Der einfachste Weg ist hier
die ``Gallery`` zu öffnen und Symbol auszuwählen. Zum Verständnis soll hier das Symbol ohne
diese Abkürzung zu erstellen.

Eine Liniensymbol kann grundsätzlich immer nur eine Farbe aufweisen. Für Linien mit Umrandung gibt es 
kein Standardsymbol. Die Idee ist hier mehrere Symbol Ebenen zu zeichnen. Zuerst sollte ein 
Rote Linie gezeichnet werden, deren Breite der Breite des Symbols entspricht. Darüber kann man 
im zweiten Schritt eine dünnere gelbe Linie zeichnen.

Dazu kann der **Symbol Stack** im **Symbol Composer** verwendet werden. Momentan gibt es eine 
Ebene. Über die Einstellung in ``General`` (rechts) kann die Farbe auf rot und die Liniestärke auf
``5`` gesetzt werden.

.. image:: img/symbology6.png

Im nächsten Schritt fügt man über das ``Add`` Menü im ``Stack`` Bereich eine neue Ebene vom
Typ ``Simple Line Symbol`` hinzu. Im Stack erscheint eine neue Linie. Um die Eigenschaft einer 
Symbolebene zu bearbeiten muss sie durch Klick im ``Stack`` ausgewählt werden. Für die neue Linie stellt 
man jetzt als Farbe gelb und Linienstärke ``3`` ein:

.. image:: img/symbology7.png

.. note::

    Die Zeichenreihenfolge von Symbol erfolgt im ``Stack`` von oben nach unten. Die erste (oberste)
    Ebene wird zuerst gezeichnet. Die Reihenfolge der Ebenen kann mittels ziehen mit 
    der linke Maustaste geändert werden.

    Einzelne Symbolebenen können über das *Papierkorb* Symbol wieder gelöst werden.

Wir können den Dialog jetzt mit ``Apply`` schließen. Ist danach die die Symbol Maske mit TOC
geöffnet kann diese mit ``Done`` bestätigt werden. Die Symboldarstellung in der Karte 
sollte jetzt wie folgt aussehen:

.. image:: img/symbology8.png

Im Kreuzungsbereich der Straße erkennt man, dass die Darstellung noch nicht ganz perfekt ist.
Der Grund ist, das die einzelnen Objekte hintereinander gezeichnet werden. Jedes Objekt wird einzeln 
betrachtet und beide Symbol Ebenen pro Objekt gezeichnet.

Das Verhalten kann über die Eigenschaften des *Renderers* verändert werden. Ein *Renderer* bestimmt,
welche Methoden beim Zeichnen von Objekten angewendet werden. Um die Eigenschaften des *Renderers* 
zu ändern. Muss der Layer im TOC ausgewählt werden. In der Werkzeugleiste erscheinen farblich 
hervorgehoben Werkzeuge, die sich auf den ausgewählten Layer beziehen (siehe Abbildung oben). 

Die Render Eigenschaften findet man unter dem Werkzeug ``Layer Settings``. Im *Layer Settings* 
Dialog muss links zu ``Feature Renderer`` gewechselt werden:

.. image:: img/symbology9.png

Es gibt unterschiedliche *Renderer* Typen, für diese Anforderung ist ``Single Symbol`` ausgewählt.
Das kann erst einmal so belassen werden. ``Single Symbol`` bedeutet, dass das gleiche Symbol (das 
aus mehreren Ebenen bestehen kann) für alle Objekte aus dem Layer verwendet werden soll.

Wichtig hier ist die Eigenschaft ``Cartography/Ordering``. Die Eigenschaft bestimmt, wie die 
einzelnen Symbolebenen gezeichnet werden:

* ``Simple``: Jedes Objekt wird einzeln betrachtet und gezeichnet. Dabei werden immer gleich alle
  Symbolebenen gezeichnet.

* ``Symbol Order``: Die Objekte werden pro Symbol Ebene gezeichnet. Zuerst werden werden alle Objekte 
  nur mit der ersten Symbol Ebene gezeichnet. Danach werden wieder alle Objekte mit der zweiten 
  Symbol Ebene gezeichnet, usw.

Wählt man ``Symbol Order`` und bestätigt den Dialog mit ``Apply`` sieht das Ergebnis wie folgt aus:

.. image:: img/symbology10.png

.. note::

    Die Symbolik eines Layers kann auch üben den **Layer Settings** Dialog geändert. 
    Im Abschnitt ``Feature Renderer`` werden je nach **Renderer** die Symbole angezeigt.
    Hier können entweder, wie in der Symbolmaske des TOC, die wichtigsten Symboleigenschaften
    eingestellt oder der **Symbol Composer** geöffnet werden.