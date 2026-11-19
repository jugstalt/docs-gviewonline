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
above). The first login follows the same flow described in :doc:`run-local`: the first user
created automatically becomes the administrator.


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
