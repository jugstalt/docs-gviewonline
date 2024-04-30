Installation
============

Die Installation erfolgt über das Kommandozeilen-Programm ``gview.deploy`` oder ``gview.deploy.exe``.
Dieses Tool erledigt folgende Aufgaben:

* Neuinstallation von *gView.Web* und *gView.Server*
* Verwalten von Deploy Profilen (zB ``local``, ``test``, ``staging``, ``production``)
* Verteilen von Änderungen an der Konfiguration (zB ``mapserver.json``, ``gview-web.config``)

Neue Version ausliefern
-----------------------

Unter Windows kann das Programm beispielsweise nach ``C:\deploy\gview-gis`` kopiert werden.

Startet man das Programm zum ersten Mal, muss zuert ein Profil angelegt werden.
In Profil kann beispielweise ``test``, ``staging``, ``production`` sein. Da wir im ersten
Schritt *gView GIS* nur einmal local testen wollen, bietet sich ein Profil mit dem 
Namen ``local`` für den Start an:

.. code-block:: batch
   :emphasize-lines: 1,5

   C:\> .\gview.deploy.exe

   Work-Directory: C:\\deploy\\gview-gis
   Choose a profile or create a new by enter an unique name, eg. production, staging, test
   Input profile index [0]: local

In nächsten Schritt bietet das Programm an, den aktuellen Release von *Github* herunter zu laden,
falls noch nicht vorhanden. 

.. code-block:: batch

   Do you want to download latetest version from GitHub? Y/N [Y]

Ist das nicht möglich, kann der letzte Release auch manuell 
herunter geladen werden. Dazu müssen die ZIP Dateien ins ``download`` Verzeichnis gelegt werden.
Im Beispiel also hier: ``C:\deploy\gview-gis\download``

Liegen ZIP Dateien im ``download`` Verzeichnis, werden die unterschiedlihen Versionen
angezeigt:

.. code-block:: batch

   Choose a version
   0 ... 6.24.1801
   Input version index [0]:

Die neueste Version bekommt den Index ``0``. 

.. note::

   Alle Werte die über das ``gviewdeploy`` eingeben werden, müssen bei späteren
   Aufrufen nicht mehr eingegeben werden. Stattdessen werden diese Werte mit einer 
   Indexnummer angezeigt. Man muss damit nur noch die entsprechende Nummer eingeben 
   bzw reicht es einfach ``ENTER`` zu drücken, wenn der gewünschte Index der
   vorgeschlagene Wert ist, zB ``Input version index [0]`` => ``ENTER`` => Version mit
   Index ``0``.

