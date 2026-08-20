.. _gview_server_cli:

gView.Server Kommandozeile (Offline-Modus)
===========================================

Neben dem normalen Serverbetrieb kann **gView.Server.exe** auch direkt mit Kommandozeilenparametern
aufgerufen werden, um Dienste zu verwalten, ohne dass der HTTP-Server gestartet wird. Das ist
insbesondere für *Offline*-Szenarien nützlich, etwa wenn Dienste im Rahmen eines Deployments oder
Skripts automatisiert veröffentlicht, entfernt oder abgefragt werden sollen.

Alle im Folgenden beschriebenen Aufrufe lesen die komplette Serverkonfiguration
(``_config/mapserver.json``, Plugins, Services-Pfad, siehe :ref:`config-server`) genauso ein wie
der normale Serverstart. Es wird dabei jedoch **kein** HTTP-Listener gestartet, d. h. es wird kein
Port gebunden. Der Prozess beendet sich nach der Aktion mit dem Exitcode ``0`` (Erfolg) bzw.
``!= 0`` (Fehler, die Meldung wird dabei auf ``stderr`` ausgegeben).

.. note::

   ``--service`` wird bei allen Befehlen im Format ``folder/servicename`` angegeben (bzw. nur
   ``servicename`` für einen Dienst im Root-Verzeichnis). Bei ``--remove``, ``--set-metadata`` und
   ``--get-metadata`` muss der angegebene ``folder`` bereits als existierendes Verzeichnis unter
   dem konfigurierten Services-Pfad vorhanden sein – dieselbe Regel gilt auch beim Publizieren
   über die Web-Oberfläche bzw. per HTTP.

--publish – Dienst veröffentlichen
-----------------------------------

.. code-block:: batch

   gView.Server.exe --publish --mxl <pfad-zur-mxl> --service <folder/servicename>

Validiert die angegebene MXL, benennt die erste darin enthaltene Karte auf
``folder/servicename`` um und schreibt ``.mxl`` und ``.meta`` in den konfigurierten Services-Pfad.

--remove – Dienst entfernen
------------------------------

.. code-block:: batch

   gView.Server.exe --remove --service <folder/servicename>

Löscht ``.mxl``, ``.svc`` und ``.meta`` des angegebenen Dienstes aus dem Services-Pfad.

--catalog – Dienste auflisten
--------------------------------

.. code-block:: batch

   gView.Server.exe --catalog [--format text|xml|json]

Listet alle registrierten Dienste auf (Root-Ebene sowie eine Ordnerebene darunter). Ohne
``--format`` wird die Ausgabe als Text erzeugt (``folder/name (Type)`` je Zeile).

--get-metadata – Metadaten auslesen
--------------------------------------

.. code-block:: batch

   gView.Server.exe --get-metadata --service <folder/servicename> [--out <pfad>]

Gibt die ``.meta``-XML des Dienstes aus. Mit ``--out`` wird die Ausgabe stattdessen in eine Datei
geschrieben. Ein leeres Ergebnis bedeutet, dass kein Dienst bzw. keine Metadaten vorhanden sind
(kein Fehler, Exitcode ``0``).

--set-metadata – Metadaten setzen
------------------------------------

.. code-block:: batch

   gView.Server.exe --set-metadata --service <folder/servicename> --metadata <pfad-zur-xml>

Schreibt die angegebene XML-Datei als ``.meta`` für den Dienst und lädt den Dienst anschließend
neu.
