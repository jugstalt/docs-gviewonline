Additional Layer Properties
===========================

In addition to the already described settings, the **Layer Settings** dialog offers 
additional areas. These are only briefly mentioned here:

* ``Feature Selection``: Here, a symbol can be chosen that indicates how the selection 
  of features of this layer should be displayed.

* ``Spatial Reference``: This shows the spatial reference system of the layer.
  This tab is for information only. The values come from the respective 
  feature database and cannot be changed here.

* ``Filter``: Here, an SQL statement (WHERE clause only) can be specified, which serves as a filter 
  for the displayable and queryable objects of this layer. 
  Thus, not all objects are displayed for a layer anymore, but only those that meet the filter criteria.

  .. image:: img/layersettings1.png
      :width: 400
    
  To assist in creating filter conditions, there is a ``Query Builder``.

  .. image:: img/layersettings2.png
       :width: 400

  By double-clicking, individual fields/operations/values can be added to the query.
  Additionally, suggested values are provided for the individual fields.

