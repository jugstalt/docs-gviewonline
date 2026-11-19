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
- Enter the addresses of your proxies in ``security:known-proxies`` (or ``known-networks``).
- Keep the admin pages in your internal network (``security:manage:allowed-networks``, the
  default) or use an OIDC admin login.
- Set ``force-https`` when the server is only reachable via https.
- Create the first admin with ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD`` or the setup code
  from the log before the server is reachable from outside (see :ref:`server_postinstallation`).

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
                "session-hours": 8
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
- ``"*"`` allows everywhere.
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

Upgrading to v9
---------------

- **Sessions and tokens:** tokens issued before v9 have no security stamp and are rejected.
  Admins and token users log in again, GeoServices clients request a new token.
- **Admin pages from outside:** only from ``security:manage:allowed-networks`` (default: private
  networks). Enter your networks, ``"*"`` or use OIDC.
- **Proxy on a public address:** enter it in ``security:known-proxies``, otherwise the server
  does not see the client ip and https.
- **First admin:** a fresh installation needs ``GVIEW_ADMIN_USER``/``GVIEW_ADMIN_PASSWORD`` or
  the setup code from the log.
- **WFS:** ``GetFeature`` returns at most ``MaxRecordCount`` features.
