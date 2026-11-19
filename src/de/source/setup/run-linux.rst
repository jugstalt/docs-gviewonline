Ausführen unter Linux
=====================

Wurde **gView** mit dem *Deploy Tool* installiert, muss in den entsprechenden Ordner gewechselt werden,
z.B. ``/home/{user}/apps/gview-gis/local/6.24.1801``.

Darin befinden sich folgende Dateien:

.. code-block:: bash

   myuser@JG-SP8:~/apps/gview-gis/local/6.24.2101$ ls -ls
   total 48
    4 -rwxr-xr-x  1 myuser myuser   119 May 22 08:29 gview-server.sh
    4 -rwxr-xr-x  1 myuser myuser   121 May 22 08:38 gview-webapps.sh
   20 drwxr-xr-x 55 myuser myuser 20480 May 22 10:44 server
   20 drwxr-xr-x  6 myuser myuser 20480 May 22 10:44 webapps

.. note::

    Falls die Shell-Scripts nicht als *executable* markiert sind, kann das über 
    folgendes Kommando nachgeholt werden:

    .. code-block:: bash

        sudo chmod +x gview-server.sh
        sudo chmod +x gview-webapps.sh

Die beiden Scripts starten jeweils den **gView.Server** oder **gView.WebApps**. 

.. code-block:: bash

    ./gview-server.sh
    ./gview-webapps.sh

In der Ausgabe wird angezeigt, unter welchem Port die Anwendungen laufen, in der Regel:

* gView.Server: http://localhost:45622
* gView.WebApps: http://localhost:45623

Die Anwendungen können jetzt über einen Browser mit der entsprechenden URL gestartet werden.

Möchte man die Anwendungen über das Web zugänglich machen, kann das über einen vorgeschalteten 
**NGINX** als Upstream ermöglicht werden.
