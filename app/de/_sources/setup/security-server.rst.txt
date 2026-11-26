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
- Die Adressen der Proxies unter ``security:known-proxies`` (oder ``known-networks``) eintragen,
  im Internetbetrieb immer: ohne Eintrag gilt jeder Rechner in den privaten Netzen als Proxy.
- Die Admin-Seiten im internen Netz lassen (``security:manage:allowed-networks``, Standard) oder
  einen OIDC-Admin-Login verwenden.
- Die Zwei-Faktor-Anmeldung für Admins einschalten, im Internetbetrieb als Pflicht
  (``security:manage:two-factor = required``, siehe :ref:`security-server-2fa`).
- ``force-https`` setzen, wenn der Server nur über https erreichbar ist.
- Den ersten Admin mit ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD`` oder dem Setup-Code aus dem
  Log anlegen, bevor der Server von außen erreichbar ist (siehe :ref:`server_postinstallation`).
- Den Server unter einem eigenen Dienstkonto betreiben, das nur in wenige Ordner schreiben darf,
  und mit einem Datenbank-User, der nur liest (siehe :ref:`security-server-account` und
  :ref:`security-server-sql`).
- Updates zeitnah einspielen (siehe :ref:`security-server-updates`).

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
                "session-hours": 8,
                // Zwei-Faktor-Anmeldung (TOTP) für den Admin-Login: off | optional | required
                "two-factor": "optional",
                // Name des Servers in der Authenticator-App
                "two-factor-issuer": "gView Server"
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
- ``"*"`` erlaubt den Zugriff von überall. Bei einem Server im Internet **nicht** verwenden: Dann
  sind Login und Admin-Seiten für jeden erreichbar und nur noch durch das Passwort geschützt.
  Besser die Netze der Admins eintragen, ein VPN verwenden oder einen OIDC-Admin-Login. Geht es
  nicht anders, mindestens die Zwei-Faktor-Anmeldung zur Pflicht machen; sonst schreibt der
  Server beim Start eine Warnung ins Log.
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

.. _security-server-2fa:

Zwei-Faktor-Anmeldung
---------------------

Der Admin-Login kann zusätzlich zum Passwort einen Code aus einer Authenticator-App verlangen
(TOTP nach RFC 6238: 6 Ziffern, alle 30 Sekunden neu; z. B. Microsoft Authenticator, Google
Authenticator, Aegis, 1Password, Bitwarden).

``security:manage:two-factor``:

- ``off``: keine Zwei-Faktor-Anmeldung.
- ``optional`` (Standard): Jeder Admin kann sie für sich einrichten. Wer sie eingerichtet hat,
  wird nach dem Passwort nach dem Code gefragt.
- ``required``: Pflicht für alle Admins. Wer noch keine hat, muss sie direkt nach dem Passwort
  einrichten, bevor er auf die Admin-Seiten kommt. Bestehende Sitzungen von Admins ohne zweiten
  Faktor enden beim Umstellen. Für Server, deren Admin-Seiten aus dem Internet erreichbar sind,
  wird ``required`` dringend empfohlen.

**Einrichten:** Sidebar **Two-factor login** (Seite ``/manage/twofactor``). Den QR-Code mit der
App scannen (oder den Schlüssel von Hand eingeben) und innerhalb von 10 Minuten mit einem Code aus
der App bestätigen; **Sign out everywhere** oder ein neues Passwort brechen eine laufende Einrichtung ab.
Danach zeigt der Server **einmalig** 10 Recovery-Codes (``xxxxx-xxxxx``) an: kopieren oder als
Textdatei herunterladen und sicher aufbewahren, z. B. im Passwort-Manager. Mit dem Einrichten
enden alle anderen Sitzungen dieses Admins.

**Anmelden:** Nach Benutzername und Passwort fragt die Login-Seite nach dem Code. Statt des Codes
kann ein Recovery-Code eingegeben werden; jeder Recovery-Code gilt nur einmal. Sind nur noch zwei
oder weniger übrig, zeigt der Server nach dem Login einen Hinweis.

- Codes werden 30 Sekunden vor und nach der aktuellen Zeit akzeptiert. Die Uhren von Server und
  Telefon müssen stimmen (NTP).
- Ein schon benutzter Code wird abgelehnt.
- Falsche Codes zählen wie falsche Passwörter für die Login-Sperre (``security:login``). Nach 5
  falschen Codes beginnt der Login wieder beim Passwort. Zwischen Passwort und Code hält der
  Browser nur ein kurzlebiges Cookie (5 Minuten) ohne Admin-Rechte.

