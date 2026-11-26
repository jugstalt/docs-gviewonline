.. _security-server:

Security gView.Server
=====================

The *gView Server* is usually reachable from the internet. This page describes the security
settings in ``_config/mapserver.json`` and how the server runs safely behind a reverse proxy.

Recommended setup
-----------------

- Run the *gView Server* behind a reverse proxy (IIS, nginx, Traefik, a Kubernetes ingress,
  Azure Application Gateway, ...) that terminates TLS and sends ``X-Forwarded-For`` and
  ``X-Forwarded-Proto``.
- Enter the addresses of your proxies in ``security:known-proxies`` (or ``known-networks``),
  always on the internet: without an entry every host in the private networks counts as a proxy.
- Keep the admin pages in your internal network (``security:manage:allowed-networks``, the
  default) or use an OIDC admin login.
- Switch on the two-factor login for admins, on the internet as a requirement
  (``security:manage:two-factor = required``, see :ref:`security-server-2fa`).
- Set ``force-https`` when the server is only reachable via https.
- Create the first admin with ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD`` or the setup code
  from the log before the server is reachable from outside (see :ref:`server_postinstallation`).
- Run the server with its own service account that may write to a few folders only, and with a
  database user that only reads (see :ref:`security-server-account` and
  :ref:`security-server-sql`).
- Install updates promptly (see :ref:`security-server-updates`).

The security section
--------------------

All settings are optional; the values shown are the defaults.

.. code-block:: javascript

   {
        "security": {
            // the server is only reachable via https: https urls in capabilities and HSTS
            // (the top level "force-https" is still read, "security:force-https" wins)
            "force-https": false,
            // redirect http requests to https; only if the proxy sends X-Forwarded-Proto,
            // otherwise every request looks like http and the redirect loops
            "https-redirect": false,
            "hsts-max-age-days": 365,

            // reverse proxies whose X-Forwarded-For/-Proto headers are accepted.
            // Without an entry: loopback and the private networks
            // (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16, fc00::/7)
            "known-proxies": [ "10.0.0.5" ],
            "known-networks": [ "10.0.0.0/24" ],

            "manage": {
                // networks the admin pages answer to; "*" = everywhere.
                // Default: loopback and the private networks
                "allowed-networks": [ "10.1.5.0/24" ],
                // admin login, renewed while the admin is active
                "session-hours": 8,
                // two-factor login (TOTP) for the admin login: off | optional | required
                "two-factor": "optional",
                // name of the server in the authenticator app
                "two-factor-issuer": "gView Server"
            },

            "login": {
                "max-failures": 10,          // per user ...
                "max-failures-per-ip": 30,   // ... and per client ip ...
                "window-minutes": 5,         // ... within this time ...
                "lockout-minutes": 15        // ... lock logins for this time
            },

            "cors": {
                // origins allowed to call the services from a browser
                "allowed-origins": [ "*" ]
            },

            // maximum size of a request body; 0 = no limit
            "max-request-body-mb": 50,

            // rate limit for the services per client ip (admin pages are not limited);
            // usually better done by the reverse proxy
            "rate-limit": {
                "enabled": false,
                "requests-per-minute": 600
            }
        }
   }

Reverse proxy and HTTPS
-----------------------

The server takes the client ip and the scheme (http/https) from ``X-Forwarded-For`` and
``X-Forwarded-Proto``, but only if the request comes from a trusted proxy. The client ip is
used for the admin networks, the login lockout, the rate limit and the logs; the scheme for the
urls in the capabilities.

- Without ``known-proxies``/``known-networks`` every host in the private networks is trusted.
  This fits Docker, Kubernetes and most installations behind a proxy in the same network. It
  also means that every host in your LAN could send a fake ``X-Forwarded-For``. If you restrict
  the admin networks, enter your proxies: otherwise the server writes a warning to the log at
  startup.
