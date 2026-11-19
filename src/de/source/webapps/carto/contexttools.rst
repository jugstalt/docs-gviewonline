Kontextbezogene Werkzeuge
==========================

Nicht alle Werkzeuge in der Werkzeugleiste sind statisch bzw. immer sichtbar.
Es gibt Werkzeuge, die sich auf einen bestimmten Kontext beziehen, z.B. 
auf einen Layer, der gerade im TOC ausgewählt ist.

Um einen Layer im TOC auszuwählen, reicht es, ihn mit der Maus anzuklicken.
Die Auswahl wird farblich dargestellt. Zusätzlich erscheinen, ebenfalls farblich 
hinterlegt, neue Werkzeuge, die sich auf diesen Layer beziehen:

.. image:: img/contexttools1.png

Bei *Feature-Layern* werden beispielsweise folgende Werkzeuge angezeigt:

* **Data Table:** Zeigt die Daten des Layers in Tabellenform an.
* **Layer Settings:** Öffnet den Dialog zu den Einstellungen des Layers.

.. note::

    In der farblich hervorgehobenen Toolbox wird mit dem Text ``Context: ...`` angezeigt,
    auf welches Objekt (hier Layer *Streets*) sich die Werkzeuge beziehen.

Ein Gruppenlayer hat teilweise andere Kontextwerkzeuge:

.. image:: img/contexttools2.png

* **Add Data:** Entspricht ``Add Data`` aus der ``Map`` Toolbox. Allerdings werden hier die 
  hinzugefügten Layer direkt in diese Gruppe eingefügt.
* **Layer Settings:** Auch Gruppenlayer haben *Settings*. Diese können hier verwaltet werden.