**Verwalten** (Seite **Two-factor login**, angemeldet):

- *New recovery codes:* neue Recovery-Codes mit einem Code aus der App; die alten werden ungültig.
- *New device:* auf ein neues Telefon umziehen (Code aus der App oder Recovery-Code, dann neuer
  QR-Code). Das alte Gerät gilt, bis das neue bestätigt ist. Beendet alle anderen Sitzungen.
- *Disable:* nur bei ``optional``, mit Passwort und Code. Beendet alle anderen Sitzungen.

**Speicherung:** ``<user>.2fa`` neben ``<user>.lgn`` im Ordner ``_login/manage``. Das Secret ist
mit dem Server-Zertifikat (``crypto0.pfx``) verschlüsselt, die Recovery-Codes sind gehasht. Die
Datei gehört wie ``crypto0.pfx`` in die Sicherung, aber nicht in fremde Hände.

**Notfall-Reset** (Telefon und Recovery-Codes verloren):

- Die Datei ``_login/manage/<user>.2fa`` löschen, oder
- den Server mit der Umgebungsvariable (oder dem Konfigurationswert) ``GVIEW_ADMIN_RESET_2FA``
  starten: ``admin`` bzw. ``admin1,admin2`` für einzelne Admins, ``*`` für alle. Praktisch für
  Docker ohne Shell. Die Variable wirkt bei **jedem** Start, solange sie gesetzt ist, und der
  Server warnt dann im Log: nach dem Reset wieder entfernen.

Der Reset beendet alle Sitzungen des Admins. Bei ``required`` richtet er die
Zwei-Faktor-Anmeldung beim nächsten Login neu ein, bei ``optional`` reicht das Passwort.

**Nicht betroffen:**

- Token-User (Token-Login, ``generateToken``, Basic Auth, Url-Tokens) und damit auch das
  Publizieren aus *gView Carto*: Das sind meist Programme, die keinen Code eingeben können.
  Token-User mit Publish-Rechten deshalb mit langen, zufälligen Passwörtern versehen und
  regelmäßig wechseln.
- OIDC-Admin-Login (``Authentication:Type = oidc``): Die Zwei-Faktor-Anmeldung ist dann Sache
  des Identity Providers.

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
- **Bildgröße:** Kartenbilder (WMS, GeoServices, ArcXML, GeoJson-Service, OGC API Maps) sind
  höchstens ``MaxImageWidth``/``MaxImageHeight`` des Dienstes groß (Einstellung des Dienstes,
  sonst ``mapserver-defaults:maxImageWidth``/``maxImageHeight``, sonst 4096), aber nie größer als
  16384 Pixel. Größere Anfragen werden mit gleichem Seitenverhältnis verkleinert, die DPI
  entsprechend reduziert. Breite oder Höhe ≤ 0 ergeben einen Fehler. Die Capabilities melden die
  tatsächlichen Grenzen.
- **DPI:** höchstens ``mapserver-defaults:maxDpi`` (Standard 600, gilt für alle Dienste). Höhere
  Werte werden auf diese Grenze gesetzt, die Bildgröße bleibt gleich.

  .. code-block:: json

      "mapserver-defaults": {
        "maxImageWidth": 4096,
        "maxImageHeight": 4096,
        "maxDpi": 600
      }

- **XML:** XML aus Requests (ArcXML, WFS POST, ``SLD_BODY``, OGC-Filter) und aus Antworten
  fremder Dienste wird ohne DTD-Verarbeitung gelesen: keine Entity-Expansion, keine externen
  Ressourcen.

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

.. _security-server-account:

Dienstkonto und Dateirechte
---------------------------

Den *gView Server* nicht als Administrator, ``root`` oder ``LocalSystem`` betreiben, sondern unter
einem eigenen Dienstkonto (IIS: App-Pool-Identität, Windows-Dienst oder Linux: eigener User,
Docker: User ``app``). Das Konto braucht:

- **Lesen:** das Programmverzeichnis, ``_config`` und die Daten der Dienste (Dateien, Raster,
  FDBs).
- **Schreiben** nur in diese Ordner (aus ``_config/mapserver.json``):

  - ``output-path``: Kartenbilder, die als Url ausgeliefert werden.
  - ``tilecache-root``: Tile-Cache, Vector-Tile-Cache und Glyphen (``_glyphs``).
  - ``services-folder``: ``services`` (Dienste publizieren, löschen, Einstellungen),
    ``_login`` (Logins, Tokens, Security Stamps, Zertifikat zum Signieren der Tokens),
    ``log`` (Dienst-Logs) und ``networks`` (Netzwerk-Build; oder ``networks-folder``).
  - Daten, die über einen *FeatureServer* oder OGC API Features bearbeitet werden (dateibasierte
    Quellen wie FDB, GeoPackage, Shape).