- A proxy on a public address must be entered, otherwise its headers are ignored.
- Several proxies in a row are supported; the headers are processed up to the first address
  that is not trusted.

``force-https`` makes the server generate https urls and send HSTS, even if the proxy does not
send ``X-Forwarded-Proto``. ``https-redirect`` additionally redirects http requests; enable it
only if the proxy sends ``X-Forwarded-Proto``.

The first admin
---------------

A first login no longer creates the admin. Instead:

- **Environment:** if ``GVIEW_ADMIN_USER`` and ``GVIEW_ADMIN_PASSWORD`` are set (environment
  variables or configuration values) and no admin exists, the admin is created at startup.
  Useful for Docker. The password must be strong enough, otherwise the server falls back to the
  setup code.
- **Setup code:** otherwise the server writes a one-time code to the log
  (``No admin login exists. Open /manage/login and use this setup code ...``). The login page
  then asks for the code, a username and the password (twice). The code is valid until the
  server restarts; wrong codes are locked out like failed logins.

Admin networks
--------------

The admin pages (``/manage``, setup, publishing and deleting services and folders in
``/browseservices``, the legacy urls ``AddMap``/``RemoveMap``/``GetMetadata``/``SetMetadata``)
only answer to clients in ``security:manage:allowed-networks``. Everyone else gets a 404 and the
request is logged. The **Manage** links are hidden outside.

- Default: loopback and the private networks.
- ``"*"`` allows everywhere. Do **not** use it for a server on the internet: login and admin pages
  are then reachable for everyone and only protected by the password. Enter the admins' networks,
  use a VPN or an OIDC admin login instead. If there is no other way, at least make the
  two-factor login required; otherwise the server writes a warning to the log at startup.
- With an OIDC admin login (``Authentication:Type = oidc``) the restriction only applies if
  ``allowed-networks`` is configured.
- *gView Carto* publishes services with ``BrowseServices/PublishService``: the Carto server must
  be in an allowed network.

Logins, lockout and sessions
----------------------------

- Passwords are stored as PBKDF2 hashes. Hashes from older versions keep working and are
  replaced on the next successful login.
- Failed logins (manage login, token login, ``generateToken``, Basic auth) are counted per user
  and per client ip (``security:login``). While locked, every login fails as if the password
  were wrong. The counters live in the memory of each server instance.
- Admin sessions last ``security:manage:session-hours`` (8) and are renewed while the admin is
  active.
- Every token from a login (admin cookie, token user cookie, GeoServices token) carries the
  security stamp of the login. Changing the password ends all sessions and tokens of that user.
  **Sign out everywhere** (sidebar, for admins) does the same without changing the password; it
  writes ``<user>.stamp`` next to the ``<user>.lgn`` file.

.. _security-server-2fa:

Two-factor login
----------------

The admin login can ask for a code of an authenticator app in addition to the password (TOTP,
RFC 6238: 6 digits, a new one every 30 seconds; e.g. Microsoft Authenticator, Google
Authenticator, Aegis, 1Password, Bitwarden).

``security:manage:two-factor``:

- ``off``: no two-factor login.
- ``optional`` (default): every admin can set it up for themselves. Admins who did are asked
  for the code after the password.
- ``required``: required for all admins. An admin without one has to set it up right after the
  password, before reaching the admin pages. Existing sessions of admins without a second
  factor end when switching. Strongly recommended for servers whose admin pages are reachable
  from the internet.

**Set up:** sidebar **Two-factor login** (page ``/manage/twofactor``). Scan the QR code with the
app (or enter the key manually) and confirm with a code from the app within 10 minutes; **Sign out
everywhere** or a new password cancels a running setup. The server then shows 10
recovery codes (``xxxxx-xxxxx``) **once**: copy them or download them as a text file and keep
them safe, e.g. in a password manager. Setting it up ends all other sessions of this admin.

**Log in:** after username and password the login page asks for the code. A recovery code can
be entered instead; each recovery code works once. When two or fewer are left, the server shows
a hint after the login.

