.. _security-server:

Sicherheit gView.Server
=======================

Der *gView Server* ist in der Regel aus dem Internet erreichbar. Diese Seite beschreibt die
Sicherheitseinstellungen in ``_config/mapserver.json`` und wie der Server sicher hinter einem
Reverse Proxy betrieben wird.

Empfohlener Betrieb
-------------------

- Den *gView Server* hinter einem Reverse Proxy betreiben (IIS, nginx, Traefik,
  Kubernetes-Ingress, Azure Application Gateway, ...), der TLS beendet und ``X-Forwarded-For``
  und ``X-Forwarded-Proto`` sendet.
- Die Adressen der Proxies unter ``security:known-proxies`` (oder ``known-networks``) eintragen.
- Die Admin-Seiten im internen Netz lassen (``security:manage:allowed-networks``, Standard) oder
  einen OIDC-Admin-Login verwenden.
- ``force-https`` setzen, wenn der Server nur über https erreichbar ist.
- Den ersten Admin mit ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD`` oder dem Setup-Code aus dem
  Log anlegen, bevor der Server von außen erreichbar ist (siehe :ref:`server_postinstallation`).

Der Abschnitt security
----------------------

Alle Einstellungen sind optional; angegeben sind die Standardwerte.

.. code-block:: javascript

   {
        "security": {
            // Server nur über https erreichbar: https-Urls in Capabilities und HSTS
            // ("force-https" auf oberster Ebene wird weiter gelesen, "security:force-https" hat Vorrang)
            "force-https": false,
            // http-Requests auf https umleiten; nur wenn der Proxy X-Forwarded-Proto sendet,
            // sonst sieht jeder Request wie http aus und die Umleitung läuft im Kreis
            "https-redirect": false,
            "hsts-max-age-days": 365,

            // Reverse Proxies, deren X-Forwarded-For/-Proto-Header akzeptiert werden.
            // Ohne Eintrag: localhost und die privaten Netze
            // (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16, fc00::/7)
            "known-proxies": [ "10.0.0.5" ],
            "known-networks": [ "10.0.0.0/24" ],

            "manage": {
                // Netze, aus denen die Admin-Seiten erreichbar sind; "*" = überall.
                // Standard: localhost und die privaten Netze
                "allowed-networks": [ "10.1.5.0/24" ],
                // Admin-Login, wird bei Aktivität verlängert
                "session-hours": 8
            },

            "login": {
                "max-failures": 10,          // pro Benutzer ...
                "max-failures-per-ip": 30,   // ... und pro Client-IP ...
                "window-minutes": 5,         // ... innerhalb dieser Zeit ...
                "lockout-minutes": 15        // ... sperren Logins für diese Zeit
            },

            "cors": {
                // Origins, die die Dienste aus dem Browser aufrufen dürfen
                "allowed-origins": [ "*" ]
            },

            // maximale Größe eines Request-Bodys; 0 = keine Grenze
            "max-request-body-mb": 50,

            // Rate Limit für die Dienste pro Client-IP (Admin-Seiten nicht begrenzt);
            // meist besser im Reverse Proxy
            "rate-limit": {
                "enabled": false,
                "requests-per-minute": 600
            }
        }
   }

Reverse Proxy und HTTPS
-----------------------

Der Server übernimmt Client-IP und Schema (http/https) aus ``X-Forwarded-For`` und
``X-Forwarded-Proto``, aber nur, wenn der Request von einem vertrauenswürdigen Proxy kommt. Die
Client-IP wird für die Admin-Netze, die Login-Sperre, das Rate Limit und das Protokoll
verwendet, das Schema für die Urls in den Capabilities.

- Ohne ``known-proxies``/``known-networks`` gilt jeder Rechner in den privaten Netzen als
  vertrauenswürdig. Das passt für Docker, Kubernetes und die meisten Installationen hinter einem
  Proxy im selben Netz. Es heißt aber auch, dass jeder Rechner im LAN ein gefälschtes
  ``X-Forwarded-For`` senden kann. Wer die Admin-Netze einschränkt, sollte die Proxies
  eintragen; sonst schreibt der Server beim Start eine Warnung ins Log.
- Ein Proxy mit öffentlicher Adresse muss eingetragen werden, sonst werden seine Header
  ignoriert.
- Mehrere Proxies hintereinander sind möglich; die Header werden bis zur ersten nicht
  vertrauenswürdigen Adresse ausgewertet.

``force-https`` lässt den Server https-Urls erzeugen und HSTS senden, auch wenn der Proxy kein
``X-Forwarded-Proto`` sendet. ``https-redirect`` leitet zusätzlich http-Requests um; nur
einschalten, wenn der Proxy ``X-Forwarded-Proto`` sendet.

Der erste Admin
---------------

Der erste Login legt den Admin nicht mehr an. Stattdessen:

