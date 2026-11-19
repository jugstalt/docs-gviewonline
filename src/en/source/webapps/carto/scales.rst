Scale Limits
============

It is often necessary to display layers or labels only within certain
scale limits. This can be configured via the **Layer Settings** dialog 
in the ``Map / Display`` section:

.. image:: img/scales1.png

Here, a layer is only displayed between 1:20,000 and 1:1,000,000.

.. note::
   The scale limits can also be set for labeling, where the display scale range is always checked first. 
   That means, if a layer is not displayed due to scale limits, it will not be labeled either.

It can also be set here whether the reference scale should be applied to the symbology.

.. note::

   The reference scale was set in the map properties. When applied to a
   layer, it means that the symbol has the size set for this scale. If you zoom further into the map, 
   the symbol becomes larger and vice versa.

* **Apply reference scale:** The reference scale is applied to the symbology of this layer.

* **Do not apply reference scale:** The reference scale is not applied. The symbology remains
  unchanged at any scale.

* **Apply reference scale (max scale up):** The reference scale is applied, but the symbols 
  cannot be scaled up indefinitely. For example, using the slider, it can be defined that a symbol 
  can only be enlarged to double its size.

.. note::

   All these settings work the same for text.