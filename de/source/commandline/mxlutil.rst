gView.Cmd.MxlUtil
=================

Dieses Werkzeug stellt einige Methoden zur Verfügung, um wiederkehrende Aufgaben im Umfeld mit 
Map-Projektdateien (MXL) zu automatisieren:

* **PublishService**: Eine Karte im *gView MapServer* veröffentlichen.
* **MxlDataset**: Anzeigen und Anpassen von Verbindungseigenschaften einer MXL-Datei.
* **MxlToFdb**: Kopieren aller Vektordaten eines Kartenprojekts in eine *gView Feature Database*. 
  Das *gView FDB*-Format garantiert eine hohe Performance. Die Datenbank wird hier in *SQL Server*, 
  *PostGre* oder *Sqlite* angelegt.
  Dieses Werkzeug kann verwendet werden, um Kartenprojekte *offline* verfügbar zu machen 
  (alle Daten in einer *Sqlite*-Datenbank).

.. note::

    Die folgenden Befehle werden im **interaktiven Modus** gezeigt. Dazu muss ``gView.Cmd.exe -i``
    aufgerufen werden. Ohne den *interaktiven Modus* müsste jedem hier gezeigten Befehl noch
    ``gView.Cmd.exe --command`` vorangestellt werden.

Der allgemeine Aufruf für dieses Werkzeug lautet:

.. code-block:: batch

   Command:>MxlUtil -u <utiltiy-name> [parameters]

Als ``utility-name`` kann eines der hier vorgestellten Optionen verwendet werden.

PublishService
--------------

Zum Publizieren von Kartendiensten können folgende Parameter übergeben werden:

.. code-block:: batch

  Command:>MxlUtil -u publishservice

  PublishService
  --------------

  Publish Mxl as gView Map Service

  Required arguments:
  -mxl <mxl-file>
  -server <gview-server-url>
  -service <servicename incl. folder ... folder/servicename>

  Optional arguments:
  -client <clientname>
  -secret <clientsecret>

* ``-mxl``: Pfad zum XML File
* ``-server``: Url der gView Instanz, über die der Dienst publiziert werden sollte
* ``-service``: Name des Services: ``{Verzeichnis}``/``{Dienstname}``

Beispiel:

.. code-block:: batch

    Command:>MxlUtil -u publishservice -mxl c:\my-map.mxl -server https://localhost -service produktiv/my-service

Über die optionalen Parameter kann ein *Client* übergeben werden, über den der Dienst publiziert 
werden sollte. In der Regel sollte man nicht jedem Benutzer erlauben, Dienste zu publizieren. Um das zu 
steuern, kann der Administrator/Manager des *gView MapServer* Clients anlegen (in der Manager 
Weboberfläche unter ``Security``). Für diese Clients können auf Service- und Verzeichnisebene Rechte 
vergeben werden (Schloss-Symbol). Clients, die dort das Recht ``publish`` für ein Verzeichnis bekommen, 
dürfen Dienste publizieren.
 
.. note::
   Werden für ein Verzeichnis keine Rechte eingestellt, werden jedem Anwender alle Rechte zugeteilt.

.. code-block:: batch

    Command:>MxlUtil -u publishservice -mxl c:\my-map.mxl -server https://localhost -service produktiv/my-service -client publisher -secret pa3sw0rd

.. note::
   Über die Kommandozeile kann *nur* ein gültiger *Client* angegeben werden. Hier kann nicht der 
   Administrator übergeben werden. Möchte man über die Kommandozeile *sicher* Dienste publizieren, 
   muss ein Client angelegt werden!

MxlDatasets
-----------

Hier können die Verbindungsparameter der Datasets innerhalb einer MXL-Datei angezeigt oder verändert 
werden. Ein Anwendungsfall kann sein, wenn Karten mit Verbindungen zu einer (lokalen) Testdatenbank 
entwickelt werden. Vor dem (automatisierten) Veröffentlichen können hier die Verbindungseigenschaften 
auf eine produktive Datenbank umgeschrieben werden.

Aufruf:

.. code-block:: batch
  
   Command:>MxlUtil -u mxldatasets
    
   Required arguments:
   -mxl <mxl-file>
   -cmd <info|modify-cs|modify-connectionstring>

   Optional arguments:
   -out-xml <name/path of the out xml>

   Commands:
   info
   ----
   Shows all dataset connections and connection string parameters in the mxl file.

   modify-cs|modify-connectionstring
   ---------------------------------
   Changes the value of an parameter in a connection  in the mxl file.

   Required arguments:
   -parameter|-parameter-name <name of the parameter>
   -new-value|-new-parameter-value <set this value for the parameter>

   Optional arguments:
   -dsindex|-dataset-index <the index of the dataset you want change the parameter> default = -1 => all datasets 


* ``-mxl``: Pfad zum MXL-File
* ``-cmd``: Weitere Spezifikation des Commands, das für die Dataset-Verbindungen ausgeführt werden soll.

Optional:

* ``-mxl-out``: Pfad zu einem MXL, das erstellt werden sollte (nur bei ``modify-connectionstring``).
  Wird kein Output XML angegeben, wird die ursprüngliche Datei überschrieben.
    
