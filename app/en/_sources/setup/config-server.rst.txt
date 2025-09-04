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

        // default value for mapserver and services
        "mapserver-defaults": {
            // maximum width in pixel of an map image
            "maxImageWidth": 4096,
            // maximum height in pixel of an map image
            "maxImageHeight": 4096,
            // maximum records that will be returned from a query
            "maxRecordCount": 1000
        },

        // Whether clients are allowed to log in through the web interface
        "allowFormsLogin": true,
        // It can be assumed that all calls are made over HTTPS
        "force-https":  false,

        // the level that causes an error on load and deploy services
        "CriticalErrorLevel": {
            "ErrorType": "error"  // any, warning, error, critical, never (default: error)
        },

        // optional: graphic engines. gdiplus only works on windows systems
        "graphics": {
            "rendering": "skia",  // engine for rendering: skia|gdiplus 
            "encoding": "skia"  // engine for encoding images (jpg|png): skia|gdiplus 
        },

        // optional: projection engine        
        "proj-engine": {
            "engine": "ManageProj4Parallel" // ManagedProj4, NativeProj4
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

In the section ``mapserver-defaults``, *default values* for map services can be specified.
If these values are not explicitly set for a service, the *default value* configured here will be used. 
This refers to the maximum size of a map image in pixels that can be retrieved by a client. 
It can also be specified how many geo-objects can be retrieved in a query at most.
If the section or individual values are not specified, the values mentioned above apply.

.. note::

    The values specified here are *default values* and can be overridden for each service via the 
    *Map Service Setting* in *gView.Carto*. Higher values than the *default values* can also be 
    set for individual services. The values in this configuration are not *global maximum values*.

.. note::

    A client can request a map image larger than the maximum values. The map server 
    will not return an error but instead deliver a resized image that matches the maximum values. 
    The aspect ratio and geographic area remain intact. However, the resolution of the image 
    (DPI value) is automatically adjusted so that the scale corresponds to the originally 
    requested map image.

    A client can verify whether the image was scaled by checking the size of the resulting image. 
    The values are also included in the *GeoServices Response* in the result JSON.


In the section ``graphics``, the *Graphic Engine* can be specified. This can either be ``skia`` or
``gdiplus`` (on Windows platforms). However, ``gdiplus`` is becoming obsolete and 
only works if the application is run on a Windows system.

In the ``graphics`` section, the *Graphic Engine* can be specified. This can either be ``skia`` or
``gdiplus`` (on Windows platforms). However, ``gdiplus`` is considered deprecated and 
only works when the application is running on a Windows system.

The ``CriticalErrorLevel`` section is optional. Here, you can specify from which
error level a service cannot be loaded or deployed. Possible values are:

- ``any``: Any error, whether a warning or a critical error, prevents a service from being loaded and deployed.

- ``warning``: From warnings onwards, a service will not be loaded or deployed.

- ``error``: From errors onwards, a service will not be loaded or deployed.

- ``critical``: Only in the case of critical errors will a service not be loaded or deployed.

- ``never``: A service will always be loaded and deployed, regardless of any errors that occur.

The ``proj-engine`` section is optional. Here, you can specify which *Proj4 Engine* to use:

- ``ManageProj4Parallel``: Uses the Proj4 port in C#, where transformations are parallelized 
  for objects with many vertices (>100) to improve performance.

- ``ManagedProj4``: Uses the Proj4 port in C#.

- ``NativeProj4``: Uses the native C++ Proj4 library.

One of the managed versions is recommended, as they are platform-independent and 
no longer suffer from performance drawbacks. The native version can be used 
if the managed version does not produce the correct results.

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





