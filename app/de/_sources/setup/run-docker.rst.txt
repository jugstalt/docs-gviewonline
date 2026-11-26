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
oben). Den ersten Administrator legt man über die Umgebungsvariablen ``GVIEW_ADMIN_USER`` und
``GVIEW_ADMIN_PASSWORD`` an oder mit dem Setup-Code, den der Server ins Log schreibt
(``docker logs gview-server``), siehe :doc:`security-server`. Hat ein Admin Telefon und
Recovery-Codes der Zwei-Faktor-Anmeldung verloren, setzt ``GVIEW_ADMIN_RESET_2FA=<user>`` sie
beim Start zurück (danach wieder entfernen).


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


Benutzer und Volumes
--------------------

Die Images laufen nicht als ``root``, sondern als User ``app`` (UID 1654) des .NET-Basis-Images.
Alles, was die Anwendung zur Laufzeit schreibt, muss für diesen User schreibbar sein:

* ``/app/_config``: Konfiguration, wird beim ersten Start aus ``_setup`` erzeugt, wenn keine
  eigene gemountet ist.
* ``/etc/gview/gview-repository`` (Standard für ``GV_REPOSITORY_PATH``) bzw.
  ``/etc/gview-repository`` (wie in den Beispielen hier): Services, Logins, Logs, Output-Dateien,
  Tile-Caches.

Im Image gehören diese Verzeichnisse bereits dem User ``app``, Docker-Volumes übernehmen das. Bei
Host-Verzeichnissen (Bind-Mounts) muss der Besitzer passen:

.. code-block:: bash

    sudo chown -R 1654:1654 /pfad/zu/gview-repository

    docker run -d -p 45622:8080 --name=gview-server         -v /pfad/zu/gview-repository:/etc/gview-repository         -e GV_REPOSITORY_PATH=/etc/gview-repository         -e GV_ONLINERESOURCE_URL=http://localhost:45622         ghcr.io/jugstalt/gview-server:latest

In Kubernetes passt das zu ``runAsNonRoot: true`` und ``runAsUser: 1654`` (siehe
:doc:`run-kubernetes`).

.. note::

    Bis v8 liefen die Images als ``root``. Beim Umstieg auf v9 müssen bestehende Host-Verzeichnisse
    für UID 1654 schreibbar gemacht werden, sonst kann der Server dort nichts mehr anlegen.


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
