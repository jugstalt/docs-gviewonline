Running on Linux
================

If **gView** was installed using the *Deploy Tool*, navigate to the appropriate directory,
e.g. ``/home/{user}/apps/gview-gis/local/6.24.1801``.

The following files are located there:

.. code-block:: bash

   myuser@JG-SP8:~/apps/gview-gis/local/6.24.2101$ ls -ls
   total 48
    4 -rwxr-xr-x  1 myuser myuser   119 May 22 08:29 gview-server.sh
    4 -rwxr-xr-x  1 myuser myuser   121 May 22 08:38 gview-webapps.sh
   20 drwxr-xr-x 55 myuser myuser 20480 May 22 10:44 server
   20 drwxr-xr-x  6 myuser myuser 20480 May 22 10:44 webapps

.. note::

    If the shell scripts are not marked as *executable*, this can be corrected with the 
    following command:

    .. code-block:: bash

        sudo chmod +x gview-server.sh
        sudo chmod +x gview-webapps.sh

The two scripts start **gView.Server** and **gView.WebApps**, respectively.

.. code-block:: bash

    ./gview-server.sh
    ./gview-webapps.sh

The output will indicate which port the applications are running on, usually:

* gView.Server: http://localhost:45622
* gView.WebApps: http://localhost:45623

The applications can now be started via a browser with the respective URL.

If you want to make the applications accessible over the web, this can be achieved 
through an upstream **NGINX** configuration.