- **Keine** Schreibrechte auf das Programmverzeichnis und ``_config``: Beim ersten Start legt der
  Server ``_config/mapserver.json`` an, wenn sie fehlt. Dafür einmalig Schreibrechte geben oder die
  Datei vorher anlegen.

``_login`` enthält Passwort-Hashes und das Zertifikat, mit dem Tokens signiert werden: Nur das
Dienstkonto und Admins dürfen diesen Ordner lesen. Keinen der Ordner unter einem Verzeichnis
ablegen, das der Webserver direkt ausliefert (außer ``output-path``).

Für den Zugriff auf Datenbanken siehe :ref:`security-server-sql`.

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

**Fehlermeldungen an Clients:** Die Dienste geben keine Interna (Pfade, Verbindungsdaten, SQL,
Datenbankmeldungen, Stacktraces) an Clients weiter. Unerwartete Fehler werden mit
``internal server error (id: 7f3a9c21)`` beantwortet; in Kartenbildern und Kacheln steht derselbe
Text. Die vollständige Meldung steht mit derselben Id im Fehler-Log des Dienstes
(``Logging:LogServiceErrors``, Standard ``true``, Ordner ``log``) bzw. beim GeoJson-Service und
OGC API im Server-Log. Fehler, die der Request selbst verursacht (unbekannter Layer, ungültiger
Filter, ...), werden weiter mit ihrem Text gemeldet. Die Admin-Seiten zeigen die Details.

.. _security-server-updates:

Updates einspielen
------------------

- **gView Server:** Neue Versionen enthalten aktualisierte Bibliotheken. Releases mit einem
  Abschnitt *Security* im Changelog zeitnah einspielen. Vor jedem Release werden die
  NuGet-Pakete gegen bekannte Schwachstellen geprüft.
- **.NET-Runtime:** Die normalen ZIPs verwenden die installierte ASP.NET-Core-Runtime. Diese über
  Windows Update bzw. die Paketverwaltung aktuell halten. Die *portable*-ZIPs bringen die Runtime
  mit und werden nur mit einem neuen gView-Release aktualisiert.
- **Docker:** Die Images basieren auf ``mcr.microsoft.com/dotnet/aspnet``. Mit jedem Release neu
  ziehen; eigene Images auf Basis von ``gview-server`` regelmäßig neu bauen.
- **GDAL** wird nicht mitgeliefert (``GVIEW_GDAL_PATH``, Programmverzeichnis oder System). Eine
  eigene GDAL-Installation selbst aktuell halten. Die übrigen nativen Bibliotheken (Skia,
  HarfBuzz, SQL-Client) kommen mit dem gView-Release.


Umstieg auf v9
--------------

- **Sitzungen und Tokens:** Tokens von vor v9 haben keinen Security Stamp und werden abgelehnt.
  Admins und Token-User melden sich neu an, GeoServices-Clients holen ein neues Token.
- **Admin-Seiten von außen:** nur aus ``security:manage:allowed-networks`` (Standard: private
  Netze). Eigene Netze eintragen oder OIDC verwenden; ``"*"`` nur, wenn der Server nicht aus dem
  Internet erreichbar ist.
- **Proxy mit öffentlicher Adresse:** unter ``security:known-proxies`` eintragen, sonst sieht
  der Server Client-IP und https nicht.
- **Erster Admin:** Eine neue Installation braucht ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD``
  oder den Setup-Code aus dem Log.
- **WFS:** ``GetFeature`` liefert höchstens ``MaxRecordCount`` Features.
- **DPI:** Kartenbilder mit mehr als 600 DPI (z. B. Druck) werden auf 600 DPI begrenzt. Wird mehr
  gebraucht, ``mapserver-defaults:maxDpi`` erhöhen.
- **Docker:** Die Images laufen als User ``app`` (UID 1654). Gemountete Verzeichnisse müssen für
  diesen User schreibbar sein (siehe :doc:`run-docker`).
- **Fehlermeldungen:** Unerwartete Fehler werden mit ``internal server error (id: ...)``
  beantwortet statt mit der internen Meldung (siehe `Protokoll`_). Die Details stehen im Log.
  FeatureServer-Edits melden Datenbankfehler ebenfalls nur noch mit dieser Id.
