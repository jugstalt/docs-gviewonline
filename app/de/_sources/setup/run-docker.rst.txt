Docker
======

Sowohl *gView.Server* als auch *gView.WebApps* stehen als fertige Docker-Images zur Verfügung.
Damit lassen sich die Anwendungen containerisiert betreiben, z. B. in Docker Compose oder
Kubernetes.

.. note::

    Die Images werden nicht mehr auf Docker Hub (``docker.io/gstalt/...``) veröffentlicht,
    sondern auf der GitHub Container Registry unter ``ghcr.io/jugstalt/...``. Verweise auf
    ``docker.io/gstalt/...`` sind veraltet und sollten nicht mehr verwendet werden.


gView.Server Image
-------------------

.. code-block:: bash

    docker pull ghcr.io/jugstalt/gview-server:latest

Der Container kann direkt gestartet werden:

.. code-block:: bash

    docker run -d -p 45622:8080 --name=gview-server \
        -e GV_REPOSITORY_PATH=/etc/gview-repository \
        -e GV_ONLINERESOURCE_URL=http://localhost:45622 \
        ghcr.io/jugstalt/gview-server:latest

Über die beiden Umgebungsvariablen lassen sich die wichtigsten Werte der ``_config/mapserver.json``
überschreiben, ohne die Datei selbst anzupassen:

* ``GV_REPOSITORY_PATH``: Verzeichnis, in dem Services, Client-Daten, Output-Dateien usw.
  abgelegt werden. Für den produktiven Einsatz sollte dieses Verzeichnis über ein Volume
  außerhalb des Containers gemountet werden, damit die Einstellungen einen Neustart des
  Containers überstehen.
* ``GV_ONLINERESOURCE_URL``: Die URL, unter der der Server von außen erreichbar ist. Diese wird
  z. B. verwendet, um Kartenbilder über das Output-Verzeichnis an Clients auszuliefern.

Nach dem Start ist die Verwaltungsoberfläche unter http://localhost:45622 erreichbar (im Beispiel
oben). Der erste Login-Vorgang entspricht dem unter :doc:`run-local` beschriebenen Ablauf: Der
erste angelegte Benutzer wird automatisch zum Administrator.


gView.WebApps Image
--------------------

.. code-block:: bash

    docker pull ghcr.io/jugstalt/gview-webapps:latest

.. code-block:: bash

    docker run -d -p 45623:8080 --name=gview-webapps ghcr.io/jugstalt/gview-webapps:latest

Die Anwendung ist danach unter http://localhost:45623 erreichbar.

.. note::

    Für das *gView.WebApps* Image sind aktuell keine speziellen Umgebungsvariablen dokumentiert.
    Die Konfiguration erfolgt wie bei den anderen Betriebsarten über ``_config/gview-web.config``
    (siehe :doc:`config-webapps`). Muss diese Datei angepasst werden, kann eine eigene Version
    per Volume in das Verzeichnis ``/app/_config`` des Containers gemountet werden.


Eigene Konfiguration einbinden
-------------------------------

Statt die Konfigurationsdateien im laufenden Container zu ändern, sollte eine angepasste
``mapserver.json`` bzw. ``gview-web.config`` als Volume in den Container gemountet werden — das
entspricht dem gleichen *Override*-Prinzip, das auch beim klassischen Deploy verwendet wird
(siehe :doc:`config`):

.. code-block:: bash

    docker run -d -p 45622:8080 --name=gview-server \
        -v /pfad/zu/meiner/mapserver.json:/app/_config/mapserver.json:ro \
        -e GV_REPOSITORY_PATH=/etc/gview-repository \
        -e GV_ONLINERESOURCE_URL=http://localhost:45622 \
        ghcr.io/jugstalt/gview-server:latest

.. note::

    Früher gab es dafür zusätzlich ein eigenes Basis-Image (``gview-server-base``), das als
    ``FROM``-Basis für ein eigenes Dockerfile diente, um Konfiguration und Fonts direkt in ein
    eigenes Image einzubacken. Dieses Basis-Image ist aktuell nicht unter ``ghcr.io/jugstalt``
    veröffentlicht. Für individuelle Konfiguration ist das Mounten eigener Dateien (siehe oben)
    der empfohlene Weg.
