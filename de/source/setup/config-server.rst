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

Im ersten Abschnitt werden Pfade und Urls definiert. Diese werden im ersten Schritt über das ``gview.deploy``
Tool angelegt und können hier noch einfach angepasst werden.

Unter ``task-queue`` kann angeben werden, wie viele Requests gleichzeitig abgearbeitet werden können.
Wenn ausreichend RAM zur Verfügung steht kann dieser Wert beliebig erhöht werden. Bedenken muss man,
dass der Server Kartenbilder (Bitmaps) erzeugt, die einiges an RAM benötigen können. Ist man mit der 
RAM Auslastung immer wieder am Limit (aufgrund vieler Anfragen), kann man Wert auch niedriger ansetzten.
Sind alle Tasks belegt, kommt ein Request in die Warteschleife. Die Länge der Warteschleife kann hier ebenfalls
angepasst werden.

Im Abschnitt ``graphics`` kann die *Graphic Engine* angegeben werden. Die kann entweder ``skia`` oder
``gdiplus`` (auf Windows Plattformen) lauten. ``gdiplus`` ist allerdings ein Auslaufmodell und 
funktioniert nur, wenn die Anwendung auf einen Windowssystem ausgeführt wird.

Unter ``Facilities`` können weiter optionale Einstellungen getroffen werden.

Eine Options ist beispielsweise ``MessageQueue``. Dort kann bisher nur eine MessageQueue vom 
Type ``messagequeue-net`` angeben werden (https://github.com/jugstalt/messagequeuenet).
Eine MessageQueue macht Sinn, wenn der *gView.Server* in mehreren Instanzen gleichzeitig ausgeführt wird.
Beispielsweise, wenn *gView.Server* in Kubernetes mit mehren *Replicas* läuft.
Über die MessageQueue können die einzelnen Komponenten miteinander kommunizieren, wenn beispielsweise 
eine Kartendienst geändert oder gelöscht wird. So bleiben alle Instanzen auf dem aktuellen Stand.
Mit ``Namespace`` kann hier ein Namensraum angegeben werden, der definiert, welche Instanzen zusammen 
gehören. Da beispielsweise sowohl Instanzen aus ``Staging`` und ``Production`` auf die selbe 
MessageQueue API zugreifen, kann über den Namensraum unterschieden werden, welche Instanzen wirklich 
zusammen gehören.




