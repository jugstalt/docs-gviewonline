Configuration
=============

Each application (**gView.WebApps** and **gView.Server**) has files in the ``_config`` directory
for configuration.

However, it is not good practice to directly modify the files there. When updating 
via the ``gview.deploy`` tool, a new directory is created for each new version with the 
version name as its name. If you have modified the configuration, you would have to transfer
the configuration files to the new version every time.

To avoid this, modify the configuration in the 
``_deploy_repository/profiles/{profile}/[web|server]/override`` directory. 
This is located where the ``webgis.deploy`` tool is also found.

If you modify the files there, they will be overwritten with each update. The files stored there will also be overwritten if the version being deployed already exists.
In this case, only the ``override`` files are published.

.. note::

    Changing the configuration files (``mapserver.json``, ``gview-web.config``) only takes effect 
    after the application has been restarted.
    In IIS, for example, the corresponding *Application Pool* needs to be restarted.

The configuration files in the ``override`` directories contain placeholders, for example:


.. code-block:: javascript

    {
        "RepositoryPath": "{repository-path}/gview-web",
    }

The placeholders correspond to the values specified when creating the profile in ``gview.deploy``.
For consistency, these placeholders should be preserved when modifying the configuration. For example, 
``{repository-path}`` appears in several configuration files (``mapserver.json``, ``gview-web.config``, ...). 
If you want to relocate the root directory for the *gView Repository* to another location,
you would need to update all the configuration files accordingly.

The best practice is to change the values in the file 
``_deploy_repository/profiles/{profile}/deploy-model.json``:

.. code-block:: javascript

    {
        "ProfileName": "local",
        "TargetInstallationPath": "C:\\apps\\gview-gis",

        // Placeholder: {repository-path}
        "RepositoryPath": "C:\\apps\\gview-gis/local/gview-repository",
        
        // ...
    }

.. note::

    The names of the properties here are not exactly the same as the names of the placeholders.
    The properties are in *PascalCase*, while the placeholders are usually in *kebab-case*.
    However, it should be possible to deduce from the names which placeholder is being referred to.


.. toctree::
   :maxdepth: 2

   config-webapps
   config-server


