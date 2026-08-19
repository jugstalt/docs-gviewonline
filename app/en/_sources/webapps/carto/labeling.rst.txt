Label Renderer
==============

To label the features of a layer, the approach is similar to that used in *Rendering*. In the 
*Settings* dialog for the layer, on the left, you will find the section ``Label Renderer``. Here, 
the checkbox ``Label features for this layer`` must first be activated:

.. image:: img/labeling1.png

Different *Renderers* are offered here as well.

Simple Text Renderer
--------------------

With this renderer, features can be labeled based on attribute values. This represents the 
most common use case.

The properties of the *Renderer* are divided into the following categories:

* **Field / Expression:** Labeling can be done based on a field or an expression.
  For the ``Field`` type, simply select the field you want to label by:

  .. image:: img/labeling2.png
      :width: 400

  With ``Expression``, an expression can be defined. When you click on ``Edit Expression``, 
  the **Expression Editor** opens. Here, an expression can be specified that consists of free text
  and placeholders (field names in square brackets) for the fields:

  .. image:: img/labeling3.png
      :width: 400

  Field names are listed at the top. By double-clicking on a field, it is added as 
  a placeholder in the expression.

  Using *expressions*, field values can also be formatted. Formatting is possible for 
  number and date fields. The syntax for a placeholder with formatting is as follows:

    ``[FieldName:Format]``, e.g. ``[FieldName:0.00]`` for a number with two decimal places.

  Examples:

  *Standard formats for numbers:* 

  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - Purpose	
      - Format
      - Placeholder Example
      - Result with Value = 12345.678
    * - Fixed decimal places 
      - F2
      - [AMOUNT:F2]
      - 12,345.68
    * - Currency	
      - C	
      - [PRICE:C]	
      - €12,345.68
    * - Thousands separator, standard	
      - N0	
      - [QUANTITY:N0]	
      - 12,346
    * - Percent	
      - P1
      - [SHARE:P1]
      - (Value = 0.256) 25.6%
    * - Exponential	
      - E3	
      - [VALUE:E3]	
      - 1.235E+004
    * - Hexadecimal (Integer)	
      - X8	
      - [ID:X8] 
      - (Value = 48879) 0000BEEF

  *Custom number formats:*

  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - Purpose 
      - Format String 
      - Example 
      - Result
    * - Two decimals, dot as separator 
      - 0.00 
      - [VALUE:0.00] (12.3456) 
      - 12.35
    * - Up to two decimals 
      - #.## 
      - [VALUE:#.##] (12.3) 
      - 12.3
    * - Fixed width, leading zeros 
      - 00000 
      - [NO:00000] (42) 
      - 00042
    * - Thousands separator, two decimals 
      - #,##0.00 
      - [TOTAL:#,##0.00] 
      - 12,345.68 

  *Standard formats for date/time:*
  
  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - Purpose 
      - Format 
      - Placeholder Example 
      - Result with Value = 2025-04-24 14:30
    * - Short date 
      - d 
      - [DATE:d] 
      - 04/24/2025
    * - Long date 
      - D 
      - [DATE:D] 
      - Thursday, April 24, 2025
    * - Date + short time 
      - g 
      - [DATE:g] 
      - 04/24/2025 14:30
    * - ISO-8601
      - o
      - [DATE:o]
      - 2025-04-24T14:30:00.0000000+02:00

  **SimpleScript expressions:**

  In addition to simple placeholder expressions, the **Expression Editor** also supports
  so-called ``SimpleScript`` expressions. Such an expression must start with ``@@start`` and
  end with ``@@end``. In between, conditional text blocks can be defined using ``@@if`` /
  ``@@endif``.

  With ``@@if(...)``, the text between ``@@if`` and ``@@endif`` is tied to a condition. The
  condition always refers to a field (in square brackets). The following forms are possible
  (compared to the equivalent VB condition):

  .. list-table::
    :width: 100 %
    :header-rows: 1

    * - VB
      - Meaning
      - ``@@if(...)`` form
    * - ``[FIELD] <> ""``
      - Field is not empty
      - ``@@if([FIELD])``
    * - ``[FIELD] = ""``
      - Field is empty
      - ``@@if([FIELD],)``
    * - ``[FIELD] = "Value"``
      - Field equals "Value"
      - ``@@if([FIELD],Value)``
    * - ``[FIELD] <> "Value"``
      - Field does not equal "Value"
      - ``@@if([FIELD],not,Value)``

  ``@@if`` blocks can also be nested to combine multiple conditions, as in the following example
  (labeling by type, with the prefix ``S:`` for ``Schieber`` and ``V:`` for ``Ventil``):

  .. code-block:: text

    @@start
    @@if([TYP],Schieber)
    S: [NAME]
    @@endif
    @@if([TYP],Ventil)
    @@if([TYP],not,Schieber)
    V: [NAME]
    @@endif
    @@endif
    @@if([TYP],not,Schieber)
    @@if([TYP],not,Ventil)
    [NAME]
    @@endif
    @@endif
    @@end

  In addition, any number of ``@@replace(SearchText,ReplaceText)`` statements can follow after
  ``@@end``. They are applied one after another to the text produced by the script, each
  replacing the search text with the replacement text. Leaving the replacement text empty
  (``@@replace(SearchText,)``) simply removes the search text. This is useful, for example, to
  abbreviate long default values or hide them entirely:

  .. code-block:: text

    @@start
    [TYP]
    @@end
    @@replace(Hausanschluß,HA)
    @@replace(Hausanschluss,HA)
    @@replace(Sonstiger Endpunkt,)
    @@replace(Sonstiger Punkt,)
    @@replace(Sonstiges Punktobjekt,)
    @@replace(Reserve,Res.)

* **Behavior:** Here, the priority of the label is specified:

  .. image:: img/labeling4.png
       :width: 400

  When multiple layers are labeled, this is done in the drawing order of the layers. To highlight the labels 
  of a layer, the priority can be specified here. The reason is also that labels are only drawn if there 
  is space available on the map. Labels cannot overlap. Therefore, it is possible that a label is not drawn at all.
  Labels with higher priority have a better chance of being drawn.
  If the priority ``Always`` is given, it will always be drawn, regardless of overlaps.
  However, this can lead to illegible labels if too many texts overlap.

  ``How many labels`` indicates how often a text may occur:

   * ``One per feature``: Each feature is labeled exactly once.
   * ``One per part``: If the feature is a *multipart* feature, each part of the feature is labeled.
   * ``One per name``: If there are multiple features labeled with the same text,
     only the first is labeled. This prevents duplicate texts (for this layer) on the map.

* **Symbol / Cartography:** Here, the labeling symbol (font) can be set:

  .. image:: img/labeling5.png
      :width: 400

  Similar to feature symbology, there is also a **Symbol Composer** for text symbols here:

  .. image:: img/labeling6.png 
      :width: 400

  There is a ``Gallery`` with predefined symbols. Different types of symbols can be added via the ``Stack``:

  * ``Simple Text``: Just the text, no border
  * ``Glowing Text``: Text with a colored outline
  * ``Blockout Text``: Text within a colored rectangle

  .. note:: 

     **Glowing** and **Blockout** text improve the readability of text because they stand out better 
     against the background. On backgrounds like aerial images, normal texts are often 
     difficult to read.

* **Placement / Placement Priority:** For point and line themes, it may make sense to determine where 
  the label is placed. This can be controlled through ``Placement``:
  
  .. image:: img/labeling7.png
      :width: 400

  The center point would mean that the label is placed directly in the middle of the point or line.
  Through the priority, alternative positions can be assigned. If the label cannot be drawn due to 
  space constraints, the subsequent specified positions are applied in order until a positive result is achieved.


Scale Dependent/Group Layer Renderer
------------------------------------

Here, as previously with the **Scale Dependent (Feature) Renderer**, groups of *Label Renderers*
can be defined. This allows layers to be labeled differently at different scales.

For example, countries could be labeled with a country abbreviation at small scales.
As you zoom further into the map, they could be labeled with the full country name.

Chart Renderer
--------------

Here, instead of text, charts are inserted into the map:

.. image:: img/labeling8.png

* **Behavior:** Here, the type of chart can be specified (Pie, Bars, Stack).

* **Chart Data:** Here, fields can be dragged into the ``Chart Fields`` area.
  The chart is created from the field values of these fields.
  (Note: the first field must be dragged onto the title (``Chart Fields``).)
  If you want to remove a field, it can simply be dragged back to the left into the list.

* **Size:** Charts can have a fixed size, or the size can depend on the total sum.
  For this, a sum value and a size in pixels are entered. The size of the charts is then
  calculated relative to these values.