- Codes are accepted 30 seconds before and after the current time. The clocks of server and
  phone must be correct (NTP).
- A code that was already used is refused.
- Wrong codes count like wrong passwords for the login lockout (``security:login``). After 5
  wrong codes the login starts again with the password. Between password and code the browser
  only holds a short-lived cookie (5 minutes) without admin rights.

**Manage** (page **Two-factor login**, logged in):

- *New recovery codes:* new recovery codes with a code from the app; the old ones become
  invalid.
- *New device:* move to a new phone (code from the app or a recovery code, then a new QR code).
  The old device stays valid until the new one is confirmed. Ends all other sessions.
- *Disable:* only with ``optional``, with password and code. Ends all other sessions.

**Storage:** ``<user>.2fa`` next to ``<user>.lgn`` in the folder ``_login/manage``. The secret is
encrypted with the server certificate (``crypto0.pfx``), the recovery codes are hashed. Back the
file up like ``crypto0.pfx``, and keep it out of other hands.

**Emergency reset** (phone and recovery codes lost):

- delete the file ``_login/manage/<user>.2fa``, or
- start the server with the environment variable (or configuration value)
  ``GVIEW_ADMIN_RESET_2FA``: ``admin`` or ``admin1,admin2`` for single admins, ``*`` for all.
  Handy for Docker without a shell. The variable applies on **every** start while it is set,
  and the server then warns in the log: remove it after the reset.

The reset ends all sessions of the admin. With ``required`` the admin sets the two-factor login
up again at the next login, with ``optional`` the password is enough.

**Not affected:**

- Token users (token login, ``generateToken``, Basic auth, url tokens) and with them publishing
  from *gView Carto*: these are mostly programs that cannot enter a code. Give token users with
  publish rights long random passwords and change them regularly.
- OIDC admin login (``Authentication:Type = oidc``): two-factor login is then up to the
  identity provider.

CORS, cookies and security headers
----------------------------------

- CORS headers are sent for the services only (``security:cors:allowed-origins``), never for
  the admin and login pages.
- The login cookie is ``HttpOnly``, ``Secure`` and ``SameSite=Lax``.
- State changing admin requests sent with cookies need an antiforgery token (the admin pages
  send it automatically). Requests with an ``Authorization`` header (Basic, Bearer) are not
  checked.
- Every response gets ``X-Content-Type-Options: nosniff`` and
  ``Referrer-Policy: strict-origin-when-cross-origin``; the admin and login pages also
  ``X-Frame-Options: DENY`` and a Content-Security-Policy. Map services can still be embedded by
  other applications.

Url tokens
----------

Url tokens (``/geoservices(token-name~secret)/rest/...``) are stored hashed. A new or recycled
token is shown **once**, right after it is created; the list shows ``token-name~(hidden)``. Url
tokens from older versions are hashed on their first use. A wrong url token is answered with
*Invalid Token (498)*.

Limits
------

- **Request body:** ``security:max-request-body-mb`` (50 MB) for Kestrel, IIS and forms. With
  IIS, ``requestLimits maxAllowedContentLength`` in ``web.config`` must be at least as large.
- **Features per request:** GeoServices, OGC API Features and WFS return at most the
  ``MaxRecordCount`` of the service (service setting, else ``mapserver-defaults:maxRecordCount``,
  else 1000). WFS ``MAXFEATURES`` is limited by it, too.
- **Vector tile glyphs** are cached per installed font, not per requested font stack.
- **Rate limit:** optional, ``security:rate-limit``, answers with 429.
- **Image size:** map images (WMS, GeoServices, ArcXML, GeoJson service, OGC API Maps) are at most
  ``MaxImageWidth``/``MaxImageHeight`` of the service (service setting, else
  ``mapserver-defaults:maxImageWidth``/``maxImageHeight``, else 4096), but never larger than
  16384 pixels. Larger requests are scaled down keeping the aspect ratio, the dpi is reduced
  accordingly. Width or height ≤ 0 is an error. The capabilities report the actual limits.
