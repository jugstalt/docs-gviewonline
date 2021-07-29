.. _commandline-tools-render-tile-cache:

gView.Cmd.RenderTileCache
=========================

This tool sends the prompt to calculate *TileCache* tiles to the *gView Server*. The corresponding service must be set to deploy *Tiles* through its metadata in *gView Carto*.
It must be possible to call the *WMTS capabilities* (for example via the gView Server interface).

.. code::

   .\gView.Cmd.RenderTileCache.exe

   USAGE:
   gView.Cmd.RenderTileCache <-info|-render> -server <server> -service <service>
          optional paramters: -epsg <epsg-code>                           [default: first]
                              -compact ... create a compact tile cache
                              -orientation <ul|ll|upperleft|lowerleft>    [default: upperleft]
                              -bbox <minx,miny,maxx,maxy>                 [default: fullextent]
                              -scales <scale1,scale2,...>                 [default: empty => all scales
                              -threads <max-parallel-requests>            [default: 1]

Required parameters:

* ``-info``: Only information about the TileCache service is output here. This can be useful to check if a service is being usable as a *tile cache*.

.. code::

   .\gView.Cmd.RenderTileCache.exe -info -server https://localhost:44331 -service cache/ortsplan
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
   
It also returns any information that can be useful for optional parameters for rendering.
If the service is not available as a *tiling service*, the output is similar to the following:

.. code::

   Exception:
   Can't read metadata from server. Are you sure taht ervice is a gView WMTS service?

* ``-render``: this command triggers the actual rendering of *Tile Cache* tiles. To further specify what should be rendered, the optional parameters are used. Only commands to the *gView Server* are displayed here
that cause rendering. The *Rending* happens in the *gView Server*. There the tiles are created and stored in the file system.
  
.. note::
  If a tile already exists, the server does not recalculate it. The server only calculates tiles that do not yet exist. This makes sense if only a few tiles of a *Tile Cache* need to be recalculated.
  Here you first delete the affected *Tiles* on the File Sytsem (e.g. with the command ``gView.Cmd.ClipCompactTilecache``)

Optional parameters:

* ``-epsg``: The *gView Server* can offer tilecaches in different coordinate systems for a service. Which coordinate systems are possible can be set in the metadata in *gView Carto*. Using the ``-info`` command shown above
the possible values can be displayed.

* ``-compact``: This option creates a *Compact Tile Cache*. The difference to a *classic tile cache* is that not a file is created for each tile. Here, 128 x 128 tiles always combined in on 
File. As a result, the Tile Cache takes up a little less space in the File System and can be copied more easily (since a classic Tile Cache often consists of millions of files). A large file 
is usually easier to handle for the file system than many small individual files). However, with each later request, the single tile must be extracted from the large files (which is usually done very quickly).

* ``-orientation``: Here the orientation of the *Tile Cache* (or the location of the origin) is indicated. *Tile caches* with the orientation *lowerleft* cannot be used with WMTS. Therefore, this option is only
of completeness and can usually be omitted. 

* ``-bbox``: A *Bounding Box* (in the respective coordinate system) can be specified here. Only for this area the render commands are sent to the server

* ``-scales``: A list of scales (separated by commas) for which commands are sent to the server.

* ``-threads``: To speed up tile cache creation, several commands can be sent to the *gView Server* at the same time. Otherwise, only the command for a tile is sent to the server at the same time. It doesn't make a
  sense to specify extremely high values here. Rule of thumb: ``-threads`` = number of processors. If this does not increase the processor load, it means that most of the time during rendering is spent waiting for the database.
  In this case, the value can also be increased here.
  

Example:

.. code::
