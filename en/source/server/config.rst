Configuration
=============

The gView Server can be configured from the _config/mapserver.json file:

.. code-block:: javascript

   {
        // Folder, where gView Server stores Services, Logins, etc
        "services-folder": "C:\\gView5\\Server\\Services",

        // Path, where gView Server stores Map Request Images
        "output-path": "C:\\IIS\\Output",
        // Url to the Path, where gView Server stores Map Request Images
        "output-url": "http://my.server.com/output",

        // Url with which gView Server can be reached via Internet
        "onlineresource-url": "https://my.server.com/gview5-server",

        // Path, where gView Server stores Tiles
        "tilecache-root": "C:\\data\\tilecache",

        // The Task Queue
        "task-queue": {
            // Indicates how many requests can be processed at the same time
            "max-parallel-tasks": 5,
            // The maximum length of the queue
            "max-queue-length": 1000
        },

        // Whether clients are allowed to log in through the web interface
        "allowFormsLogin": true,
        // It can be assumed that all calls are made over HTTPS
        "force-https":  false
    }


If you start the viewer as described in the installation procedure in *standalone* mode, this file, if not yet
existing at the first start automatically generated. The output directories are stored in the folder ''server-files'' parallel to the
folder ``server``.

The starting point for the default configuration is the file ``_config/_mapserver.json`` which is included in the installation package.
This is a template file with placeholders (``{installation-path}`` and ``{installation-host}``). The placeholders
are replaced at the first start and a ``_config/mapserver.json`` file is generated.

This mechanism can also be used on Linux or within *Docker containers*. For this, only the appropriate
Environment variables (``GV_REPOSITORY_PATH`` and ``GV_ONLINERESOURCE_URL``). An image and a
description of this can be found under https://hub.docker.com/r/gstalt/gview5-server

.. note::
    Map images are stored in the output directory and then picked up by the client there. The *gView Server* must have write rights
    for this directory. The client must reach the url specified here. As a server that delivers the images,
    any existing Http server can be used (IIS, Nginx, ...). There is also the possibility to use *gView Server* for 
    delivery of the map images. To do this (as in the default configuration), the url to the *gView MapServer* must be the ``output-url``,
    e.g: ``https://gview.my-server.com/output`` oder ``https://www.my-server.com/gview5/output``

