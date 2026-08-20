.. _gview_server_cli:

gView.Server Command Line (Offline Mode)
==========================================

In addition to normal server operation, **gView.Server.exe** can also be called directly with
command line parameters to manage services without starting the HTTP server. This is particularly
useful for *offline* scenarios, for example when services should be published, removed, or queried
automatically as part of a deployment or script.

All calls described below read the complete server configuration
(``_config/mapserver.json``, plugins, services path, see :ref:`config-server`) in the same way as
a normal server start. However, **no** HTTP listener is started, i.e. no port is bound. The process
exits after the action with exit code ``0`` (success) or ``!= 0`` (error, the message is written to
``stderr``).

.. note::

   ``--service`` is specified for all commands in the format ``folder/servicename`` (or just
   ``servicename`` for a service in the root directory). For ``--remove``, ``--set-metadata`` and
   ``--get-metadata``, the specified ``folder`` must already exist as a directory under the
   configured services path – the same rule applies when publishing via the web interface or HTTP.

--publish – Publish a Service
-------------------------------

.. code-block:: batch

   gView.Server.exe --publish --mxl <path-to-mxl> --service <folder/servicename>

Validates the given MXL, renames the first map it contains to ``folder/servicename``, and writes
``.mxl`` and ``.meta`` to the configured services path.

--remove – Remove a Service
------------------------------

.. code-block:: batch

   gView.Server.exe --remove --service <folder/servicename>

Deletes the ``.mxl``, ``.svc``, and ``.meta`` files of the specified service from the services
path.

--catalog – List Services
----------------------------

.. code-block:: batch

   gView.Server.exe --catalog [--format text|xml|json]

Lists all registered services (root level plus one folder level). Without ``--format``, the
output is text (``folder/name (Type)`` per line).

--get-metadata – Read Metadata
---------------------------------

.. code-block:: batch

   gView.Server.exe --get-metadata --service <folder/servicename> [--out <path>]

Outputs the service's ``.meta`` XML. With ``--out``, the output is written to a file instead. An
empty result means that no service or no metadata exists (not an error, exit code ``0``).

--set-metadata – Set Metadata
--------------------------------

.. code-block:: batch

   gView.Server.exe --set-metadata --service <folder/servicename> --metadata <path-to-xml>

Writes the specified XML file as ``.meta`` for the service and reloads the service afterwards.