- **Umgebung:** Sind ``GVIEW_ADMIN_USER`` und ``GVIEW_ADMIN_PASSWORD`` gesetzt
  (Umgebungsvariablen oder Konfigurationswerte) und gibt es noch keinen Admin, wird er beim
  Start angelegt. Praktisch für Docker. Das Passwort muss stark genug sein, sonst gibt der
  Server stattdessen einen Setup-Code aus.
- **Setup-Code:** Sonst schreibt der Server einen einmaligen Code ins Log
  (``No admin login exists. Open /manage/login and use this setup code ...``). Die Login-Seite
  fragt dann Code, Benutzername und Passwort (zweimal) ab. Der Code gilt bis zum nächsten
  Neustart; falsche Codes werden wie Fehlversuche beim Login gesperrt.

Admin-Netze
-----------

Die Admin-Seiten (``/manage``, Setup, Publizieren und Löschen von Diensten und Ordnern in
``/browseservices``, die alten Urls ``AddMap``/``RemoveMap``/``GetMetadata``/``SetMetadata``)
antworten nur Clients aus ``security:manage:allowed-networks``. Alle anderen bekommen 404, und
der Zugriff wird protokolliert. Der Link **Manage** wird außerhalb ausgeblendet.

- Standard: localhost und die privaten Netze.
- ``"*"`` erlaubt den Zugriff von überall.
- Mit OIDC-Admin-Login (``Authentication:Type = oidc``) gilt die Einschränkung nur, wenn
  ``allowed-networks`` gesetzt ist.
- *gView Carto* publiziert Dienste über ``BrowseServices/PublishService``: Der Carto-Server muss
  in einem erlaubten Netz liegen.

Logins, Sperre und Sitzungen
----------------------------

- Passwörter werden als PBKDF2-Hashes gespeichert. Hashes älterer Versionen funktionieren weiter
  und werden beim nächsten erfolgreichen Login ersetzt.
- Fehlgeschlagene Logins (Manage-Login, Token-Login, ``generateToken``, Basic Auth) werden pro
  Benutzer und pro Client-IP gezählt (``security:login``). Während der Sperre schlägt jeder
  Login fehl, als wäre das Passwort falsch. Die Zähler liegen im Speicher jeder Server-Instanz.
- Admin-Sitzungen dauern ``security:manage:session-hours`` (8) und werden bei Aktivität
  verlängert.
- Jedes Token aus einem Login (Admin-Cookie, Token-User-Cookie, GeoServices-Token) trägt den
  Security Stamp des Logins. Ein Passwortwechsel beendet alle Sitzungen und Tokens dieses
  Benutzers. **Sign out everywhere** (Sidebar, für Admins) macht dasselbe ohne
  Passwortwechsel; dabei wird ``<user>.stamp`` neben die Datei ``<user>.lgn`` geschrieben.

CORS, Cookies und Security-Header
---------------------------------

- CORS-Header gibt es nur für die Dienste (``security:cors:allowed-origins``), nie für die
  Admin- und Login-Seiten.
- Das Login-Cookie ist ``HttpOnly``, ``Secure`` und ``SameSite=Lax``.
- Admin-Requests, die etwas ändern und mit Cookies gesendet werden, brauchen ein
  Antiforgery-Token (die Admin-Seiten senden es automatisch). Requests mit
  ``Authorization``-Header (Basic, Bearer) werden nicht geprüft.
- Jede Antwort bekommt ``X-Content-Type-Options: nosniff`` und
  ``Referrer-Policy: strict-origin-when-cross-origin``, die Admin- und Login-Seiten zusätzlich
  ``X-Frame-Options: DENY`` und eine Content-Security-Policy. Kartendienste können weiter in
  andere Anwendungen eingebettet werden.

Url-Tokens
----------

Url-Tokens (``/geoservices(token-name~secret)/rest/...``) werden gehasht gespeichert. Ein neues
oder recyceltes Token wird **einmal** angezeigt, direkt nach dem Erzeugen; die Liste zeigt
``token-name~(hidden)``. Url-Tokens älterer Versionen werden beim ersten Gebrauch gehasht. Ein
falsches Url-Token wird mit *Invalid Token (498)* beantwortet.

Grenzen
-------

- **Request-Body:** ``security:max-request-body-mb`` (50 MB) für Kestrel, IIS und Formulare.
  Mit IIS muss ``requestLimits maxAllowedContentLength`` in der ``web.config`` mindestens so
  groß sein.
- **Features pro Request:** GeoServices, OGC API Features und WFS liefern höchstens
  ``MaxRecordCount`` des Dienstes (Einstellung des Dienstes, sonst
  ``mapserver-defaults:maxRecordCount``, sonst 1000). Auch WFS ``MAXFEATURES`` wird darauf
  begrenzt.
- **Glyphen für Vector Tiles** werden pro installierter Schrift gecacht, nicht pro angefragtem
  Font-Stack.
- **Rate Limit:** optional, ``security:rate-limit``, antwortet mit 429.

.. _security-server-sql:

Datenbank und SQL-Filter
------------------------

