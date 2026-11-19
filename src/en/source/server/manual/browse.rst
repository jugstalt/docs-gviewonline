Browse Services
===============

This section demonstrates how *gView MapServer* lists services and directories in the 
web interface. To list services, the menu item ``Browse Services`` is available 
(tile on the homepage or sidebar):

.. image:: img/browse1.png 

The initial view at ``Browse Service`` looks approximately like this:

.. image:: img/browse2.png 

**Folders** are displayed in bold. Services are displayed in regular font. Moreover, 
there is an option to delete services using the trash bin icon.
This is only displayed if the logged-in user/client is authorized to do so.

.. note::
   ``Browse Services`` is possible for every user (even without logging in). However, only 
   those services for which the current user is authorized are listed.

.. note::
   In this example, the administrator is still logged in. Therefore, the options ``Publish Service`` 
   and ``Create Folder`` are also available. The procedure for publishing is discussed 
   under :ref:`publish-map-service-example` in the section :ref:`examples`.

.. note::
   In this example, the service ``ortsplan`` is not located in a directory but in the 
   so-called **root** area. In practice, services should always be organized in directories!

Clicking on a service in this view displays all possible interfaces for that service:

.. image:: img/browse3.png 

For each of these interfaces, links are provided that can be used to integrate these services 
into various *GIS programs*. For example, clicking on a link for WMS displays the 
*Capabilities Request* for that service in the browser:

.. image:: img/browse4.png 

This link could also be integrated into **QGIS** as a *WMS Layer*.

Which interfaces are offered here can be determined via the *Manage Web Interface* in
the permissions for the service or directory.
