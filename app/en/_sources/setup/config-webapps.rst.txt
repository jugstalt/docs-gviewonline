.. _config_webapps:

Configuration of gView.WebApps
==============================

The *gView.WebApps* application can be configured via the file ``_config/gview-webapps.config``:

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

Custom Drives/Directories
-------------------------

In the ``Drives`` section, paths can be specified that are listed under ``Computer`` 
in **gView.DataExplorer**. If this section is omitted, all available drives will be displayed.

.. note::

    The paths and drives refer to the computer/server on which **gView.WebApps** is installed.

.. note::

    If **gView.WebApps** is installed on a server, it is **strongly** recommended to maintain this 
    section. For security reasons, access should not be allowed to the entire filesystem. 
    Otherwise, a potential attacker could theoretically access all files on the server.

Within a path, a placeholder ``{{username}}`` can be used. This will be replaced with the 
username of the user currently logged into **gView.WebApps**. This allows a user, for example, 
to store MXD files in a *private* directory.

Custom Tiles
------------

In the ``CustomTiles`` section, you can create additional tiles that are displayed on the 
homepage of **gView.WebApps**. This allows, for example, the creation of tiles linking to 
one or more **gView.Server** instances.

Each tile has the following properties:

* **Title**, **Description:** title and description of the tile.
* **TargetUrl:** the URL opened when the tile is clicked.
* **BackgroundColor**, **FontColor:** optional, background and font colour of the tile
  (e.g. ``#0f766e``).
* **Visibility:** optional, who sees the tile:

  * ``admin`` (default): *admin users* only
  * ``user``: all signed in users with a *gView* role (*carto users* and *admin users*)
  * ``public``: everyone, including visitors who are not signed in

.. _config_webapps_home:

Start Page
----------

Visitors who are not signed in see a **Sign in** button on the start page and the applications
*gView Carto* and *gView Explorer* as locked tiles. A click on a locked tile leads to the login
and then directly into the application. Signed in users without a *gView* role (e.g. with
``oidc`` a user without ``RequiredUserRole`` and ``RequiredAdminRole``) get a hint that they
are missing the permission.

The optional ``Home`` section adapts the start page to your organisation:

* **Title:** heading of the start page (default: ``gView GIS``).
* **WelcomeText:** welcome text below the heading. Plain text, line breaks (``\n``) are kept.
  This is a good place to explain how to sign in.
* **LogoUrl:** URL of a logo shown above the heading.
* **NoAccessText:** text for signed in users without a *gView* role, e.g. whom to ask for access
  (default: *Please contact your administrator.*).
* **Links:** footer links, each with ``Title`` and ``Url``. When set, they replace the default
  links (documentation, GitHub).

.. code-block:: javascript

    "Home": {
        "Title": "GIS Portal",
        "WelcomeText": "Welcome to the GIS portal.\nSign in with your company account.",
        "LogoUrl": "https://my-server/logo.png",
        "NoAccessText": "Please ask the GIS team for access: gis@my-company.com",
        "Links": [
            { "Title": "GIS manual", "Url": "https://my-server/gis-manual" }
        ]
    }

.. note::

    The fixed texts of the start page are English. Texts in your own language can be set with
    ``Title``, ``WelcomeText`` and ``NoAccessText``.

.. note::

    Without an authentication (see below) every user sees the start page as *admin user*. If
    **gView.WebApps** is not called via ``localhost``, the start page shows a warning in the
    **System** section.

Publishing Maps
---------------

The `Publish` section allows you to specify which **gView.Server** instances
maps can be published to directly. Multiple instances can be defined under `Servers`:

* **Name:** Any name that describes the respective instance.

* **Url:** The URL to the **gView.Server** instance.

* **Client:** A *client* is necessary for publishing services. Here the name of the
  *client* is specified. This option can also be omitted. The user must then enter the
  *client* manually during publishing.

* **Secret:** The *secret* for the *client*. This parameter is also optional. If nothing
  is specified here, the user must enter the *secret* during publishing.

.. note::

    If a *secret* is specified, it must be entered in plain text in the `gview-webapps.config`.
    To avoid this, it is good practice to only specify the *client* and omit the *secret*.
    The user must then know the *secret* and specify it during publishing.

.. note::

    To later publish services via **gView.Carto**, one must be logged in as an **admin** user.
    The `Publish Map` tool is not offered to **carto** users.

Further configurations of the **gView.Server** are necessary to be able to publish.
It is recommended to configure this section only after the following sections have
been read:

:ref:`publish-map-service-example`

:ref:`config-server`


Authentication
-----------------

To determine how one can/must sign in to **gView.WebApps**, the ``Authentication`` section is used.

.. note::

    If this section is omitted, no authentication occurs. Every user can do everything. However, this should only be possible for local installations.
    If **gView.WebApps** is running on a server, it is **absolutely necessary** to set up an authentication method.

**gView.WebApps** distinguishes two categories of users:

* **Admin-User:** Users who are allowed to use all applications and tools.
* **Carto-User:** Users who are only allowed to use the *Carto* application. These users can only create and save maps. They can access predefined database connections to incorporate geo-data into the map. However, unlike *Admin-Users*, they cannot view or modify the *Connection String*.

Currently, the following authentication methods are available:

* ``forms``: Users can log in using a username and password via a login form.
  The list of users is set directly in the ``gview-web.config``.
  This is a simple-to-maintain form of authentication that 
  often suffices for small teams. This method does not provide advanced security policies
  and should be used only for applications within an intranet or confined areas.

  Deploying **gView.WebApps** over the Internet should not be considered with this method.

* ``oidc``: OpenID Connect is another method of authentication. In this case, 
  users log in through an external authentication service. This generally offers higher security,
  including two-factor authentication, etc.


Forms Authentication
++++++++++++++++++++

Here, the value ``forms`` is specified as the ``Type``. In the ``Forms`` section, 
multiple ``AdminUsers`` and ``CartoUsers`` can be specified, each as an array of objects
with the properties ``Username`` and ``PasswordHash``.
To avoid specifying the password in plaintext, a hash value in hexadecimal format is used instead.
This hash can be generated using the ``SHA256`` or ``SHA512`` algorithm.

.. note::

    There are online tools available for calculating these hash values, e.g.:

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

OpenID Connect Authentication
+++++++++++++++++++++++++++++

If an *Identity Service* that supports *OpenID Connect* is available, it can be used for
authentication.

The value ``oidc`` must be entered as the ``Type``. In the ``Oidc`` section,
the *Identity Server* (``Authority``) must be specified. On the *Identity Server*,
*gView.WebApps* must be added as a client. The respective ``ClientId`` and
``ClientSecret`` must also be entered here. The following values are recommended for ``Scopes``:

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

Since the *Identity Server* also provides roles, a specific role for 
**Admin-User** and **Carto-User** must be specified. This is done through the parameters 
``RequiredUserRole`` (for Carto-User) and ``RequiredAdminRole`` (for Admin-User).

RasterNet raster data source
----------------------------

``_config/gview-webapps.config`` can contain a ``rasternet`` section as well. It applies to *gView.DataExplorer*
and *gView.Carto* and has the same entries as for *gView.Server* (see :ref:`gView.Server configuration, rasternet section <config-rasternet>`):

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
