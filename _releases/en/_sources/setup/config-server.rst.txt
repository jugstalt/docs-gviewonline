.. _config-server:

Configuration gView.Server
==========================

The **gView.Server** can be configured via the file ``_config/mapserver.json``:

.. code-block:: javascript

   {
        // Folder, where gView Server stores Services, Logins, etc
        "services-folder": "{repository-path}/server/configuration",

        // Path, where gView Server stores Map Request Images
        "output-path": "{repository-path}/server/web/output",
        // Url to the Path, where gView Server stores Map Request Images
        "output-url": "{server-url}/output",

        // Path, where gView Server stores Tiles
        "tilecache-root": "{repository-path}/server/web/tile-caches",

        // The Task Queue
        "task-queue": {
            // Indicates how many requests can be processed at the same time
            "max-parallel-tasks": 50,
            // The maximum length of the queue
            "max-queue-length": 1000
        },

        // Whether clients are allowed to log in through the web interface
        "allowFormsLogin": true,
        // It can be assumed that all calls are made over HTTPS
        "force-https":  false,

        // optional: graphic engines. gdiplus only works on windows systems
        "graphics": {
            "rendering": "skia",  // engine for rendering: skia|gdiplus 
            "encoding": "skia"  // engine for encoding images (jpg|png): skia|gdiplus 
        },


        // Optional Settings
        "Facilities": {
            "MessageQueue": {
                "Type": "messagequeue-net",
                "ConnectionString": "http://localhost:9091",
                "Namespace": "development"
            }
        }
    }

In the first section, paths and URLs are defined. These are initially set up via the ``gview.deploy``
tool and can easily be adjusted here.

Under ``task-queue``, it can be specified how many requests can be processed simultaneously.
If sufficient RAM is available, this value can be increased arbitrarily. However, one must consider
that the server generates map images (bitmaps) which can require a significant amount of RAM. If you are 
consistently reaching the limit of RAM usage (due to many requests), the value can also be set lower.
If all tasks are occupied, a request goes into the queue. The length of this queue can also be
adjusted here.

In the section ``graphics``, the *Graphic Engine* can be specified. This can either be ``skia`` or
``gdiplus`` (on Windows platforms). However, ``gdiplus`` is becoming obsolete and 
only works if the application is run on a Windows system.

Under ``Facilities``, further optional settings can be made.

One option is ``MessageQueue``. Currently, only a message queue of the 
type ``messagequeue-net`` can be specified (https://github.com/jugstalt/messagequeuenet).
A message queue makes sense when the *gView.Server* is run in multiple instances simultaneously,
for example, when *gView.Server* is running in Kubernetes with several *Replicas*.
The message queue allows the individual components to communicate with each other, for instance, 
when a map service is modified or deleted. This keeps all instances up to date.
With ``Namespace``, a namespace can be specified here, which defines which instances belong together.
Since instances from both ``Staging`` and ``Production`` can access the same 
MessageQueue API, the namespace can differentiate which instances really 
belong together.