- **Dpi:** at most ``mapserver-defaults:maxDpi`` (default 600, applies to all services). Higher
  values are set to this limit, the image size stays the same.

  .. code-block:: json

      "mapserver-defaults": {
        "maxImageWidth": 4096,
        "maxImageHeight": 4096,
        "maxDpi": 600
      }

- **XML:** XML from requests (ArcXML, WFS POST, ``SLD_BODY``, OGC filters) and from responses of
  remote services is read without DTD processing: no entity expansion, no external resources.

.. _security-server-sql:

Database and SQL filters
------------------------

**Database user with as few rights as possible:** the *gView Server* accesses the database with
the user of the services' connection. This user should only have the rights the services need:

- Read rights on the tables and views of the services.
- Write rights (INSERT, UPDATE, DELETE) only on tables edited through a *FeatureServer*.
- No owner, DDL or admin rights (CREATE, ALTER, DROP, ``sysadmin``, ``superuser``) and no access
  to other databases or schemas.

Even if a filter ever lets through more than intended, nothing can be changed or deleted and no
other table can be read.

**Filters in requests:** filters sent by a client are checked before the query:

- GeoServices ``where``, ``layerDefs`` (export, identify) and
  ``dynamicLayers[].definitionExpression``, ArcXML ``where``, GeoJsonService ``where``.
- Allowed are fields of the layer, text, number and date literals (``date '2020-01-01'``,
  ``timestamp '...'``), ``NULL``, ``TRUE``/``FALSE``, comparisons (``= <> != < > <= >=``),
  ``AND``, ``OR``, ``NOT``, ``IN``, ``LIKE`` (with ``ESCAPE``), ``IS [NOT] NULL``, ``BETWEEN``,
  parentheses and ``+ - * /``.
- Allowed functions: ``UPPER``, ``LOWER``, ``CHAR_LENGTH``, ``TRIM``, ``CAST``, ``EXTRACT``,
  ``CURRENT_DATE``, ``CURRENT_TIMESTAMP``.
- Everything else (subqueries, ``UNION``, ``;``, comments, other functions, unknown fields) is
  refused with status 400. The message names the refused part, not the SQL. ``1=1`` (Esri
  clients) stays allowed.
- Sorting (GeoServices ``orderByFields``, WFS ``SortBy``) only ``field [ASC|DESC]``, field
  lists (``outFields``, ArcXML ``subfields``, fields of renderers from ``dynamicLayers`` and WMS
  ``SLD_BODY``) only fields of the layer.
- WFS filters are written from the XML as new SQL (texts escaped, numbers checked).

The filter of a layer from the service configuration is not checked; it is always combined with
the filter from the request by ``AND``.

More functions can be allowed:

.. code-block:: javascript

   {
        "security": {
            "sql": {
                // in addition to the default functions, function names only
                "allowed-functions": [ "ROUND", "SUBSTRING" ]
            }
        }
   }

.. note::
   Since 8.26.4001, ``layerDefs`` and ``definitionExpression`` are checked, too. Clients using
   other functions or subqueries there now get an error. Add the functions they need to
   ``security:sql:allowed-functions``.

.. _security-server-account:

Service account and file permissions
------------------------------------

Do not run the *gView Server* as administrator, ``root`` or ``LocalSystem``, but with its own
service account (IIS: app pool identity, Windows service or Linux: a dedicated user, Docker: user
``app``). The account needs:

- **Read:** the program directory, ``_config`` and the data of the services (files, rasters,
  FDBs).
