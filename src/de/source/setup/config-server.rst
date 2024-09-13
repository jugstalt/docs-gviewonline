.. _config-server:

Konfiguration gView.Server
==========================

Der **gView.Server** kann über die Datei ``_config/mapserver.json`` konfiguriert werden:


.. code-block:: javascript

   {
        // Folder, where gView Server stores Services, Logins, etc
        "services-folder": "{repository-path}/server/configuration",

        // Path, where gView Server stores Map Request Images
        "output-path": "{repository-path}/server/web/output",
        // Url to the Path, where gView Server stores Map Request Images
        "output-url": "{server-url}/output",

        // Path, where gView Server stores Tiles
        "tilecache-root": "{repository-path}/server/web/tile-caches",

        // The Task Queue
        "task-queue": {
            // Indicates how many requests can be processed at the same time
            "max-parallel-tasks": 50,
            // The maximum length of the queue
            "max-queue-length": 1000
        },

        // default value for mapserver and services
        "mapserver-defaults": {
            // maximum width in pixel of an map image
            "maxImageWidth": 4096,
            // maximum height in pixel of an map image
            "maxImageHeight": 4096,
            // maximum records that will be returned from a query
            "maxRecordCount": 1000
        },

        // Whether clients are allowed to log in through the web interface
        "allowFormsLogin": true,
        // It can be assumed that all calls are made over HTTPS
        "force-https":  false,

        // optional: graphic engines. gdiplus only works on windows systems
        "graphics": {
            "rendering": "skia",  // engine for rendering: skia|gdiplus 
            "encoding": "skia"  // engine for encoding images (jpg|png): skia|gdiplus 
        },


        // Optional Settings
        "Facilities": {
            "MessageQueue": {
                "Type": "messagequeue-net",
                "ConnectionString": "http://localhost:9091",
                "Namespace": "development"
            }
        }
    }

Im ersten Abschnitt werden Pfade und URLs definiert. Diese werden im ersten Schritt über das ``gview.deploy``
Tool angelegt und können hier noch einfach angepasst werden.

Unter ``task-queue`` kann angegeben werden, wie viele Requests gleichzeitig abgearbeitet werden können.
Wenn ausreichend RAM zur Verfügung steht, kann dieser Wert beliebig erhöht werden. Man muss jedoch bedenken,
dass der Server Kartenbilder (Bitmaps) erzeugt, die einiges an RAM benötigen können. Ist man mit der 
RAM-Auslastung immer wieder am Limit (aufgrund vieler Anfragen), kann der Wert auch niedriger angesetzt werden.
Sind alle Tasks belegt, kommt ein Request in die Warteschleife. Die Länge der Warteschleife kann hier ebenfalls
angepasst werden.

Im Abschnitt ``mapserver-defaults`` können *Default-Werte* für Kartendienste angegeben werden.
Werden für einen Dienst diese Werte nicht explizit gesetzt, wird der hier eingestellte
*Default-Wert* verwendet. Dabei geht es um die maximale Größe eines Kartenbildes in Pixel,
das von einem Client abgeholt werden kann. Ebenfalls kann hier angegeben werden, wie viele 
Geo-Objekte bei einer Abfrage maximal abgeholt werden dürfen. Wird der Abschnitt oder einzelne 
Werte nicht angeführt, gelten die oben angeführten Werte.


.. note::

    Die hier angegebenen Werte sind *Default-Werte* und können für jedes Service über die 
    *Map Service Einstellung* in *gView.Carto* überschrieben werden. Es können dort für 
    einzelne Dienste auch Werte angeführt werden, die höher sind als die *Default-Werte*.
    Die Werte in dieser Konfiguration sind keine *globalen Maximalwerte*.

.. note::

    Ein Client kann ein Kartenbild anfordern, das größer ist als die Maximalwerte. Der Kartenserver
    liefert dann keinen Fehler, sondern ein verkleinertes Bild, das den Maximalwerten 
    entspricht. Das Seitenverhältnis und der geografische Ausschnitt bleiben erhalten. Allerdings 
    wird dann automatisch die Auflösung des Bildes (DPI-Wert) angepasst, damit der Maßstab 
    dem ursprünglich bestellten Kartenbild entspricht.

    Ein Client kann überprüfen, ob das Bild skaliert wurde, indem die Größe des Ergebnisbildes 
    geprüft wird. Die Werte stehen beim *GeoServices Response* auch im Ergebnis-JSON.

Im Abschnitt ``graphics`` kann die *Graphic Engine* angegeben werden. Diese kann entweder ``skia`` oder
``gdiplus`` (auf Windows-Plattformen) sein. ``gdiplus`` ist allerdings ein Auslaufmodell und 
funktioniert nur, wenn die Anwendung auf einem Windows-System ausgeführt wird.

Unter ``Facilities`` können weitere optionale Einstellungen vorgenommen werden.

Eine Option ist beispielsweise ``MessageQueue``. Dort kann bisher nur eine MessageQueue vom 
Typ ``messagequeue-net`` angegeben werden (https://github.com/jugstalt/messagequeuenet).
Eine MessageQueue ist sinnvoll, wenn der *gView.Server* in mehreren Instanzen gleichzeitig ausgeführt wird,
beispielsweise wenn *gView.Server* in Kubernetes mit mehreren *Replicas* läuft.
Über die MessageQueue können die einzelnen Komponenten miteinander kommunizieren, wenn beispielsweise 
ein Kartendienst geändert oder gelöscht wird. So bleiben alle Instanzen auf dem aktuellen Stand.
Mit ``Namespace`` kann hier ein Namensraum angegeben werden, der definiert, welche Instanzen zusammengehören.
Da beispielsweise sowohl Instanzen aus ``Staging`` als auch ``Production`` auf dieselbe 
MessageQueue-API zugreifen können, kann über den Namensraum unterschieden werden, welche Instanzen wirklich 
zusammengehören.