Im nächsten Schritt kann festgelegt werden, welche Produkt installiert werden soll. 
``Everything``deployed sowohl ```gView.Server`` als auch ``gView.Web``:

.. code-block:: batch

   Choose a product
   0 ... Everything
   1 ... gView.Server
   2 ... gView.Web
   Input product index [0]:

Nachdem die Version und Produkt gewählt wurde, fragt das Deployment Tool noch einmal nach, ob 
tatsächlich die gewählte Version mit dem Profil deployed werden sollte:

.. code-block:: batch

   Deploy 'Everything' from version 6.24.1801 to profile local
   Do you want to continue? Y/N [Y]

Ein ``ENTER`` oder ``Y`` Started den Deploy Vorgang.

Publiziert man ein Profil (hier ``local``) das erste Mal, müssen noch ein paar 
Werte bekannt geben werden. Möcht man den Standardwert verwenden, reicht es, die Frage
mit ``ENTER`` zu bestätigen.

.. code-block:: batch

   Target installation path [C:\apps\gview-gis]:
   Repsitory path [C:\apps\gview-gis\local\gview-repository]:
   gView Server online url [http://localhost:5050]:

* **Target installation path:** Der Pfad, an dem gview-gis installiert werden sollte
  unter diesem Verzeichnis, legt das Deploy Werzeug noch einem Order mit dem Profil
  Namen und der Version an. Hier würde die App unter ``C:\\apps\\gview-gis\\local\\6.24.1801``
  installiert werden.

* **Repository path:** Im Repository Path, werden unterschiedliche Dateien gespeichert, die
  für das funktionieren der Software notwendig sind, beispielsweise die Karten Dokumente (XML 
  Files), die vom Kartenserver veröffentlicht werden. Der Repository Ordner wird 
  normaleweise im Verzeichnis des Profiles (hier: ``C:\\apps\\gview-gis\\local``) gelegt.
  Da der Ordner nicht im *Versions* Ordner liegt, kann er von einer neuen installierten Version
  gleich mit verwendet werden. Wichtig ist, dass unterschiedliche Profile ihr eigenes 
  Repository Verzeichnis verwenden.

* **gView Server online url:** Eine Url, unter der der *gView.Server* zugänglich sein wird.
  Möchte man das ``local`` Profil testen und die Programme nur lokal ausführen, erfolgt das 
  in der Regel über http://localhost:5050.
  Der Verteil diesen Wert hier Festzulegen ist, dass später in der *gView.Web* App eine
  zusätzliche Kachel zum Aufruf des *gView.Servers* angeboten wird. Das erleichtert die 
  Administration. Ohne diese Url, würden nur die Kacheln für *gView.Carto* und 
  *gView.Explorer* angezeigt werden.

Die nächsten Werte legen wir den **Admin User** und das Admin Passwort fest.
Außerdem definieren wir einen **Carto User** ein.
Das Passwort ist jeweils einzugeben:

.. code-block:: batch

   gView Admin Username [admin]:
   gView Admin Password [*****]: my-secret-admin-password
   gView Admin Username [carto]:
   gView Admin Password [*****]: my-secret-carto-password

Der Unterschied der beiden User ist, dass der **Carto User** nur auf eingeschränkte 
Werkzeuge zugreifen kann. Er kann beispielsweise den **gView.Explorer** nicht aufrufen
sondern nur **gView.Carto**. Außerdem sieht er die eigentlichen *Connection Strings*
der Verbindungen nicht. Der **Carto User** kann somit nur auf vordefinierte Verbindungen
zugreifen, aber keinen eigenen Verbindungen zu Datenbanken anlegen, etc. Dieser User 
sollte von Benutzern verwendet werden, um neue Karte zu erstellen. Diese User müssen in 
der Regel keine Datenbank Credentials kennen.

Danach startet der Deploy Vorgang:

.. code-block:: batch

   ***********************************************************************
   Create a new webgis repositiry C:\apps\gview-gis\local\gview-repository
   ***********************************************************************

   Deploy version 6.24.1801
   Deploy gView Server:
   ...succeeded 972 items created
   Deploy gView Web:
   ...succeeded 448 items created
   Overrides
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\server\override\_config\mapserver.json
   ...succeeded 1 items created/overridden
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\web\override\_config\gview-web.config
   ...succeeded 1 items created/overridden

Es werden sowohl *gView.Web* als auch *gView.Server* deployed. Nach dem entpacken der ZIP Files
werden benutzerspezifische Files aus dem ``_deploy_repository\profiles\{profile}\[server|web]\override``
Verzeichnis ins jeweilige Applikationsverzeichnis kopiert.
Hier wird beispielsweise die Konfiguration aus dem Installationspaket mit den Konfiguration aus dem
aktuellen Profil überschrieben. 

.. note::
   
   In die *Override* Verzeichnisse können können beliebige Dateien kopiert werden, die zusätzlich
   in die Applikationsverzeichnisse kopiert oder überschrieben werden sollte.
   Konfigurationsdateien sollten nie direkt im Applikationsverzeichnis (Deploy Verzeichnis) geändert
   werden, sondern immer im *Override* Verzeichnis. Damit wird sicher gestellt, dass Änderungen
   an der Konfiguration auch beim nächsten Update eines Profiles wieder kopiert werden.
  
Aktuelle Konfiguration ändern
-----------------------------

Fügt am Änderungen in der Konfiguration durch (zB ``mapserver.json``) erfolgt dies im *Override*
Verzeichnis, danach führt man erneut ``gview.deploy`` aus und bekommt folgende Meldung:

.. code-block:: batch

   Choose a profile or create a new by enter an unique name, eg. production, staging, test
   0 ... local
   Input profile index [0]:

   Do you want to download latetest version from GitHub? Y/N [Y]

   Choose a version
   0 ... 6.24.1801
   Input version index [0]:

   Deploy version 6.24.1801 to profile local
   Do you want to continue? Y/N [Y]
   Target installation path: C:\apps\gview-gis
   Repsitory path: C:\apps\gview-gis/local/gview-repository
   gView Server online url: http://localhost:5050
   gView Admin Username: admin
   gView Admin Password:
   gView User Username: carto
   gView Carto Password:

   Deploy version 6.24.1801


   **************************************

   Warning: version already deployed

   ***************************************

   Overrides
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\server\override\_config\mapserver.json
   ...succeeded 1 items created/overridden
   Copy C:\deploy\gview-gis\_deploy_repository\profiles\local\web\override\_config\gview-web.config
   ...succeeded 1 items created/overridden


Es erscheint die Warnung, dass diese Version bereits deployed wurde. Aus den ZIP Dateien werden keine 
Daten kopiert. Durchgeführt werden nur die *Overrides*

