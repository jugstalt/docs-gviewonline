.. _config_webapps:

Konfiguration gView.WebApps
===========================

Die *gView.WebApps* Anwendung kann über die Datei ``_config/gview-webapps.config`` konfiguriert werden:

.. code-block:: javascript

    {
    "RepositoryPath": "{repository-path}/gview-web",
        "Authentication": {
            // depends on your authentication provider
        },
        "Drives": {
            "TILE_CACHES": "{repository-path}/server/web/tile-caches",
            "USER": "{repository-path}/gview-web/users/{{username}}"
        },
        "CustomTiles": [
            {
                "Title": "Local gView Server",
                "Description": "gView Server in development environment",
                "TargetUrl": "https://localhost:44331",
                "Visibility": "admin"  // optional: admin (default), user, public
            }
        ],
        "Home": {  // optional: start page
            "Title": "GIS Portal",
            "WelcomeText": "Welcome to the GIS portal.\nSign in with your company account.",
            "LogoUrl": "https://my-server/logo.png",
            "NoAccessText": "Please ask the GIS team for access: gis@my-company.com",
            "Links": [
                { "Title": "Manual", "Url": "https://my-server/gis-manual" }
            ]
        },
        "Publish": {  // optional
            "Servers":
            [
                {
                    "Name": "localhost",
                    "Url": "https://localhost:44331",
                    "Client": "carto-publish",  // optional
                    "Secret": "008bHbuWQx7JAY5lxOWnNWqm67L"  // optional: if you dont set, user is ask on publish 
                }
            ]
        }
    }       

Benutzerdefinierte Laufwerke/Verzeichnisse
------------------------------------------

Im Abschnitt ``Drives`` können Pfade festgelegt werden, die im **gView.DataExplorer** unter 
``Computer`` aufgelistet werden. Wird dieser Abschnitt weggelassen, werden alle verfügbaren 
Laufwerke angezeigt.

.. note::

    Die Pfade und Laufwerke beziehen sich auf den Computer/Server, auf dem **gView.WebApps** 
    installiert ist.

.. note::

    Wenn **gView.WebApps** auf einem Server installiert ist, wird **dringend** empfohlen, diesen 
    Abschnitt zu pflegen. Aus Sicherheitsgründen darf und soll man hier nicht auf das komplette 
    Dateisystem zugreifen können. Ein potentieller Angreifer hätte sonst theoretisch Zugriff auf 
    alle Dateien des Servers.

Innerhalb eines Pfades kann ein Platzhalter ``{{username}}`` verwendet werden. Dieser wird mit dem 
Usernamen des aktuell bei **gView.WebApps** angemeldeten Benutzers ersetzt. So kann ein 
Anwender beispielsweise MXD-Dateien in einem *privaten* Verzeichnis ablegen.

Benutzerdefinierte Kacheln
--------------------------

Im Abschnitt ``CustomTiles`` können beliebige weitere Kacheln angelegt werden, die auf der 
Startseite von **gView.WebApps** angezeigt werden. Damit können beispielsweise Kacheln zu 
einer oder mehreren **gView.Server** Instanzen angelegt werden.

Jede Kachel hat folgende Eigenschaften:

