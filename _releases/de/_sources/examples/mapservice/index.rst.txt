.. _publish-map-service-example:

Karten als *gView Server Dienst* veröffentlichen
================================================

Kartenprojekte, die mit *gView Carto* erstellt wurden, können im *gView MapServer* veröffentlicht werden.
Dadurch können diese Karten in unterschiedlichsten GIS Anwendungen eingebunden werden 
(Desktop, WebGIS, Leaflet, ...).

Die Kartendienste werden über den *gView MapServer* in unterschiedlichen Schnittstellen bereitgestellt, z.B:

* **OGC WMS**: Eine sehr verbreitete Schnittstelle, die in alle gängigen GIS Softwarepakete eingebunden 
  werden kann. Es können Kartenbilder und Legenden erzeugt und in einfacher Form auch Features abgefragt 
  werden (*Punktidentify*).

* **OGC WMTS**: Bereitstellen von vorprozessierten Tile-Kacheln.

* **GeoServices REST**: Eine von ESRI definierte Schnittstelle auf Basis von REST/Json. 
  Damit können Kartenbilder und Legendenbilder abgeholt werden, und komplexere Abfragen (Punkt, Rechteck, 
  beliebige Geometrie) sowie Suche nach Attributen bereitgestellt werden. 
  Neben den Kartendiensten (*MapServer*) werden hier auch *Feature* Dienste (*FeatureServer*) angeboten, 
  mit denen GeoObjekte nicht nur abgeholt/abgefragt sondern auch bearbeitet und erstellt werden können (*Editing*).

Zum Veröffentlichen von Kartendiensten gibt es drei Möglichkeiten:

* **Kommandozeile:** Der Weg über die Kommandozeile wird im Abschnitt (:ref:`gview_cmd_mxlutil`) 
  beschrieben.
  
* **MapServer WebUI:** Über die Web-Oberfläche des **gView.Servers**.

* **Carto:** Über die **gView.Carto** App.

Veröffentlichen über die MapServer WebUI
----------------------------------------

Die hier beschriebene Vorgehensweise erfolgt über die Web-Oberfläche des Servers. 
Nach dem Aufruf des Servers wird folgende Oberfläche angezeigt:

.. image:: img/mapserver1.png

Um Kartendienste zu veröffentlichen, muss man sich entweder als Administrator oder als berechtigter 
*Client* beim Server anmelden. Um sich als Administrator anzumelden, muss auf die Kachel ``Manage`` 
geklickt werden. Danach muss man sich über das Login-Formular anmelden.

Als *Client* kann man sich über die *Sidebar* (links) über ``Login`` anmelden. *Clients* haben bestimmte 
Rechte auf Dienste und Verzeichnisse mit Diensten (MapRequest, Query, Edit, Publish).
*Clients* dürfen allerdings selber keine Verzeichnisse anlegen. Das ist nur dem Administrator erlaubt.

Da für das Veröffentlichen von Diensten mittels Kommandozeile oder **gView.Carto** ein Client notwendig ist, 
wird hier kurz gezeigt, wie die Vorgehensweise für das Anlegen von Clients ist.

Client anlegen
++++++++++++++

Im Administrationsbereich auf ``Security`` klicken:

.. image:: img/manage1.png

In dieser Oberfläche kann ein neuer Client angelegt werden:

.. image:: img/manage2.png

Im nächsten Schritt sollte ein Verzeichnis angelegt werden, in dem der neue Client Karten veröffentlichen 
darf. Dafür wechselt man zuerst über die *Sidebar* zu ``Browse Services``.
In dieser Ansicht kann unter ``Create Folder`` ein neues Verzeichnis angelegt werden. Klickt man danach 
auf ``Create new folder``, sollte das neue Verzeichnis in der Liste erscheinen.

.. image:: img/manage3.png

Danach wechselt man zurück zum ``Manage`` Bereich. Auch dort sollte das neue Verzeichnis jetzt 
mit einem offenen Schloss erscheinen. Offen bedeutet hier, dass jeder Anwender für dieses 
Verzeichnis alle Rechte (außer *publish*) hat (für Produktionssysteme nicht empfehlenswert).
Klickt man auf das Schloss, öffnet sich ein Fenster. Hier kann in der Auswahlliste der vorhin angelegte
User gefunden werden. Für diesen User sollte zumindest das ``publish`` Recht vergeben werden. 
Für den anonymen Benutzer ``_anonymous`` können beispielsweise nur die ``map`` und ``query`` Rechte 
vergeben werden:

.. image:: img/manage4.png

