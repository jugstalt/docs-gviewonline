Konfiguration
=============

Jede Anwendung (**gView.Web** und **gView.Server**) verfügt im ``_config`` Verzeichnis über Dateien,
um sie zu konfigurieren.

Es ist jedoch keine gute Praxis, die Dateien direkt dort zu verändern. Bei einem Update 
über das ``gview.deploy`` Werkzeug wird für jede neue Version ein neues Verzeichnis 
mit der Version als Name angelegt. Hat man die Konfiguration geändert, müsste man immer die
Konfigurationsdateien in die neue Version mitübernehmen.

Um das zu vermeiden, ändert man die Konfiguration im 
``_deploy_repository/profiles/{profile}/[web|server]/override`` Verzeichnis. 
Dieses befindet sich dort, wo auch das ``webgis.deploy`` Werkzeug liegt.


Ändert man die Dateien dort, werden sie bei jedem Update neu überspielt. Die dort 
abgelegten Dateien werden auch überspielt, wenn die Version, die deployed wird, bereits existiert.
In diesem Fall werden sogar ausschließlich nur die ``override`` Files publiziert.

.. note::

    Das Ändern der Konfigurationsfiles (``mapserver.json``, ``gview-web.config``) wirkt 
    sich erst aus, wenn die Anwendung neu gestartet wird.
    Bei IIS muss beispielsweise der entsprechende *Application Pool* neu gestartet werden.

Die Konfigurationsdateien in den ``override`` Verzeichnissen weisen Platzhalter auf, beispielsweise:

.. code-block:: javascript

    {
        "RepositoryPath": "{repository-path}/gview-web",
    }

Die Platzhalter entsprechen den Werten, die beim Anlegen des Profils im ``gview.deploy`` 
angegeben wurden. Aus Gründen der Konsistenz sollten diese Platzhalter auch beim 
Ändern der Konfiguration erhalten bleiben. ``{repository-path}`` kommt beispielsweise in 
mehreren Konfigurationsdateien vor (``mapserver.json``, ``gview-web.config``, ...). 
Möchte man das Wurzelverzeichnis für das *gView Repository* an einen anderen Ort verlegen,
muss man das für alle Konfigurationsdateien anpassen.

Beste Praxis ist es, die Werte in der Datei ``_deploy_repository/profiles/{profile}/deploy-model.json``
zu ändern:

.. code-block:: javascript

    {
        "ProfileName": "local",
        "TargetInstallationPath": "C:\\apps\\gview-gis",

        // Placeholder: {repository-path}
        "RepositoryPath": "C:\\apps\\gview-gis/local/gview-repository",
        
        // ...
    }

.. note::

    Die Namen der Eigenschaften sind hier nicht exakt gleich wie die Namen der Platzhalter.
    Die Eigenschaften sind hier in *PascalCase*, während die Platzhalter in der Regel in 
    *kebab-case* sind.
    Aus den Namen sollte jedoch ersichtlich sein, um welchen Platzhalter es sich handelt.


.. toctree::
   :maxdepth: 2

   config-web
   config-server