- **Write** only to these folders (from ``_config/mapserver.json``):

  - ``output-path``: map images delivered as url.
  - ``tilecache-root``: tile cache, vector tile cache and glyphs (``_glyphs``).
  - ``services-folder``: ``services`` (publishing, deleting, settings of services),
    ``_login`` (logins, tokens, security stamps, the certificate that signs the tokens),
    ``log`` (service logs) and ``networks`` (network build; or ``networks-folder``).
  - Data edited through a *FeatureServer* or OGC API Features (file based sources such as FDB,
    GeoPackage, shape).

- **No** write permissions on the program directory and ``_config``: on the first start the
  server creates ``_config/mapserver.json`` if it is missing. Grant write permissions once or
  create the file beforehand.

``_login`` contains password hashes and the certificate that signs the tokens: only the service
account and admins may read this folder. Do not put any of these folders below a directory the
web server delivers directly (except ``output-path``).

For database access see :ref:`security-server-sql`.

Logging
-------

Security events are logged with the category ``gView.Server.Security``: failed and refused
logins, lockouts, refused admin requests, the admin setup, *Sign out everywhere* and
configuration warnings. The client ip and the username are logged, passwords never. Use the
category to filter the log or to forward it (fail2ban, SIEM).

Tokens, passwords, secrets, url tokens and ``Authorization`` values are masked (``***``) in the
gView service logs (``Logging:LogServiceRequests``/``...Details``). The ASP.NET request log
(``Microsoft.AspNetCore.Hosting``) is not masked: keep it at ``Warning`` (the default in
``appsettings.json``), otherwise query strings with tokens are written in plain text.

**Error messages to clients:** the services do not pass internals (paths, connection data, SQL,
database messages, stack traces) to clients. Unexpected errors are answered with
``internal server error (id: 7f3a9c21)``; map images and tiles show the same text. The full
message is written with the same id to the error log of the service
(``Logging:LogServiceErrors``, default ``true``, folder ``log``), for the GeoJson service and OGC
API to the server log. Errors caused by the request itself (unknown layer, invalid filter, ...)
are still reported with their text. The admin pages show the details.

.. _security-server-updates:

Installing updates
------------------

- **gView Server:** new versions contain updated libraries. Install releases with a *Security*
  section in the changelog promptly. Before every release the NuGet packages are checked for
  known vulnerabilities.
- **.NET runtime:** the regular ZIPs use the installed ASP.NET Core runtime. Keep it up to date
  with Windows Update or the package manager. The *portable* ZIPs include the runtime and are only
  updated with a new gView release.
- **Docker:** the images are based on ``mcr.microsoft.com/dotnet/aspnet``. Pull them with every
  release; rebuild your own images based on ``gview-server`` regularly.
- **GDAL** is not included (``GVIEW_GDAL_PATH``, program directory or system). Keep your own GDAL
  installation up to date. The other native libraries (Skia, HarfBuzz, SQL client) come with the
  gView release.


Upgrading to v9
---------------

- **Sessions and tokens:** tokens issued before v9 have no security stamp and are rejected.
  Admins and token users log in again, GeoServices clients request a new token.
- **Admin pages from outside:** only from ``security:manage:allowed-networks`` (default: private
  networks). Enter your networks or use OIDC; ``"*"`` only if the server is not reachable from
  the internet.
- **Proxy on a public address:** enter it in ``security:known-proxies``, otherwise the server
  does not see the client ip and https.
- **First admin:** a fresh installation needs ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD`` or
  the setup code from the log.
- **WFS:** ``GetFeature`` returns at most ``MaxRecordCount`` features.
- **Dpi:** map images with more than 600 dpi (e.g. printing) are limited to 600 dpi. If more is
  needed, raise ``mapserver-defaults:maxDpi``.
- **Docker:** the images run as user ``app`` (UID 1654). Mounted directories must be writable for
  this user (see :doc:`run-docker`).
- **Error messages:** unexpected errors are answered with ``internal server error (id: ...)``
  instead of the internal message (see `Logging`_). The details are in the log. FeatureServer
  edits also report database errors with this id only.