* **Title**, **Description:** Titel und Beschreibung der Kachel.
* **TargetUrl:** Die URL, die beim Klick auf die Kachel geöffnet wird.
* **BackgroundColor**, **FontColor:** optional, Hintergrund- und Schriftfarbe der Kachel
  (z. B. ``#0f766e``).
* **Visibility:** optional, wer die Kachel sieht:

  * ``admin`` (Standard): nur *Admin-User*
  * ``user``: alle angemeldeten Benutzer mit einer *gView* Rolle (*Carto-User* und *Admin-User*)
  * ``public``: alle, auch Besucher, die nicht angemeldet sind

.. _config_webapps_home:

Startseite
----------

Wer nicht angemeldet ist, sieht auf der Startseite einen **Sign in** Button und die Anwendungen
*gView Carto* und *gView Explorer* als gesperrte Kacheln. Ein Klick auf eine gesperrte Kachel
führt zur Anmeldung und danach direkt in die Anwendung. Angemeldete Benutzer ohne *gView* Rolle
(z. B. bei ``oidc`` ein Benutzer ohne ``RequiredUserRole`` und ``RequiredAdminRole``) bekommen
einen Hinweis, dass ihnen die Berechtigung fehlt.

Über den optionalen Abschnitt ``Home`` kann die Startseite an die eigene Organisation angepasst
werden:

* **Title:** Überschrift der Startseite (Standard: ``gView GIS``).
* **WelcomeText:** Willkommenstext unter der Überschrift. Reiner Text, Zeilenumbrüche
  (``\n``) werden übernommen. Hier kann z. B. stehen, wie man sich anmeldet.
* **LogoUrl:** URL zu einem Logo, das über der Überschrift angezeigt wird.
* **NoAccessText:** Text für angemeldete Benutzer ohne *gView* Rolle, z. B. an wen sie sich
  wenden können (Standard: *Please contact your administrator.*).
* **Links:** Links in der Fußzeile, jeweils mit ``Title`` und ``Url``. Werden Links angegeben,
  ersetzen sie die Standard-Links (Dokumentation, GitHub).

.. code-block:: javascript

    "Home": {
        "Title": "GIS Stadtwerke Musterstadt",
        "WelcomeText": "Willkommen im GIS-Portal der Stadtwerke.\nMelde dich mit deinem Firmenkonto an.",
        "LogoUrl": "https://my-server/logo.png",
        "NoAccessText": "Bitte beim GIS-Team anfragen: gis@stadtwerke.example",
        "Links": [
            { "Title": "GIS-Handbuch", "Url": "https://my-server/gis-handbuch" }
        ]
    }

.. note::

    Die festen Texte der Startseite sind englisch. Texte in der eigenen Sprache können über
    ``Title``, ``WelcomeText`` und ``NoAccessText`` angegeben werden.

.. note::

    Ist keine Authentifizierung konfiguriert (siehe unten), sieht jeder Benutzer die Startseite
    als *Admin-User*. Wird **gView.WebApps** dann nicht über ``localhost`` aufgerufen, zeigt die
    Startseite im Bereich **System** eine Warnung.

Karten Veröffentlichen
----------------------

Über den Abschnitt `Publish` kann angegeben werden, auf welche **gView.Server** Instanzen
Karten direkt publiziert werden können. Unter `Servers` können hier mehrere Instanzen definiert werden:

* **Name:** Ein beliebiger Name, der die entsprechende Instanz beschreibt.

* **Url:** Die URL zur **gView.Server** Instanz.

* **Client:** Zum Veröffentlichen von Diensten ist ein *Client* notwendig. Hier wird der Name des 
  *Clients* angegeben. Diese Option kann auch weggelassen werden. Der Anwender muss dann beim 
  Veröffentlichen den *Client* manuell eintragen.

* **Secret:** Das *Secret* für den *Client*. Dieser Parameter ist ebenfalls optional. Wird hier nichts 
  angegeben, muss der Anwender das *Secret* beim Veröffentlichen eingeben.

.. note::

    Wird ein *Secret* angegeben, muss dieses in der `gview-webapps.config` im Klartext angegeben werden.
    Um das zu vermeiden, ist es gute Praxis, nur den *Client* anzugeben und das *Secret* wegzulassen.
    Der Anwender muss dann das *Secret* kennen und beim Veröffentlichen angeben.

.. note::

    Um später Dienste über **gView.Carto** zu veröffentlichen, muss man als **admin** User angemeldet 
    sein. **carto** Usern wird das `Publish Map` Werkzeug nicht angeboten.  

Um Veröffentlichen zu können, sind weitere Konfigurationen des **gView.Server** notwendig.
Es wird empfohlen, diesen Abschnitt erst zu konfigurieren, wenn auch die folgenden Abschnitte 
gelesen wurden:

:ref:`publish-map-service-example`

:ref:`config-server`


Authentifizierung
-----------------

Um zu bestimmen, wie man sich bei **gView.WebApps** anmelden kann/muss, dient der Abschnitt 
``Authentication``.

.. note::

    Wird der Abschnitt weggelassen, findet keine Authentifizierung statt. Jeder Benutzer darf 
    alles. Dies sollte jedoch nur für lokale Installationen möglich sein.
    Läuft **gView.WebApps** auf einem Server, sollte **unbedingt** eine Authentifizierungsmethode
    eingestellt werden.

**gView.WebApps** unterscheidet zwei Kategorien von Benutzern:

* **Admin-User:** Benutzer, die alle Anwendungen und Werkzeuge verwenden dürfen.
* **Carto-User:** Benutzer, die nur die *Carto* Anwendung benutzen dürfen. Diese Anwender
  können nur Karten erstellen und speichern. Sie können auf vordefinierte Datenbankverbindungen
  zugreifen, um Geodaten in die Karte einzubinden. Allerdings können sie den *Connection String*
  für diese nicht einsehen oder ändern (im Gegensatz zu *Admin-Usern*). 

Derzeit sind folgende Authentifizierungsmethoden möglich:

* ``forms``: Benutzer können sich mit Benutzername/Passwort über eine Login-Maske
  anmelden. Welche Benutzer es gibt, wird direkt in der ``gview-web.config`` eingestellt.
  Dies ist eine einfach zu pflegende Methode der Authentifizierung, die 
  für kleine Teams oft ausreicht. Diese Methode bietet keine fortgeschrittenen Sicherheitsrichtlinien
  und sollte maximal für Anwendungen im Intranet oder abgegrenzten Bereichen verwendet werden.
  
  Eine Bereitstellung von **gView.WebApps** über das Internet sollte mit dieser Methode nicht
  erfolgen.

* ``oidc``: OpenID Connect ist eine weitere Methode der Authentifizierung. Dabei meldet 
  sich der Anwender über einen externen Authentifizierungsdienst an. Dieser bietet in der 
  Regel höhere Sicherheit und Zwei-Faktor-Authentifizierung, etc.

Forms Authentifizierung
+++++++++++++++++++++++

Hier wird als ``Type`` der Wert ``forms`` angegeben. In der Sektion ``Forms`` können dann 
mehrere ``AdminUsers`` und ``CartoUsers`` angegeben werden, jeweils als Array von Objekten
mit den Eigenschaften ``Username`` und ``PasswordHash``.
Damit das Passwort hier nicht im Klartext angegeben werden muss, wird stattdessen ein 
Hashwert im Hexadezimalformat angegeben. Dieser Hash kann mit dem 
``SHA256``- oder ``SHA512``-Algorithmus erstellt worden sein.

.. note::

    Es gibt Online-Tools, mit denen diese Hashwerte berechnet werden können, z.B.:

    * https://coding.tools/sha256
    * https://coding.tools/sha512

.. code-block:: javascript

    "Authentication": {
            "Type": "forms",
            "Forms": {
                "AdminUsers": [
                    {
                        "Username": "admin",
                        "PasswordHash": "B109F3BBBC244EB82441917ED06D6...."
                    }
                ],
                "CartoUsers": [
                    {
                        "Username": "carto",
                        "PasswordHash": "5E884898DA28047151D0E56F8DC629277360..."
                    }
                ]
            }
        }


OpenID Connect Authentifizierung
++++++++++++++++++++++++++++++++

Steht ein *Identity-Dienst* zur Verfügung, der *OpenID Connect* unterstützt, kann dieser für
die Authentifizierung verwendet werden.

Als ``Type`` muss hier der Wert ``oidc`` eingetragen werden. Im Abschnitt ``Oidc`` muss
der *Identity Server* (``Authority``) angegeben werden. Am *Identity Server* muss 
*gView.WebApps* als Client hinzugefügt werden. Die entsprechende ``ClientId`` und das 
``ClientSecret`` sind ebenfalls hier einzutragen. Als ``Scopes`` werden die folgenden
Werte empfohlen:

.. code-block:: javascript

     "Authentication": {
            "Type": "oidc",
            "RequiredUserRole": "gview-web-user",
            "RequiredAdminRole": "gview-web-admin",
            "Oidc": {
                "Authority": "https://my-identity-server",
                "ClientId": "client-id-for-gview-web",
                "ClientSecret": "passW0rd",
                "scopes":["openid", "profile", "role"]
            }
        }

Da der *Identity Server* auch Rollen mitliefert, muss jeweils eine Rolle für 
**Admin-User** und **Carto-User** angegeben werden. Das erfolgt über die Parameter 
``RequiredUserRole`` (für Carto-User) und ``RequiredAdminRole`` (für Admin-User).

Raster-Datenquelle RasterNet
----------------------------

Auch in ``_config/gview-webapps.config`` kann ein Abschnitt ``rasternet`` angegeben werden. Er gilt für
*gView.DataExplorer* und *gView.Carto* und hat dieselben Einträge wie beim *gView.Server*
(siehe :ref:`Konfiguration gView.Server, Abschnitt rasternet <config-rasternet>`):

.. code-block:: javascript

    "rasternet": {
        "blockcache-mb": 256,
        "blockcache-maxidle-minutes": 10,
        "max-threads": 4,
        "http-timeout-seconds": 30,
        "http-retries": 3,
        "http-headers": "",
        "http-probe-sidecars": false
    }