Die nächsten Schritte können mit dem bereits angemeldeten Administrator User durchgeführt werden. 
Allerdings könnte man sich jetzt auch abmelden und dann über die *Sidebar* mit 
``Login`` als *Client* ``publish-test`` anmelden.

Publish Services
++++++++++++++++

Zum Veröffentlichen von Diensten ist in den Bereich ``Browse Services`` (*Sidebar*) zu wechseln. Hier 
muss noch in das gewünschte Verzeichnis gewechselt werden. Ist man mit dem angemeldeten Benutzer zum 
Veröffentlichen von Diensten berechtigt, erscheint die Schaltfläche ``Publish``. Dort kann ein 
MXL File (Kartenprojekt) ausgewählt werden:

.. image:: img/publish1.png

Unter ``Service Name`` kann der Name des Dienstes angegeben werden (wenn dieser nicht automatisch dem 
Namen des MXL Files entspricht). Klickt man auf den Button ``Publish Service``, 
wird versucht den Dienst zu veröffentlichen. Beim Veröffentlichen wird noch geprüft, ob alle Datenquellen 
vom Server aus erreichbar sind und ob alle verwendeten *Fonts* installiert sind. Ist das Veröffentlichen 
erfolgreich, erscheint der Dienst in der Liste. Besteht bereits ein Dienst mit diesem Namen, 
wird er automatisch ersetzt.

Veröffentlichen über gView.Carto
--------------------------------

Karten können auch direkt von der **gView.Carto** App publiziert werden. Das hat den Vorteil, 
dass nicht extra in die **gView.Server** Web-Oberfläche gewechselt werden muss. Hier sind allerdings
ein paar Voraussetzungen notwendig:

* **gview-webapps.config:** In der Datei `_config/gview-webapps.config` muss der Abschnitt `Publish`
  definiert werden (siehe :ref:`config_webapps`). Hier werden die **Server** und **Clients** angeführt, auf die über die **gView.Carto**
  App veröffentlicht werden kann.

* **Publish Client:** Wie oben schon beschrieben, wird zum Veröffentlichen von Diensten ein 
  *Client* benötigt (Ausnahme: Veröffentlichen über die **Server WebUI**. Dort kann direkt über 
  das Administrator-Konto veröffentlicht werden).
  Ein *Client* kann immer nur in einen *Folder* Dienste veröffentlichen. Dazu muss, wie oben schon 
  beschrieben, ein *Client*, z. B. `carto-publish` mit einem *Secret* vom Administrator angelegt werden.
  Danach müssen, wie oben ebenfalls beschrieben, *Folders* angelegt und dem *Client* `carto-publish`
  das `publish` Recht zugewiesen werden.

Sind alle Voraussetzungen erfüllt, gibt es in der **gView.Carto** App folgendes Werkzeug:

.. image:: img/carto1.png

.. note::

  Das Werkzeug ist nur für **admin** User sichtbar.

Klickt man auf den Button, wird folgender Dialog geöffnet:

.. image:: img/carto2.png

Hier kann über `Select Servers` aus unterschiedlichen **gView.Server** Instanzen ausgewählt werden.
Wurden in der Konfiguration für eine Instanz nicht bereits `Client` **und** `Client Secret` definiert, muss 
die notwendigen **Credentials** hier eingegeben werden. 
Klickt man auf `Connect`, wird eine Verbindung zum Server aufgebaut und die **Folders** aufgelistet,
in die publiziert werden kann:

.. image:: img/carto3.png 

Gibt es bereits *Services* in diesem *Folder*, werden diese ebenfalls aufgelistet. Klickt man auf eines 
dieser Services, wird der Name im Eingabefeld `Service Name` eingetragen.

.. note::

  Veröffentlicht man eine Karte mit dem Namen eines bereits bestehenden *Service*, wird der 
  Dienst am **gView.Server** überschrieben.

Möchte man einen neuen Dienst publizieren, kann ein Name in `Service Name` eingetragen werden.
Hier sollte nur Kleinbuchstaben, Ziffern, `-` und `_` verwendet werden!

Mit dem Button `Publish map as service` wird die Karte veröffentlicht. Ist das Veröffentlichen
erfolgreich, wird das im Dialog angezeigt:

.. image:: img/carto4.png

Treten Fehler auf, müssen diese behoben werden, damit die Karte veröffentlicht werden kann.
Fehler können beispielsweise sein:

* **Datenquellen:** Die **gView.Server** Instanz hat keinen Zugriff auf die Datenquellen in der 
  Karte.

* **Fonts:** Fonts in der Karte (Labels, TrueTypeMarker) sind nicht am Server installiert, auf dem 
  der **gView.Server** läuft.








