Konfiguration
=============

Der gView Server kann über die Datei _config/mapserver.json konfiguriert werden:

.. code-block:: javascript

   {
        // Folder, where gView Server stores Services, Logins, etc
        "services-folder": "C:\\gView5\\Server\\Services",

        // Path, where gView Server stores Map Request Images
        "output-path": "C:\\IIS\\Output",
        // Url to the Path, where gView Server stores Map Request Images
        "output-url": "http://my.server.com/output",

        // Url with which gView Server can be reached via Internet
        "onlineresource-url": "https://my.server.com/gview5-server",

        // Path, where gView Server stores Tiles
        "tilecache-root": "C:\\data\\tilecache",

        // The Task Queue
        "task-queue": {
            // Indicates how many requests can be processed at the same time
            "max-parallel-tasks": 5,
            // The maximum length of the queue
            "max-queue-length": 1000
        },

        // Whether clients are allowed to log in through the web interface
        "allowFormsLogin": true,
        // It can be assumed that all calls are made over HTTPS
        "force-https":  false
    }


Startet man den Viewer wie in der Installation beschrieben vorgangsweise im *Standalone* Modus, wird diese Datei, falls noch nicht
vorhanden beim ersten Start automatisch erzeugt. Die Ausgabeverzeichnisse werden dabei in den Ordner ``server-files`` parallel zum
Ordner ``server`` gelegt.

Ausgangspunkt für die Standardkonfiguration ist die Datei ``_config/_mapserver.json`` die im Installationspaket mitgeliefert wird.
Dabei handelt es sich um eine Vorlagendatei mit Platzhaltern (``{installation-path}`` und ``{installation-host}``). Die Platzhalter
werden beim ersten Start ersetzt und daraus eine ``_config/mapserver.json`` Datei erzeugt.

Dieser Mechanismus kann auch unter Linux bzw. innerhalb von *Docker Containern* verwendet werden. Dafür müssen nur die entsprechenden
Umgebungsvariablen gesetzt werden (``GV_REPOSITORY_PATH`` und ``GV_ONLINERESOURCE_URL``). Ein fertiges Image und eine 
Beschreibung dazu gibt es unter https://hub.docker.com/r/gstalt/gview5-server

.. note::
   Im Output Verzeichnis werden Kartenbilder abgelegt und dann vom Client dort abgeholt. Der *gView Server* muss schriebrechte
   für dieses Verzeichnis besitzen. Der Client muss die hier angegebene Url erreichen. Als Server, der die Bilder ausliefert,
   kann jeder beliege Http-Server verwendet werden (IIS, Nginx, ...). Es besteht auch die Möglichkeit *gView Server* zum 
   Ausliefern der Kartenbilder zu veranlassen. Dazu muss (wie im der Standardkonfiguration) als ``output-url`` die Url zum *gView MapServer*
   plus ``/output`` angeben werden, zB: ``https://gview.my-server.com/output`` oder ``https://www.my-server.com/gview5/output``

