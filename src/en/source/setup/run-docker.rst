Docker
======

Both *gView.Server* and *gView.WebApps* are available as ready-to-use Docker images. This allows
the applications to be run containerized, e.g. with Docker Compose or Kubernetes.

.. note::

    The images are no longer published on Docker Hub (``docker.io/gstalt/...``), but on the
    GitHub Container Registry under ``ghcr.io/jugstalt/...``. References to
    ``docker.io/gstalt/...`` are outdated and should no longer be used.


gView.Server Image
-------------------

.. code-block:: bash

    docker pull ghcr.io/jugstalt/gview-server:latest

The container can be started directly:

.. code-block:: bash

    docker run -d -p 45622:8080 --name=gview-server \
        -e GV_REPOSITORY_PATH=/etc/gview-repository \
        -e GV_ONLINERESOURCE_URL=http://localhost:45622 \
        ghcr.io/jugstalt/gview-server:latest

The two environment variables let you override the most important values of
``_config/mapserver.json`` without editing the file itself:

* ``GV_REPOSITORY_PATH``: Directory in which services, client data, output files, etc. are
  stored. For production use, this directory should be mounted as a volume outside the
  container so that the settings survive a container restart.
* ``GV_ONLINERESOURCE_URL``: The URL under which the server is reachable from the outside. This
  is used, for example, to deliver map images to clients via the output directory.

After startup, the management interface is available at http://localhost:45622 (in the example
above). The first administrator comes from the environment variables ``GVIEW_ADMIN_USER`` and
``GVIEW_ADMIN_PASSWORD`` or from the setup code the server writes to the log
(``docker logs gview-server``), see :doc:`security-server`. If an admin lost the phone and the
recovery codes of the two-factor login, ``GVIEW_ADMIN_RESET_2FA=<user>`` resets it at startup
(remove it afterwards).


gView.WebApps Image
--------------------

.. code-block:: bash

    docker pull ghcr.io/jugstalt/gview-webapps:latest

.. code-block:: bash

    docker run -d -p 45623:8080 --name=gview-webapps ghcr.io/jugstalt/gview-webapps:latest

The application is then reachable at http://localhost:45623.

.. note::

    No special environment variables are currently documented for the *gView.WebApps* image.
    Configuration works the same way as in the other run modes, via ``_config/gview-web.config``
    (see :doc:`config-webapps`). If this file needs to be adjusted, a customized version can be
    mounted into the container's ``/app/_config`` directory as a volume.


User and volumes
----------------

The images do not run as ``root`` but as the user ``app`` (UID 1654) of the .NET base image.
Everything the application writes at runtime must be writable for this user:

* ``/app/_config``: configuration, created from ``_setup`` on first start unless your own is
  mounted.
* ``/etc/gview/gview-repository`` (default of ``GV_REPOSITORY_PATH``) or
  ``/etc/gview-repository`` (as in the examples here): services, logins, logs, output files,
  tile caches.

In the image these directories already belong to the user ``app``, Docker volumes take that
over. For host directories (bind mounts) the owner must match:

.. code-block:: bash

    sudo chown -R 1654:1654 /path/to/gview-repository

    docker run -d -p 45622:8080 --name=gview-server         -v /path/to/gview-repository:/etc/gview-repository         -e GV_REPOSITORY_PATH=/etc/gview-repository         -e GV_ONLINERESOURCE_URL=http://localhost:45622         ghcr.io/jugstalt/gview-server:latest

In Kubernetes this fits ``runAsNonRoot: true`` and ``runAsUser: 1654`` (see
:doc:`run-kubernetes`).

.. note::

    Up to v8 the images ran as ``root``. When upgrading to v9, existing host directories must be
    made writable for UID 1654, otherwise the server cannot create anything there.


Using a custom configuration
-----------------------------

Instead of changing configuration files inside a running container, a customized
``mapserver.json`` or ``gview-web.config`` should be mounted into the container as a volume —
following the same *override* principle used for the classic deployment (see :doc:`config`):

.. code-block:: bash

    docker run -d -p 45622:8080 --name=gview-server \
        -v /path/to/my/mapserver.json:/app/_config/mapserver.json:ro \
        -e GV_REPOSITORY_PATH=/etc/gview-repository \
        -e GV_ONLINERESOURCE_URL=http://localhost:45622 \
        ghcr.io/jugstalt/gview-server:latest

.. note::

    In the past there was also a dedicated base image (``gview-server-base``) meant as a
    ``FROM`` base for your own Dockerfile, to bake configuration and fonts directly into a
    custom image. This base image is currently not published under ``ghcr.io/jugstalt``.
    For custom configuration, mounting your own files (as shown above) is the recommended
    approach.
