Konfiguration gView.Web
=======================

Die *gView.Web* Anwendung kann über die Datei ``_config/gview-web.config`` 
konfiguriert werden:

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
                "TargetUrl": "https://localhost:44331"
            }
        ]
    }       

Benutzerdefinierte Laufwerke/Verzeichnisse
------------------------------------------

Im Abschnitt ``Drives`` können Pfade festgelegt werden, die im **gView.DataExplorer** unter 
``Computer`` aufgelistet werden. Wird dieser Abschnitt weg gelassen, werden alle Laufwerke,
die zur Verfügung stehen angezeigt.

.. note::

    Die Pfade und Laufwerke beziehen sich auf den Computer/Server auf dem **gView.Web** 
    installiert ist.

.. note::

    Wenn **gView.Web** am Server installiert ist, wird **dringend** empfohlen, diesen Abschnitt
    zu pflegen. Hier darf und soll man aus Sicherheitsgründen nicht auf das komplette Dateisystem
    zugreifen können. Ein potentieller Angreifer hätte so theoretisch Zugriff auf alle 
    Dateien des Servers!

Innerhalb einen Pfades kann ein Platzhalter ``{{username}}`` verwendet werden. Dienst wird mit dem 
Usernamen des aktuell bei **gView.Web** angemeldeten Benutzers ersetzt. Dann kann ein 
Anwender beispielsweise MXL Dateien in einem *privaten* Verzeichnis ablegen.

Benutzerdefinierte Kacheln
--------------------------

Im Abschnitt ``CustomTiles`` können beliebige weitere Kacheln angelegt werden, die auf der 
Startseite von **gView.Web** angezeigt werden. Damit können beispielsweise eine Kacheln zu
einer oder mehreren **gView.Server** Instanzen angelegt werden.

Authentifizierung
-----------------

Um zu bestimmen, wie man sich bei **gView.Web** anmelden kann/muss dient der Abschnitt 
``Authentication``.

.. note::

    Wird der Abschnitt weg gelassen, erfolgt eine Authentifizierung. Jeder Benutzer darf 
    alles. Das sollte allerdings nur für lokale Installation möglich sein.
    Läuft **gView.Web** auf einem Server sollte **unbedingt** eine Authentifizierungsmethode
    eingestellt werden.

**gView.Web** unterscheidet zwei Kategorien von Benutzern:

* **Admin-User:** Benutzer, die alle Anwendungen und Werkzeuge verwenden dürfen.
* **Carto-User:** Benutzer, die nur die *Carto* Anwendung benutzen dürfen. Die Anwender
  können nur Karten erstellen und Speichern. Sie können auf vordefinierte Datenbank Verbindungen
  zugreifen, um Geodaten in die Karte einzubinden. Allerdings könne die die *Connection String*
  für diese nicht einsehen oder ändern (im Gegensatz *Admin-Usern*) 

Derzeit sind folgende Authentifizierungsmethoden möglich:

* ``forms``: Benutzer können sich mit Benutzername/Passwort über eine Login Maske
  anmelden. Welche Benutzer es gibt, wird direkt in der ``gview-web.config`` eingestellt.
  Hierbei handelt es sich um eine einfach zu pflegende Art der Authentifizierung die 
  für kleine Teams oft ausreicht. Diese Methode bietet keine fortgeschrittenen Sicherheitsrichtlinien
  und sollte maximal für Anwendungen im Intranet oder abgegrenzten Bereichen verwendet werden.

  Eine Bereitstellung von **gView.Web** über das Internet sollte mit dieser Methode nicht
  angedacht werden.

* ``oidc``: OpenID Connect ist eine weiter Methode der Authentifizierung. Dabei meldet 
  sich der Anwender über einen extern Authentifizierungsstelle an. Diese biete in der 
  Regel höhere Sicherheit und Zwei Faktor Authentifizierung, etc

Forms Authentifizierung
+++++++++++++++++++++++  

Hier wird als ``Type`` der Wert ``forms`` angegeben. In der Sektion ``Forms`` können dann 
mehrerer ``AdminUsers`` und ``CartoUsers`` angegeben werden. Jeweils als Array von Objekten
mit den Eigenschaften ``Username`` und ``PasswordHash``.
Damit das Passwort hier nicht im Klartext angeben werden muss, wird statt dessen ein 
Hashwert im Hexadezimal Format angeben. Dieser Hash kann sowohl mit dem 
``SHA256`` als auch mit ``SHA512`` Algorithmus gehasht worden sein. 

.. note::
    
    Es gibt Online Tools, mit den diese Hashwerte berechnet werden können, z.B:

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

Steht ein *Identity Dienst* zur Verfügung, der *OpenID Connect* unterstützt, kann dieser für
Authentifizierung verwendet werden.

Als ``Type`` muss hier der Wert ``oidc`` eingetragen werden. Im Abschnitt ``Oidc`` muss
der *Identity Server* (``Authority``) eingetragen werden. Am *Identity Server* muss 
*gView.Web* als Client hinzugefügt werden. Die entsprechende ``ClientId`` und das 
``ClientSecret`` sind ebenfalls hier einzutragen. Als ``Scopes`` werden die unter angeführten
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

Da der *Identity Server* auch Rollen mitliefert muss noch jeweils eine Rolle für 
**Admin-User** und **Carto-User** angegeben werden. Das erfolgt über die Parameter 
``RequiredUserRole`` (Carto-User) und ``RequiredAdminRole`` (Admin-User).