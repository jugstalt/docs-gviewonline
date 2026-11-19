.. _commandline-tools-render-tile-cache:

gView.Cmd RenderTile
====================

This tool sends the request to compute *TileCache* tiles to the *gView Server*. 
The respective service must be authorized via its metadata in *gView Carto* for the provision of *Tiles*.

A call to the *WMTS-Capabilities* (for example, through the gView Server interface) must be possible.

.. note::

    The following commands are shown in **interactive mode**. For this, ``gView.Cmd.exe -i``
    must be called. Without the *interactive mode*, ``gView.Cmd.exe --command`` would have to be 
    prefixed to each command shown here.

There are three ``TileCache`` commands:

.. code-block:: batch

  TileCache.Render:        Forces a gView Server instance to render service a tile cache
  TileCache.Info:          Shows information about a tilecache service
  TileCache.ClipCompact:   Clips a tile compact cache by polygon(s)

TileCache.Info
--------------

This only outputs information about the TileCache service. This can be useful 
to verify if a service can be used as a *Tile Cache*.

.. code-block:: batch

   Command:>TileCache.Info --help
   Help: TileCache.Info
   Shows information about a tilecache service
   Usage:
      -server: gView Server Instance, eg. https://my-server/gview-server
      -service: The service to pre-render, eg. folder@servicename

   Command:>TileCache.Info -server https://localhost:44331 -service cache/ortsplan
    TileSize [Pixel]: 512 x 512
    ImageFormats: png
    Scales:
      1 : 1000000
      1 : 500000
      1 : 250000
      1 : 100000
      1 : 50000
      1 : 25000
      1 : 10000
      1 : 5000
      1 : 2500
      1 : 1000
    Origin: upperleft
      EPSG:31256 upperleft: -5622500, 5001000
    BBox:
      EPSG:31256: -226900, 163300, 0, 315500
   
Additionally, all information that can be useful for optional parameters for rendering 
is returned here. If the service is not available as a *Tiling Service*, the output 
would be approximately as follows:

.. code-block:: batch

   Exception:
   Can't read metadata from server. Are you sure taht ervice is a gView WMTS service?

TileCache.Render
----------------

This command triggers the actual rendering of *Tile Cache* tiles. Optional parameters are used to further specify
what should be rendered. Only commands that prompt rendering are sent to the *gView Server*.
The *rendering* occurs on the *gView Server*. There, the tiles are created and stored in the file system.

.. note::
   If a tile already exists, it will not be recalculated by the server.
   The server only calculates tiles that do not yet exist. This makes sense if only some
   tiles of a *Tile Cache* need to be recalculated.
   Here, affected *Tiles* should first be deleted from the file system
   (e.g., with the command ``TileCache.ClipCompact``)

Optional Parameters:

* ``-epsg``: The *gView Server* can offer Tile Caches in different coordinate systems for a service.
  Which coordinate systems are possible can be set in the metadata in *gView Carto*.
  The possible values can be displayed using the ``-info`` command shown above.

* ``-compact``: This option creates a *Compact Tile Cache*. Unlike a
  *classic Tile Cache*, not every tile is stored in a separate file. Here, always
  128 x 128 tiles are combined into one file in the FileSystem. This requires less space in the file
  system and can be especially easier to copy (since a classic Tile Cache
  often consists of millions of files. A large file is generally easier for the file system to
  handle than many small individual files). However, each individual tile must be extracted from the large files
  on subsequent requests (which usually happens very quickly).

* ``-orientation``: This specifies the orientation of the *Tile Cache* (or the location of the origin).
  *Tile Caches* with the orientation *lower left* cannot be used with WMTS,
  therefore this option is only offered for completeness and can generally be omitted.

* ``-bbox``: A *Bounding Box* (in the respective coordinate system) can be specified. Only for this
  area will the rendering commands be sent to the server.

* ``-scales``: A list of scales (comma-separated) for which commands are sent to the server.

* ``-threads``: To speed up the creation of the Tile Cache, multiple commands can be sent simultaneously
  to the *gView Server*. Otherwise, only the command for one tile is sent to the server at a time.
  It does not make sense to specify extremely high values here.
  Rule of thumb: ``-threads`` = number of processors. If the processor load does not increase significantly,
  it means that most of the rendering time is spent waiting for the database.
  In this case, the value here can also be increased.

Example:

.. code-block:: batch

   Command:>TileCache.Render -server https://localhost:44331 -service cache/ortsplan -compact -scales 1000000,500000,250000,100000,50000,25000,10000,5000 -threads 10