**Datenbank-User mit möglichst wenig Rechten:** Der *gView Server* greift mit dem User aus der
Verbindung der Dienste auf die Datenbank zu. Dieser User sollte nur die Rechte haben, die die
Dienste brauchen:

- Leserechte auf die Tabellen und Views der Dienste.
- Schreibrechte (INSERT, UPDATE, DELETE) nur auf Tabellen, die über einen *FeatureServer*
  bearbeitet werden.
- Keine Owner-, DDL- oder Admin-Rechte (CREATE, ALTER, DROP, ``sysadmin``, ``superuser``) und
  kein Zugriff auf andere Datenbanken oder Schemas.

Selbst wenn ein Filter einmal mehr durchlässt als gedacht, kann damit nichts verändert oder
gelöscht und keine fremde Tabelle gelesen werden.

**Filter in Requests:** Filter, die ein Client schickt, werden vor der Abfrage geprüft:

- GeoServices ``where``, ``layerDefs`` (Export, Identify) und
  ``dynamicLayers[].definitionExpression``, ArcXML ``where``, GeoJsonService ``where``.
- Erlaubt sind Felder des Layers, Text-, Zahlen- und Datumsliterale (``date '2020-01-01'``,
  ``timestamp '...'``), ``NULL``, ``TRUE``/``FALSE``, Vergleiche (``= <> != < > <= >=``),
  ``AND``, ``OR``, ``NOT``, ``IN``, ``LIKE`` (mit ``ESCAPE``), ``IS [NOT] NULL``, ``BETWEEN``,
  Klammern und ``+ - * /``.
- Erlaubte Funktionen: ``UPPER``, ``LOWER``, ``CHAR_LENGTH``, ``TRIM``, ``CAST``, ``EXTRACT``,
  ``CURRENT_DATE``, ``CURRENT_TIMESTAMP``.
- Alles andere (Unterabfragen, ``UNION``, ``;``, Kommentare, andere Funktionen, unbekannte
  Felder) wird mit Status 400 abgewiesen. Die Meldung nennt den abgewiesenen Teil, nicht das
  SQL. ``1=1`` (Esri-Clients) bleibt erlaubt.
- Sortierung (GeoServices ``orderByFields``, WFS ``SortBy``) nur ``Feld [ASC|DESC]``,
  Feldlisten (``outFields``, ArcXML ``subfields``, Felder von Renderern aus ``dynamicLayers``
  und WMS ``SLD_BODY``) nur Felder des Layers.
- WFS-Filter werden aus dem XML neu als SQL geschrieben (Texte maskiert, Zahlen geprüft).

Der Filter eines Layers aus der Dienst-Konfiguration wird nicht geprüft; er wird immer mit
``AND`` an den Filter aus dem Request gehängt.

Weitere Funktionen lassen sich freigeben:

.. code-block:: javascript

   {
        "security": {
            "sql": {
                // zusätzlich zu den Standard-Funktionen, nur Funktionsnamen
                "allowed-functions": [ "ROUND", "SUBSTRING" ]
            }
        }
   }

.. note::
   Ab 8.26.4001 werden auch ``layerDefs`` und ``definitionExpression`` geprüft. Clients, die
   dort andere Funktionen oder Unterabfragen verwenden, bekommen jetzt einen Fehler. Benötigte
   Funktionen unter ``security:sql:allowed-functions`` eintragen.

Protokoll
---------

Sicherheitsereignisse werden mit der Kategorie ``gView.Server.Security`` protokolliert:
fehlgeschlagene und abgewiesene Logins, Sperren, abgewiesene Admin-Requests, das Anlegen des
Admins, *Sign out everywhere* und Konfigurationswarnungen. Client-IP und Benutzername werden
protokolliert, Passwörter nie. Über die Kategorie lässt sich das Log filtern oder weiterleiten
(fail2ban, SIEM).

Tokens, Passwörter, Secrets, Url-Tokens und ``Authorization``-Werte werden in den
gView-Dienst-Logs (``Logging:LogServiceRequests``/``...Details``) maskiert (``***``). Das
ASP.NET-Request-Log (``Microsoft.AspNetCore.Hosting``) wird nicht maskiert: auf ``Warning``
lassen (Standard in ``appsettings.json``), sonst stehen Query-Strings mit Tokens im Klartext im
Log.

Umstieg auf v9
--------------

- **Sitzungen und Tokens:** Tokens von vor v9 haben keinen Security Stamp und werden abgelehnt.
  Admins und Token-User melden sich neu an, GeoServices-Clients holen ein neues Token.
- **Admin-Seiten von außen:** nur aus ``security:manage:allowed-networks`` (Standard: private
  Netze). Eigene Netze, ``"*"`` eintragen oder OIDC verwenden.
- **Proxy mit öffentlicher Adresse:** unter ``security:known-proxies`` eintragen, sonst sieht
  der Server Client-IP und https nicht.
- **Erster Admin:** Eine neue Installation braucht ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD``
  oder den Setup-Code aus dem Log.
- **WFS:** ``GetFeature`` liefert höchstens ``MaxRecordCount`` Features.
