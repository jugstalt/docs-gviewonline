Browse Services 
===============

This section shows how to list *gView MapServer* services and folders in the Web Interface.
To list the services, the menu item ``Browse Services`` is available (tile on the start page or sidebar):

.. image:: img/browse1.png 

The start view at ``Browse Service`` looks something like this:

.. image:: img/browse2.png 

*Folders* (directories) are displayed in bold here. Services are displayed with normal font. 
In addition, there is also the possibility of deleting services via the recycle bin icon.
This is only displayed if the logged-in user/client is authorized to do so.

.. note::
   ``Browse Services`` is possible for every user (even without registration). However, only 
   those services are listed for which current users is permitted.

.. note::
   In this example, the administrator is still logged on. Therefore, there are also the 
   ``Publish Service`` and ``Create Folder`` options. The procedure for the 
   Publishing is discussed in the ref:`examples` section of ref:`publish-map-service-example`.

.. note::
   In this example, the service ``ortsplan`` is not in a folder but in the so-called 
   *root* folder. In practice, services should always be organized in folders!

If you click on a service in this view, all possible interfaces for this service 
displayed:

.. image:: img/browse3.png 

For each of these interfaces, links are offered with which these services can be integrated into various GIS programs. 
For example, if you click on a link for WMS, the browser displays
the *Capabilities Request* for this service:

.. image:: img/browse4.png 

For example, this link could also be included in **QGIS** as a *WMS layer*.

Which interfaces are offered here can be found via the *Manage Web Interface* for the authorizations
for the service or folder.