**Command - Info**

Dieses Kommando ist der Standard. Wird dieses oder kein Kommando übergeben, werden die 
Verbindungsparameter der einzelnen Datasets angezeigt:

.. code-block:: batch

   Command:>MxlUtil -u mxldatasets -mxl C:\gview5\mxl\my-map.mxl
    
   Dataset 0
   ==============================================================================
   Type: gView.DataSources.MSSqlSpatial.DataSources.Sde.SdeDataset


   ConnectionString:
   ------------------------------------------------------------------------------
   Server=testdbserver
   Database=gisdb
   User Id=DB_READ
   Password=*************

In der Auflistung der Datasets bekommt jedes Dataset eine Nummer (hier ``0``). Möchte man später nur einen Parameter für ein bestimmtes 
Dataset ändern, muss dies mit dem Parameter ``-dsindex`` (siehe unten) angeführt werden.

**Command - modify-cs|modify-connectionstring**

Mit diesem Kommando können einzelne *Connection Parameter* geändert werden. Zusätzlich zu den oben angeführten Parametern müssen dazu noch folgende 
Parameter übergeben werden:

* ``-parameter|-parameter-name``: Der Name des Parameters (z.B. ``server``), der geändert werden soll.
* ``-new-value|-new-parameter-value``: Der neue Wert für den Parameter.

Sollten mehrere Parameter geändert werden, müssen die Parameter in der Kommandozeile wiederholt werden.

Optional:

* ``-dsindex|-dataset-index``: Sollte nur ein spezielles Dataset geändert werden, kann hier die Indexnummer des Datasets angegeben werden.
  Die Indexnummer kann aus dem oben gezeigten ``info`` Kommando entnommen werden. Wird der Parameter nicht angegeben, werden die Parameter für 
  alle Datasets geändert.

Beispiel:

.. code-block:: batch

    Command:>MxlUtil -u mxldatasets -mxl C:\gview5\mxl\my-map.mxl -cmd modify-cs -parameter Server -new-value proddbserver -parameter password -new-value ProdPa3sw0rd -out-mxl C:\gview5\mxl\my-map-produktiv.mxl


.. note::
   Die Verbindungsparameter werden sowohl beim Öffnen als auch beim Überschreiben überprüft. Ist mit den gegebenen Parametern keine Verbindung möglich,
   bricht das Programm ab.
   Ist eine MXL beschädigt und es kann mit den Verbindungsparametern keine Verbindung aufgebaut werden, kann dieses Werkzeug nicht verwendet werden.
   In diesem Fall muss das MXL-File über einen Texteditor repariert werden.

MxlToFdb
--------

Kopieren aller Vektordaten eines Kartenprojektes in eine *gView Feature Database*. *gView FDB* ist ein Format, für das von *gView* eine hohe Performance garantiert wird. Die Datenbanken werden hier in *SQL Server*, *PostGre* oder *Sqlite* angelegt.
Dieses Werkzeug kann verwendet werden, um Kartenprojekte *offline* zur Verfügung zu stellen (alle Daten in einer *Sqlite* Datenbank)

.. code-block:: batch

   Command:>MxlUtil -u mxltofdb

   MxlToFdb
   --------

   Copies all vector data in an MXL file to an FeatureDatabase (fdb) [SqlServer, PostGres or Sqlite).
   The result is a new MXL file with the same symbology in changed connections to the new FeatureDatabase.

   Example: Use this utitiity to make an existing database driven MXL to an 'offline' file driven (Sqlite)
   MXL.

   Required arguments:
   -mxl <mxl-file>
   -target-connectionstring <target fdb connection string>
   -target-guid <guid or sqlserver|postgres|sqlite>

   Optional arguments:
   -out-xml <name/path of the out xml>
   -dont-copy-features-from <a comma seperated list of layernames, where only an empty Db-Table-Schema is created>


* ``-mxl``: Pfad zum XML-File
* ``-target-connectionstring``: Connection String zur Ziel-Feature-Database
* ``-target-guid``: GUID des Ziel-Datenbank-Plugins oder einfach ``sqlserver|postgres|sqlite``

Optional:

* ``-mxl-out``: Pfad zu einer MXL, die erstellt werden sollte. Wird kein Output-XML angegeben, 
  wird die ursprüngliche Datei überschrieben.
* ``--dont-copy-features-from``: Eine Liste von Layern, die nicht kopiert werden sollten. Das Tool 
  wird hauptsächlich dazu verwendet, bestehende Karten *offline* 
  fähig zu machen, indem die Daten in eine SQLite-Datenbank geschrieben werden. Wenn (große) 
  Datensätze einer Karte *offline* nicht zwingend notwendig sind, können sie hier angegeben werden. 
  In der Zieldatenbank wird zwar das Schema dieser Tabellen angelegt, jedoch werden keine Daten kopiert.

Beispiel:

.. code-block:: batch

   Command:>MxlUtil -u mxltofdb -mxl C:\gview5\mxl\my-map.mxl -target-connectionstring: c:\offline.fdb -target-guid sqlite -out-mxl C:\gview5\mxl\my-map-offline.mxl -dont-copy-features-from bigdata-layer1,bigdata-layer2

