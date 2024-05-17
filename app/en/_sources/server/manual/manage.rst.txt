Managing gView Server
======================

An administrator account has already been set up under :ref:`server_postinstallation` for managing the *gView MapServer*.

With this user, you can:

- create new directories
- publish services via the web interface
- stop and restart services
- display service log files
- create clients.

Logging in as Administrator
---------------------------

Login is done via the ``Manage`` link from the sidebar:

.. image:: img/manage1.png

Manager Interface
-----------------

Once logged in as an administrator, the manager interface appears approximately like this:

.. image:: img/manage2.png 

On the left, the directories (Folders) in which services are organized are displayed.
Next to them, the services located in these directories are shown.


Creating Clients
----------------

**Clients** are known *Web Applications* that access the services on the **gView MapServer**.
For instance, if a WebGIS application accesses services, a *client* can be created for it.
Each **client** has a **secret** to log in to the **gView MapServer** when accessing it.
**Client** and **secret** are similar to **user** and **password**, except that
a **client** is typically not a physical person but an application.

.. note::

   If you want to give only certain users access to specific services later, there must be
   a separate authorization layer in the respective **Client** software (WebGIS).

In the manager interface, you can switch between ``Services`` and ``Security`` at the top. 
Under Security, new *Clients* can be created or existing *Secrets* modified:

.. image:: img/manage3.png

Service Permissions
-------------------

In general, services with similar characteristics (production/test, special permissions) 
should be organized within **Folders**. 
Permissions can then be set at the *Folder* level and apply to all services 
within the **Folder**. Alternatively, permissions must be set individually for each service.

.. note::
   Permissions are organized hierarchically. If a right is not possible over the *Folder*, 
   it is also not available for a service within this *Folder*. 
   Additional permissions for a service within a group can only further restrict access.

Whether permissions have been set for a service or a **Folder** can be recognized by the 
*lock icon*. If the lock is open, no permissions have been set yet. 
All services are then available to every user.

For example, clicking on the *lock icon* for a **Folder** opens the following dialog:

.. image:: img/security1.png 

Here, no permissions have been set yet, meaning every user/client can use all services 
in this *Folder* without restriction.


.. note::
   An exception is the *Publish Service* right, which can only be assigned to a client.
   Only authorized clients and the administrator can publish services.

* **Export Map**: Map images can be retrieved for any area.
* **Query**: Geo-objects can be queried and searched.
* **Features**: Geo-objects can be downloaded or edited as features.
  This requires the *GeoServices REST* interface. The geo-objects that can be edited are set
  in the map project (MXL) through *gView Carto* in the *Ribbon* under *Options*.
* **Publish Service**: Services can be published and deleted for this directory.
* **Interfaces**: Permissions can be restricted for specific interfaces (WMS, etc.).

In the selection list, an existing client can be chosen. A special case is the 
client ``_anonymous``, which is always automatically offered in the selection list.
This *client* is always used for authorization when no 
login is performed by the *client* application. For this *client*, the following can be set:

.. image:: img/security2.png 

This means that an **anonymous client** is allowed to fetch map images and query geo-objects for the services in this **folder**. However, only the 
``WMS`` and ``WMTS`` interfaces are available for the services.

In the next step, clients can be granted more specific rights:

.. image:: img/security3.png 

As visible above, additional values for the folder can be entered in this dialog 
(``Online Resource (override)`` and ``Output Url (override)``).
This can override the values from ``_config/mapserver.json``. This can be useful when
a server is accessible from the internet via different URLs or when services of a 
**folder** are published via a proxy. Typically, these values can be left empty.

Service Status
-------------------

Services can have a specific status during runtime. This is visible in the *Manage* 
interface in the service list (see above):

* **Idle**: The service appears "white" in the list. The service is available, 
  but has not yet been started/called by a client.
* **Running**: The service has been initialized and is running (green).
* **Stopped**: The service has been stopped by the administrator. It is no longer 
  visible to clients. In the Manage interface, the service is displayed transparently.

Additional colors:

* **Red**: The service has caused errors since the last start.

Each service also has command buttons available that can influence the status, for example:

.. image:: img/status1.png 

* **Logs**: Display (Error) logs for this service
* **Security**: Set permissions for this service
* **Start**: Start the service (if stopped)
* **Stop**: Stop the service (afterwards, it is no longer visible to clients)
* **Refresh**: Forces a restart of the service



