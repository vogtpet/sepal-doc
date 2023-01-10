GuidosToolbox Workbench – GWB
=============================

The GuidosToolbox Workbench (**GWB**, `homepage <https://forest.jrc.ec.europa.eu/en/activities/lpa/gwb/>`_) is a subset of the desktop software package GuidosToolbox (`GTB <https://forest.jrc.ec.europa.eu/en/activities/lpa/gtb/>`_) designed as a cmd-line application for Linux 64bit servers. Citation reference: `GuidosToolbox Workbench: Spatial analysis of raster maps for ecological applications <https://doi.org/10.1111/ecog.05864>`_.

This document provides usage instructions for the cmd-line implementation of  **GWB**. Documentation on the GWB SEPAL browser-based application is available `here <https://docs.sepal.io/en/latest/modules/dwn/gwb.html>`_. 


Initial setup
-------------

As regular user, please first copy the **GWB** setup into your :code:`$HOME` account using the command: 

.. code-block:: console

    $ cp -fr /opt/GWB/*put ~/

You will now find the new directories :code:`input` and :code:`output` in your :code:`$HOME` account.

-   :code:`input`: This directory contains module-specific parameter files, two sample geotif images and a README file.
-   :code:`output`: This directory is empty.

All GWB modules require categorical raster input maps in data type unsigned byte (8bit), with discrete integer values within [0, 255] byte. The two sample images in the directory :code:`input` are:

-   :code:`example.tif`: 0 byte - Missing, 1 byte - Background, 2 byte - Foreground
-   :code:`clc3class.tif`: 1 byte - Agriculture, 2 byte - Natural, 3 byte - Developed


**GWB** is designed to apply the module-specific settings of the respective parameter file to all tif-images placed in the directory :code:`input`. The module-specific results will be written into the directory :code:`output`.

.. note::

    -   Please also run the above cp-command to update your **GWB**-setup files with potentially modified files provided by a newer version of **GWB**.  
    -   The directory :code:`input` has a subdirectory :code:`backup` having backup copies of all parameter files. This subdirectory may also be used to temporarily store images that should be excluded from processing.

Example of the **GWB** setup in the user account :code:`/home/prambaud`.

.. code-block:: console

    $ pwd
    /home/prambaud

    $ ls output/
    $ ls input/
    acc-parameters.txt      clc3class.tif        example.tif         
    frag-parameters.txt     mspa-parameters.txt  parc-parameters.txt  
    rec-parameters.txt      spa-parameters.txt   backup              
    dist-parameters.txt     fad-parameters.txt   lm-parameters.txt    
    spatcon-parameters.txt  readme.txt           rss-parameters.txt

    $ less input/readme.txt
    Images:
    - GWB will process all images from the folder 'input' having the suffix: .tif

    Parameter files: *-parameter.txt
    - please do not delete these files
    - modify only the settings at the end of the file enclosed by *****

    Directory backup: not needed for processing
    - a set of backup parameter files is included here
    - temporarily store images here that you want to exclude from processing    


Usage Instructions Overview
---------------------------

To get an overview of all **GWB** modules enter the command: :code:`GWB`

.. code-block:: console

    $ GWB
    ===============================================================================
              GWB: GuidosToolbox-Workbench
    ===============================================================================
         Part A: brief module description
    ===============================================================================
    cmd-line image analysis modules from GuidosToolbox 
    (https://forest.jrc.ec.europa.eu/en/activities/lpa/gtb/):
    Usage of GWB implies compliance with the conditions in the EULA_GWB.pdf
    (https://ies-ows.jrc.ec.europa.eu/gtb/GWB/EULA_GWB.pdf)
    
    GWB_check4updates
       Display installed and current program version
       and test for program updates 
 
    GWB_ACC: Accounting of image objects and area classes
        Requirements: 1b-BG, 2b-FG, optional: 0b-missing, 
        optional: 3b-special background 1, 4b-special background 2
        Parameter file: input/acc-parameters.txt
 
    GWB_DIST: Euclidean Distance and Hypsometric Curve
        Requirements: 1b-BG, 2b-FG, optional: 0b-missing
        Parameter file: input/dist-parameters.txt
 
    GWB_FAD: Multiscale fragmentation analysis
        Requirements: 1b-BG, 2b-FG, optional: 0b-missing, 
        optional: 3b-special BG, 4b-non-fragmenting BG
        Parameter file: input/fad-parameters.txt
    
    GWB_FRAG: user-selected custom scale fragmentation analysis
        Requirements: 1b-BG, 2b-FG, optional: 0b-missing, 
        optional: 3b-special BG, 4b-non-fragmenting BG
        Parameter file: input/frag-parameters.txt
    
    GWB_LM: Landscape Mosaic 
        Requirements: 1b-Agriculture, 2b-Natural, 3b-Developed 
        optional: 0b-missing
        Parameter file: input/lm-parameters.txt
    
    GWB_MSPA: Morphological Spatial Pattern Analysis (up to 25 classes)
        Requirements: 1b-BG, 2b-FG, optional: 0b-missing
        Parameter file: input/mspa-parameters.txt
    
    GWB_PARC: Landscape Parcellation index
        Requirements: [1b, 255b]-land cover classes, optional: 0b-missing
        Parameter file: input/parc-parameters.txt
    
    GWB_REC: Recode class values
        Requirements: categorical map with up to 256 classes [0b, 255b]
        Parameter file: input/rec-parameters.txt
    
    GWB_RSS: Restoration Status summary
        Requirements: 1b-BG, 2b-FG, optional: 0b-missing
        Parameter file: input/rss-parameters.txt
    
    GWB_SPA: Spatial Pattern Analysis (2, 3, 5, or 6 classes)
        Requirements: 1b-BG, 2b-FG, optional: 0b-missing
        Parameter file: input/spa-parameters.txt
        
    GWB_SPATCON: moving window analysis of proportion and attribute adjacency table
        Requirements: categorical map within [0b, 255b]
        Parameter file: input/spatcon-parameters.txt
        
    More details in the module-specific parameter files, or run: GWB_XXX --help
    
    ===============================================================================
         Part B: usage
    =============================================================================== 
    a) standalone mode (within the directory GWB): ./GWB_ACC
       OR add a custom full path to your input and output directory i.e.: 
       ./GWB_ACC -i=<your dir_input> -o=<your dir_output>
    
    b) system mode (GWB installed in /opt/):
       To get started in system mode, copy the input/output directories to
       your home folder using the command: cp -fr /opt/GWB/*put ~/
       To process, add the full path to your input and output directory: 
       GWB_ACC -i=$HOME/input -o=$HOME/output
    
    ===============================================================================
         Part C: processing requirements
    ===============================================================================
    RAM requirements depend on module processing settings and the amount 
    and the configuration of objects in the input image.
    You can use: /usr/bin/time -v <full GWB-command> and then look 
    at 'Maximum resident set size', which will show the maximum 
    RAM usage point (in kb) encountered during execution.
     a) RAMpeakGB = divide 'Maximum resident set size' by 1024^2
     b) imsizeGB = image size in GB = xdim*ydim/1024^3
     c) processing RAM requirement by module: RAMpeak/imsizeGB
    
    Approximate peak RAM usage factors for an image of size imsizeGB: 
    GWB_ACC    : 30 * imsizeGB
    GWB_DIST   : 18 * imsizeGB
    GWB_FAD    : 30 * imsizeGB
    GWB_FRAG   : 13 * imsizeGB
    GWB_LM     :  9 * imsizeGB
    GWB_MSPA   : 20 * imsizeGB
    GWB_PARC   : 22 * imsizeGB
    GWB_REC    :  2 * imsizeGB
    GWB_RSS    : 20 * imsizeGB
    GWB_SPA    : 20 * imsizeGB
    GWB_SPATCON: metric-dependent
    Example: input image 50,000 x 50,000 pixels -> imsizeGB = 2.33 GB. 
    Processing this image for GWB_ACC will require 30 * 2.33 ~ 70 GB RAM
    
    The RAM usage factors above are indicative only. They depend on module 
    settings and the amount/configuration of objects in the input image.
    ===============================================================================
     ***  To read all GWB information in Part A, B, C above,   ***
     ***  either scroll up or run the command:   less /usr/bin/GWB  ***
    ===============================================================================


It is also possible to use the "help" option: :code:`GWB_ACC --help`

.. code-block:: console

    $ GWB_ACC --help
    ----------------------------------------------------------------------------------
    usage: /usr/bin/GWB_ACC -i=dir_input -o=dir_output
    -i=<full path to directory 'input'> 
    (with your input images and parameter files);
    Standalone mode: GWB/input 
    -o=<full path to directory 'output'> 
    (location for results, must exist and must be empty);
    Standalone mode: GWB/output 
    --help: show options

    Standalone mode: ./GWB_ACC
    System mode/use custom directories: GWB_ACC -i=<your dir_input> -o=<your dir_output>
    ----------------------------------------------------------------------------------

.. tip::
    
    When used for the first time, please accept the `EULA <https://ies-ows.jrc.ec.europa.eu/gtb/GWB/EULA_GWB.pdf>`_ terms. This step is only needed once.

Additional, general remarks:

-   The directory :code:`output` must be empty before running a new analysis. Please watch out for hidden files/folders in this directory, which may be the result of an interrupted execution. The safest way to empty the directory is to delete it and recreate a new directory :code:`output`.
-   **GWB** will automatically process all suitable geotiff images (single band and of datatype byte) from the directory :code:`input`. Images of different format or that are not compatible with the selected analysis module requirements will be skipped. Details on each image processing result can be found in the log-file in the directory :code:`output`.
-   **GWB** is written in the  the `IDL language <https://www.l3harrisgeospatial.com/Software-Technology/IDL>`_. It includes all required IDL libraries and the source code of each module, stored in the folder: :code:`/opt/GWB/tools/source/`.
-   To list your current version of **GWB**, or to check for potential new **GWB** versions, please run the command: 

    .. code-block:: console

        $ GWB_check4updates

-   Any distance or area measures are calculated in pixels. It is therefore crucial to use images in equal area projection. Conversion to meters/hectares require to know the pixel resolution.

Available Commands
------------------

.. danger:: 

    Please enter your own settings by amending the module-specific parameters within the section marked with :code:`*******` at the end of the respective input/<module>-parameters.txt file. If the parameter file is incorrect, consult the respective input/backup/<module>-parameters.txt file.

GWB_ACC
^^^^^^^

This module will conduct the **Accounting** analysis. Accounting will label and calculate the area of all foreground objects. The result are spatially explicit maps and tabular summary statistics. Details on the methodology and input/output options can be found in the `Accounting <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Objects-Accounting.pdf>`_ product sheet.

Requirements
""""""""""""

Single band geotiff in data format byte: 

-   0 byte: missing (optional)
-   1 byte: background
-   2 byte: foreground (forest)
-   3 byte: special background 1 (optional)
-   4 byte: special background 2 (optional)

Processing parameter options are stored in the file :code:`input/acc-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_ACCOUNTING parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; ACC: Accounting of image objects and patch area size classes
    ;; Input image requirements: 1b-background, 2b-foreground, optional: 0b-missing
    ;; optional: 3b-special background 1, 4b-special background 2
    ;; Please specify entries at lines 25-29 ONLY using the following options:
    ;;
    ;; line 25: Foreground connectivity: 8 (default) or 4 
    ;; line 26: spatial pixel resolution in meters: 
    ;; line 27: up to 5 area thresholds [unit: pixels] in increasing order
    ;;          and separated by a single space.
    ;; line 28: output option:   default (stats + image of viewport) OR 
    ;;   detailed (stats + images of ID, area, viewport; requires much more CPU/RAM!))
    ;; line 29: big3pink: 0 (no - default) or 1 (show 3 largest objects in pink color)
    ;;
    ;; an example parameter file with default output would look like this:
    ;; 8
    ;; 25
    ;; 200 2000 20000 100000 200000
    ;; default
    ;; 0
    ****************************************************************************
    8
    25
    200 2000 20000 100000 200000
    default
    1
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.


:code:`GWB_ACC` Command and listing of results in the directory :code:`output`:

.. code-block:: console

    $ GWB_ACC -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_ACC using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif    
    Done with: example.tif
    Accounting finished sucessfully

    $ ls -R output/
    output/:
    acc.log  clc3class_acc  example_acc

    output/clc3class_acc:
    clc3class_acc.csv  clc3class_acc.tif  clc3class_acc.txt

    output/example_acc:
    example_acc.csv  example_acc.tif  example_acc.txt

example statistics and graphical result of input image :code:`example.tif`:

.. code-block:: text

    Accounting size classes result using: 
    example
    Base settings: 8-connectivity, pixel resolution: 25 [m]
    Conversion factor: pixel_to_hectare: 0.0625000, pixel_to_acres: 0.154441
    --------------------------------------------------------------------------------------------- 
    Size class 1: [1, 200] pixels; color: black
            # Objects      Area[pixels]     % of all objects  % of total FGarea
                2789             31190           97.8596         7.2790497
    --------------------------------------------------------------------------------------------- 
    Size class 2: [201, 2000] pixels; color: red
            # Objects      Area[pixels]     % of all objects  % of total FGarea
                    44             23643           1.54386         5.5177484
    --------------------------------------------------------------------------------------------- 
    Size class 3: [2001, 20000] pixels; color: yellow
            # Objects      Area[pixels]     % of all objects  % of total FGarea
                    14             98972          0.491228         23.097855
    --------------------------------------------------------------------------------------------- 
    Size class 4: [20001, 100000] pixels; color: orange
            # Objects      Area[pixels]     % of all objects  % of total FGarea
                    2             59874         0.0701754         13.973255
    --------------------------------------------------------------------------------------------- 
    Size class 5: [100001, 200000] pixels; color: brown
            # Objects      Area[pixels]     % of all objects  % of total FGarea
                    0                 0           0.00000         0.0000000
    --------------------------------------------------------------------------------------------- 
    Size class 6: [200001 -> ] pixels; color: green
            # Objects      Area[pixels]     % of all objects  % of total FGarea
                    1            214811         0.0350877         50.132092
    --------------------------------------------------------------------------------------------- 
    --------------------------------------------------------------------------------------------- 
    Sum of all classes:
            # Objects      Area[pixels]     % of all objects  % of total FGarea
                2850            428490           100.000         100.00000
    
    Median Patch Size:                5
    Average Patch Size:          150.347
    Standard Deviation:          4143.11
    
    Three largest object IDs and area[pixels]; color: pink
    These 3 objects overlay objects listed above
    1)                  1            214811
    2)                901             33508
    3)               1662             26366

.. figure:: ../_images/cli/gwb/example_acc.png
    :width: 50%
    :align: center

Accounting has been used to map and summarize forest patch size classes in the `FAO SOFO2020 <http://www.fao.org/publications/sofo/en/>`_ report and the Forest Europe `State of Europe's Forest 2020 <https://foresteurope.org/publications/>`_ report with additional technical details in the respective JRC Technical Reports for `FAO <https://doi.org/10.2760/145325>`_ and `FE <https://doi.org/10.2760/991401>`_.

GWB_DIST
^^^^^^^^

This module will conduct the **Euclidean Distance** analysis. Each pixel will show the shortest distance to the foreground boundary. Pixels inside a foreground object have a positive distance value while background pixels have a negative distance value. The result are spatially explicit maps and tabular summary statistics.
Details on the methodology and input/output options can be found in the `Distance <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Distance-Euclidean.pdf>`_ product sheet.

Requirements
""""""""""""

Single band geotiff in data format byte: 

-   0 byte: missing (optional)
-   1 byte: background
-   2 byte: foreground (forest)

Processing parameter options are stored in the file :code:`input/dist-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_DIST parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; DIST: Euclidean Distance + Hypsometric Curve
    ;; Input image requirements: 1b-background, 2b-foreground, optional: 0b-missing
    ;;
    ;; Please specify entries at lines 17-18 ONLY using the following options:
    ;;
    ;; line 17: Foreground connectivity: 8 (default) or 4 
    ;; line 18: 1-Eucl.Distance only   or  2- Eucl.Distance + Hysometric Curve
    ;;
    ;; an example parameter file with default settings would look like this:
    ;; 8
    ;; 2
    ****************************************************************************
    8
    2
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_DIST` command and listing of results in the directory output: 

.. code-block:: console

    $ GWB_DIST -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_DIST using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    % Loaded DLM: LAPACK.
    % Loaded DLM: PNG.
    Done with: example.tif
    DIST finished sucessfully

    $ ls -R output/
    output/:
    dist.log  example_dist

    output/example_dist:
    example_dist_hist.png      example_dist_hmc.csv  example_dist_hmc.png  
    example_dist_hmc.txt       example_dist.tif      example_dist.txt  
    example_dist_viewport.tif

Example statistics (hypsometric curve) and spatial result of input image :code:`example.tif`:

.. image:: ../_images/cli/gwb/example_dist_hmc.png
    :width: 49%

.. image:: ../_images/cli/gwb/example_dist.png
    :width: 49%

Remarks
"""""""

-   The result provides additional statistics in txt and csv format.
-   Spatially explicit distance per-pixel values are shown in a pseudo-elevation color map. Positive values are associated with land (forest: yellow, orange, red, green), negative values with sea (non-forest: cyan to dark blue) and a value of zero corresponds to the coast line (forest– non-forest boundary).
-   Actual per-pixel distance values are provided in a dedicated image (not shown here)
-   Per-pixel distance values can be summarized with the Hypsometric curve (see above).

**Euclidean Distance** maps of forest patches have been used to map and summarize forest fragmentation, see for example `Kozak et al <https://doi.org/10.3390/su10051472>`_.

.. _gwb_fad:

GWB_FAD
^^^^^^^

This module will conduct the **fragmentation** analysis at **five fixed observation scales**. Because forest fragmentation is scale-dependent, fragmentation is reported at five observation scales, which allows different observers to make their own choice about scales and threshold of concern. The change of fragmentation across different observation scales provides additional interesting information. Fragmentation is measured by determining the Forest Area Density (**FAD**) within a shifting, local neighborhood. It can be measured at pixel or patch level. The result are spatially explicit maps and tabular summary statistics. Details on the methodology and input/output options can be found in the `Fragmentation <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Fragmentation-FADFOS.pdf>`_ product sheet.

Requirement
"""""""""""

Single band geotiff in data format byte: 

-   0 byte: missing (optional)
-   1 byte: background
-   2 byte: foreground (forest)
-   3 byte: specific background (optional)
-   4 byte: non-fragmenting background (optional)

Processing parameter options are stored in the file :code:`input/fad-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_FAD parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; FAD = multi-scale fragmentation analysis at fixed observation scales of
    ;; [7x7, 13x13, 27x27, 81x81, 243x243] pixels
    ;;
    ;; FAD: per-pixel density, color-coded into 6 fragmentation classes
    ;; FAD-APP2: average per-patch density, color-coded into 2 classes
    ;; FAD-APP5: average per-patch density, color-coded into 5 classes
    ;; 
    ;; Input image requirements: 1b-background, 2b-foreground, optional: 
    ;;    0b-missing, 3b-special background, 4b-non-fragmenting background
    ;;
    ;; FAD will provide 5+1 images and summary statistics.
    ;;
    ;; Please specify entries at lines 28-30 ONLY using the following options:
    ;; line 28: FAD  or  FAD-APP2  or  FAD-APP5
    ;; line 29: Foreground connectivity: 8 (default) or 4 
    ;; line 30: high-precision: 1 (default) or 0
    ;;         (1-float precision, 0-rounded byte)
    ;;
    ;; an example parameter file doing FAD-APP5 and using 8-connected foreground:
    ;; FAD-APP5
    ;; 8
    ;; 1
    ****************************************************************************
    FAD
    8
    1
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_FAD` command and listing of results in the directory output: 

.. code-block:: console

    $ GWB_FAD -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_FAD using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    % Loaded DLM: LAPACK.
    % Loaded DLM: PNG.
    Done with: clc3class.tif
    Done with: example.tif
    FAD finished sucessfully
    
    $ ls -R output/
    output/:
    clc3class_fad  example_fad  fad.log

    output/clc3class_fad:
    clc3class_fad_13.tif      clc3class_fad_27.tif       clc3class_fad_81.tif       
    clc3class_fad_mscale.csv  clc3class_fad_mscale.tif   clc3class_fad_243.tif  
    clc3class_fad_7.tif       clc3class_fad_barplot.png  clc3class_fad_mscale.sav  
    clc3class_fad_mscale.txt

    output/example_fad:
    example_fad_13.tif      example_fad_27.tif       example_fad_81.tif       
    example_fad_mscale.csv  example_fad_mscale.tif   example_fad_243.tif  
    example_fad_7.tif       example_fad_barplot.png  example_fad_mscale.sav  
    example_fad_mscale.txt

Example statistics and spatial result of a multi-scale per-pixel analysis of the input image :code:`example.tif`:

.. image:: ../_images/cli/gwb/example_fad_barplot.png
    :width: 49%

.. image:: ../_images/cli/gwb/example_fad_mscale.png
    :width: 49%

Remarks
"""""""

-   The result provides additional statistics in txt and csv format.
-   The IDL-specific sav-file contains all information to conduct fragmentation change analysis in GTB. 
-   In addition to the above multi-scale image, the result provides fragmentation images at each of the 5 fixed observation scales.
-   Options to report at pixel- or patch-level and to select the number of fragmentation classes (6, 5, 2).

Fragmentation has been used to map and summarize the degree of forest fragmentation by Riitters et al. (`2002 <https://doi.org/10.1007/s10021-002-0209-2>`_, `2012 <https://doi.org/10.1038/srep00653>`_) as well as the US Forest Inventory and Analysis (`FIA <https://www.fia.fs.fed.us/>`_) reports since 2003.

GWB_FRAG
^^^^^^^^

This module will conduct the **fragmentation** analysis at a **user-selected observation scale**. This module and its options are similar to :ref:`gwb_fad` but allow the user to specify a single (or multiple) specific observation scale. The result are spatially explicit maps and tabular summary statistics. Details on the methodology and input/output options can be found in the `Fragmentation <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Fragmentation-FADFOS.pdf>`_ product sheet.

Requirements
""""""""""""
Single band geotiff in data format byte:

-   0 byte: missing (optional)
-   1 byte: background
-   2 byte: foreground (forest)
-   3 byte: specific background (optional)
-   4 byte: non-fragmenting background (optional)

Processing parameter options are stored in the file :code:`input/frag-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_FRAG parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;; Fragmentation analysis at up to 10 user-selected Fixed Observation Scales (FOS):
    ;; FAC (Foreground Area Clustering); FAD (Foreground Area Density)
    ;; 
    ;; Options:
    ;; FAC(FAD)5/6: per-pixel clustering (density), color-coded into 5/6 fragmentation classes
    ;; FAC(FAD)-APP2/5: average per-patch clustering (density), color-coded into 2/5 classes
    ;; 
    ;; Input image requirements: 1b-background, 2b-foreground, optional: 
    ;;    0b-missing, 3b-special background, 4b-non-fragmenting background
    ;;
    ;; FRAG will provide one (1) image per observation scale and summary statistics.
    ;; (method: FAC or FAD; reporting at pixel or patch (APP) level; # of reporting classes: 2, 5, 6)
    ;; Please specify entries at lines 32-36 ONLY using the following options:
    ;; line 32: FAC_5 (default)  or  FAC_6, FAC-APP_2, FAC_APP_5, FAD_5, FAD_6, FAD-APP_2  or  FAD-APP_5
    ;; line 33: Foreground connectivity: 8 (default) or 4 
    ;; line 34: pixel resolution [meters]
    ;; line 35: up to 10 window sizes (unit: pixels, uneven within [3, 501] )
    ;;          n increasing order and separated by a single space.
    ;; line 36: high-precision: 1 (default) or 0
    ;;          (1-float precision, 0-rounded byte)
    ;;
    ;; an example parameter file using the default FAC5 and 8-connected foreground:
    ;; FAC_5
    ;; 8
    ;; 100
    ;; 27
    ;; 1
    ****************************************************************************
    FAD-APP_2
    8
    100
    23
    1
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_FRAG` command and listing of results in the directory output: 

.. code-block:: console

    $ GWB_FRAG -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_FRAG using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    Done with: example.tif
    Frag finished sucessfully
    
    $ ls -R output/
    output/:
    clc3class_frag  example_frag  frag.log

    output/clc3class_frag:
    clc3class_fos-fad-app_2class_23.tif  clc3class_fos-fad-app_2class.csv  clc3class_fos-fad-app_2class.sav  
    clc3class_fos-fad-app_2class.txt

    output/example_frag:
    example_fos-fad-app_2class_23.tif  example_fos-fad-app_2class_23.csv  example_fos-fad-app_2class_23.sav  
    example_fos-fad-app_2class_23.txt

Example statistics and spatial result of custom-scale per patch analysis of the input image :code:`example.tif`, here FAD-APP_2 showing Continuous forest patches in light green and Separated forest patches in dark green.

.. code-block:: text

    Fragmentation analysis using Fixed Observation Scale (FOS)
    Method options: FAC (Foreground Area Clustering); FAD (Foreground Area Density)
    Summary analysis for image:
    example.tif
    ================================================================================
    FOS parameter settings:
    Foreground connectivity: 8-conn FG
    FOS-type selected: FAD-APP_2
    Method: FAD
    Reporting style: FAD at patch level (APP: average per patch)
    Number of reporting classes: 2
    Pixel resolution [m]: 100.000
    Window size [pixels]: 23
    Observation scale [(window size * pixel resolution)^2]: 
      Observation scale:   1
    Neighborhood area:   23x23     
     [hectare]:     529.00
       [acres]:    1307.19
    ================================================================================
    Image foreground statistics:
    Foreground area [pixels]: 428490
    Number of foreground patches: 2850
    Average foreground patch size: 150.34737
    ================================================================================
    Proportion [%] of foreground area in foreground cover class:
    FOS-FAD-APP: 5-classes:
               Rare (FAD-pixel value within: [0 - 9]):      1.2089
           Patchy (FAD-pixel value within: [10 - 39]):      7.1572
     Transitional (FAD-pixel value within: [40 - 59]):      4.2668
         Dominant (FAD-pixel value within: [60 - 89]):     87.3670
        Interior (FAD-pixel value within: [90 - 100]):      0.0000
    FOS-FAD-APP: 2 classes:
        Separated  (FAD-pixel value within: [0 - 39]):      8.3661
      Continuous (FAD-pixel value within: [40 - 100]):     91.6339
    ================================================================================
    Precision: floating point
    Average pixel value across all foreground pixels using FAD-method:     75.2900
                    Equivalent to average foreground connectivity:     75.2900
                   Equivalent to average foreground fragmentation:     24.7100

.. figure:: ../_images/cli/gwb/example_fad-app2_23.png
    :width: 50%

Remarks
"""""""

-   The result provides additional statistics in txt and csv format.
-   The IDL-specific sav-file contains all information to conduct fragmentation change analysis in GTB.
-   The result provides one fragmentation image for each custom observation scale. In the example above, the user selected 1 observation scale with local neighborhood of 23x23 pixels.
-   Options to report at pixel- or patch-level and to select the number of fragmentation classes (6, 5, 2).

Fragmentation has been used to map and summarize the degree of forest fragmentation in the `FAO SOFO2020 <http://www.fao.org/publications/sofo/en/>`_ report and the Forest Europe `State of Europe's Forest 2020 <https://foresteurope.org/publications/>`_ report with additional technical details in the respective JRC Technical Reports for `FAO <https://doi.org/10.2760/145325>`_ and `FE <https://doi.org/10.2760/991401>`_.

GWB_LM
^^^^^^

This module will conduct the **Landscape Mosaic** analysis at a **user-selected observation scale**. The Landscape Mosaic measures land cover heterogeneity, or human influence, in a tri-polar classification of a location accounting for the relative contributions of the three land cover types **Agriculture**, **Natural**, **Developed** in the area surrounding that location. The result are spatially explicit maps and tabular summary statistics. Details on the methodology and input/output options can be found in the `Landscape Mosaic <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Pattern-LM.pdf>`_ product sheet.

Requirements
""""""""""""
Single band geotiff in data format byte: 

-   0 byte: missing (optional)
-   1 byte: Agriculture
-   2 byte: Natural
-   3 byte: Developed

.. warning::

    Input image values > 3 byte will be considered as missing data

Processing parameter options are stored in the file :code:`input/lm-parameters.txt`. 

.. code-block:: text 

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_LM parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; LM will provide an image and summary statistics.
    ;; Please specify entries at line 14 ONLY using the following options:
    ;; line 14: kdim: square window size [pixels], uneven in [3, 5, ...501]
    ;;          obs_scale [hectare] = (pixres[m] * kdim)^2 / 10000
    ;;
    ;; example parameter file 
    ;; (assuming a pixel resolution of 30m, a 11x11 window ~ 10.9 ha):
    ;; 11
    ****************************************************************************
    23
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_LM` command and listing of results in the directory output:

.. code-block:: console

    $ GWB_LM -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_LM using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    % Loaded DLM: PNG.
    Done with: clc3class.tif
    Done with: example.tif
    LM finished sucessfully
    
    $ ls -R output/
    output/:
    clc3class_lm_23  example_lm_23  lm23.log

    output/clc3class_lm_23:
    clc3class_lm_23_103class.tif  clc3class_lm_23_heatmap.png   clc3class_lm_23.tif  
    lm103class_legend.png         clc3class_lm_23_heatmap.csv   clc3class_lm_23_heatmap.sav  
    heatmap_legend.png

    output/example_lm_23:
    example_lm_23_103class.tif  example_lm_23_heatmap.png   example_lm_23.tif   
    lm103class_legend.png       example_lm_23_heatmap.csv   example_lm_23_heatmap.sav  
    heatmap_legend.png

Example statistics (heatmap) and spatial result of custom-scale analysis of the input image :code:`clc3class.tif`, showing degree of predominance of land cover types Agriculture, Natural, Developed.

.. image:: ../_images/cli/gwb/lm103class_legend.png
    :width: 49%

.. image:: ../_images/cli/gwb/clc3class_lm_23.png
    :width: 49%


Remarks
"""""""
-   The IDL-specific sav-file contains all information to conduct LM change analysis in GTB.
-   LM is not restricted to **Ag**, **Nat**, **Dev** but can be applied to any 3 types of dominant land cover.
-   The result provides the LM analysis for a single custom observation scale. In the example above, and assuming a pixel resolution of 100 meter, an observation scale of 23x23 pixels corresponds to a local neighborhood (analysis scale) of 2300x2300 meters ~ 50 hectare.
-   The heatmap facilitates assessments of temporal changes and/or comparison between different sites.

The Landscape Mosaic has been used to map and summarize the degree of landscape heterogeneity in many occasions (see references in the `Landscape Mosaic <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Pattern-LM.pdf>`_ product sheet), including the `RPA <https://www.srs.fs.usda.gov/pubs/37766>`_, `Embrapa <https://urldefense.com/v3/__https:/www.infoteca.cnptia.embrapa.br/infoteca/bitstream/doc/1126895/1/Livro-Doc-345-1815-final-3.pdf__;!!DOxrgLBm!QdlMk1JDuaLmRLWA6JeqizIFwET3sAHqnWlLDX8vQnfpu9edG2iAIws94-RV3jkaakScfw$>`_, and `MAES <https://doi.org/10.2760/757183>`_ reports.

GWB_MSPA
^^^^^^^^

.. warning::

    If your are considering using the MSPA module, keep in mind that the result provides a lot of information (up to 25 classes). The alternative module :code:`GWB_SPA` provides a similar but simplified assessment with up to 6 classes only. Both modules describe morphological features of foreground objects. While MSPA may address certain features of fragmentation, a more comprehensive assessment of fragmentation is obtained with the dedicated fragmentation module :code:`GWB_FRAG`.

This module will conduct the **Morphological Spatial Pattern Analysis**. `MSPA <https://forest.jrc.ec.europa.eu/en/activities/lpa/mspa/>`_ analyses shape and connectivity and conducts a segmentation of foreground (i.e. forest) patches in up to 25 feature classes. The result are spatially explicit maps and tabular summary statistics. Details on the methodology and input/output options can be found in the `Morphology <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Pattern-Morphology.pdf>`_ product sheet.

Requirements
""""""""""""
Single band geotiff in data format byte:

-   0 byte: missing (optional)
-   1 byte: background
-   2 byte: foreground (forest)

Processing parameter options are stored in the file :code:`input/mspa-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_MSPA parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; MSPA: Morphological Spatial Pattern Analysis (up to 25 classes)
    ;; Input image requirements: 1b-background, 2b-foreground, optional: 0b-missing
    ;;
    ;; MSPA will provide an image and summary statistics.
    ;; (see tools/docs/MSPA_Guide.pdf for details)
    ;; Please specify entries at lines 27-32 ONLY using the following options:
    ;;
    ;; line 27: MSPA parameter 1: Foreground connectivity: 8 (default) or 4 
    ;; line 28: MSPA parameter 2: EdgeWidth: 1 (default) or larger integer values
    ;; line 29: MSPA parameter 3: Transition: 1 (default) or 0 
    ;; line 30: MSPA parameter 4: IntExt: 1 (default) or 0 
    ;; line 31: disk: 0 (default) or 1 (requires 20% less RAM but +40% processing time)
    ;; line 32: statistics: 0 (default) or 1 (add summary statistics)
    ;;
    ;; a parameter file with the default settings would look like this:
    ;; 8
    ;; 1
    ;; 1
    ;; 1
    ;; 0
    ;; 0
    ****************************************************************************
    8
    1
    1
    1
    0
    1
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_MSPA` command and listing of results in the directory output:

.. code-block:: console

    $ GWB_MSPA -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_MSPA using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    Done with: example.tif
    MSPA processing finished sucessfully

    $ ls -R output/
    output/:
    example_mspa  mspa.log

    output/example_mspa:
    example_8_1_1_1.tif  example_8_1_1_1.txt

Example statistics of the input image :code:`example.tif` and explanatory sketch of the basic MSPA feature classes: 

.. code-block:: text 

    MSPA results using: 
    example (MSPA: 8_1_1_1, FG_area: 428490, iFG_area: 485606)
    
    MSPA-class [color]:  FG/data pixels [%]  #/BGarea
    ============================================================
        CORE(s) [green]:            --/--     0
        CORE(m) [green]:      75.09/32.19     1196
        CORE(l) [green]:            --/--     0
            ISLET [brown]:       3.26/ 1.40     2429
    PERFORATION [blue]:       2.17/ 0.93     423
            EDGE [black]:      13.54/ 5.80     890
            LOOP [yellow]:       0.60/ 0.26     541
            BRIDGE [red]:       1.42/ 0.61     765
        BRANCH [orange]:       3.93/ 1.68     4685
        Background [grey]:         --/57.14     2319/571240
        Missing [white]:            0.03      51/270
        Opening [grey]:   1.50 Porosity     2291/57116
    Core-Opening [darkgrey]:       --/ 0.59     717/5927
    Border-Opening [grey]:         --/ 5.12     1574/51189

.. image:: ../_images/cli/gwb/mspalegend.gif
    :width: 49%

.. image:: ../_images/cli/gwb/example_8_1_1_1.png
    :width: 49%

Remarks
"""""""

-   MSPA is very versatile and can be applied to any binary map, scale and thematic layer. Please consult the `MSPA Guide <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/MSPA_Guide.pdf>`_, the `Morphology product sheet <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Pattern-Morphology.pdf>`_ and/or the  `MSPA website <https://forest.jrc.ec.europa.eu/en/activities/lpa/mspa/>`_    for further information.
-   The simplified version, GWB_SPA provides fewer classes. GWB_SPA may be useful to get started and may be sufficient to address many assessments.

MSPA is a purely geometric analysis scheme, which can be applied to any type of raster image. It has been used in numerous peer-reviewed publications to map and summarize the spatial pattern, fragmentation and connectivity of forest and other land cover patches, including the detection of structural and functional connecting pathways, analyzing urban greenspace, landscape restoration up to classifying zooplankton species.

GWB_PARC
^^^^^^^^

This module will conduct the **parcellation** analysis. This module provides a statistical summary file (txt/csv- format) with details for each unique class found in the image as well as the full image content: class value, total number of objects, total area, degree of parcellation.
Details on the methodology and input/output options can be found in the `Parcellation <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Objects-Parcellation.pdf>`_ product sheet.

Requirements
""""""""""""

Single band geotiff in data format byte: 

-   0 byte: missing (optional)
-   at least two different landcover classes

Processing parameter options are stored in the file :code:`input/parc-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_PARC parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; PARC: Landscape Parcellation index
    ;; Input image requirements: [1b, 255b]-land cover classes, 
    ;;    optional: 0b-missing
    ;;
    ;; PARC will provide summary statistics only.
    ;;
    ;; Please specify entries at lines 17 ONLY using the following options:
    ;; line 17: Foreground connectivity: 8 (default) or 4 
    ;;
    ;; an example parameter file using 8-connected foreground:
    ;; 8
    ****************************************************************************
    8
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_PARC` command and listing of results in the directory output:

.. code-block:: console 

    $ GWB_PARC -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_PARC using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    Done with: example.tif
    PARC finished sucessfully
    
    $ ls -R output/
    output/:
    clc3class_parc  example_parc  parc.log

    output/clc3class_parc:
    clc3class_parc.csv  clc3class_parc.txt

    output/example_parc:
    example_parc.csv  example_parc.txt


Example statistics of the input image :code:`clc3class.tif` showing statistics and degree of parcellation for each land cover class as well as for the entire image area:

.. code-block:: text

    Class   Value      Count     Area[pixels]     APS          AWAPS       AWAPS/data     DIVISION      PARC[%]
        1       1          45       2448931    54420.7000  2076600.0000  1271360.0000        0.1520        1.1937
        2       2         164        957879     5840.7300    82557.6000    19770.0000        0.9138       17.7426
        3       3         212        593190     2798.0700   128177.0000    19008.4000        0.7839       11.0897
    ================================================================================================================
    8-conn. Parcels:      421       4000000     9501.1875                1310139.4429        0.6725        8.0790

Remarks
"""""""

-   Parcellation is a normalized summary index in [0, 100] %.
-   :code:`GWB_PARC` provides a tabular summary only.

Parcellation, or the degree of dissection, may be useful to provide a quick tabular summary for each land cover class and the entire image. Together with the degree of division, it may be used to make a statement on the dissection of a particular land cover class. Because Parcellation is a normalized index, measuring Parcellation can be used to quantify temporal changes over a given site as well as directly compare the degree of parcellation of different sites. Being able to quantify changes in percent may also be useful to investigate if a given landscape planning measure had in fact a tangible influence on a specific land cover type or not.

GWB_REC
^^^^^^^

This module will conduct **recoding** of categorical land cover classes.

.. danger:: 

    Please ensure to strictly follow the instructions outlined in the file :code:`input/rec-parameters.txt`. In particular:

    -   Insert two values per line separated by a space.
    -   The first is the old class value and the second is the new recoded class value.
    -   Old and new class values must be an integer number in [0, 255].
    -   Class values that are not encountered in the image will be skipped.

Requirements
""""""""""""

Single band geotiff in data format Byte.

Processing parameter options are stored in the file :code:`input/rec-parameters.txt`. 

.. code-block:: text 

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_REC parameter file, also used for recoding by GWB_SPATCON:
    ;; NOTE: change values only at the end of the file between the lines in *****
    ;;
    ;; REC: Recode image classes
    ;; Input image requirements: classes within the range [0, 255] byte
    ;; Output: the same image coverage but with recoded class values
    ;;
    ;; Please specify recoding values with two entries per line.
    ;; GWB_REC/GWB_SPATCON/spatcon.c or recode.c will error/exit if not in correct range.
    ;; Class values not found in the image will be skipped.
    ;; Class values not in the list will not be re-coded.
    ;; If an old value appears on more than one line, the last one listed is used.
    ;;
    ;; Recoding rule:
    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; The first value is the original value and the second the new recoded value:
    ;; original value within [0, 255] byte      new recoded value within [0, 255] byte
    ;; i.e., to recode class 1 to 12 and class 2 to 22 , enter one line each, like:
    ;; 1 12
    ;; 2 22
    ****************************************************************************
    1    12
    2    22
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_REC` command and listing of results in the directory output:

.. code-block:: console

    $ GWB_REC -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_REC using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    Done with: example.tif
    Recode finished sucessfully
    
    $ ls -R output/
    output/:
    clc3class_rec  example_rec  rec.log

    output/clc3class_rec:
    clc3class_rec.tif

    output/example_rec:
    example_rec.tif

Remarks
"""""""

-   The recoded images have the suffix _rec.tif to distinguish them from the original images.
-   To verify the recoding run the command: 
    
    .. code-block:: console
    
        $ gdalinfo -hist <path2image>

Recoding may be useful to quickly setup a forest mask from a land cover map by reassigning specific land cover classes to forest. Please note that most **GWB** modules require a (pseudo) binary forest mask of data type Byte with the assignment:

-   0 byte: missing data (optional)
-   1 byte: Background
-   2 byte: Foreground (i.e., forest)

GWB_RSS
^^^^^^^

This module will conduct the **Restoration Status Summary analysis**. It will calculate key attributes of the current network status, composed of foreground (forest) patches and it provides the normalized degree of network coherence. The result are tabular summary statistics. Details on the methodology and input/output options can be found in the `Restoration Planner <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-RestorationPlanner.pdf>`_ product sheet.

Requirements
""""""""""""

Single band geotiff in data format Byte: 

-   0 byte: missing (optional)
-   1 byte: background
-   2 byte: foreground (forest)

.. warning:: 

    Any other values are considered as missing data

Processing parameter options are stored in the file :code:`input/rss-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_RESTORATION-STATUS parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; RSS: Restoration Status = network coherenceof image objetcs
    ;; Input image requirements: 1b-background, 2b-foreground, optional: 0b-missing
    ;;
    ;; Please specify entry at lines 14 ONLY using the following options:
    ;; line 14: Foreground connectivity: 8 default) or 4 
    ;;
    ;; an example parameter file with default output would look like this:
    ;; 8
    ****************************************************************************
    8
    ****************************************************************************

Example
"""""""

The result is stored in a single csv-file in the directory :code:`output`, listing the statistics for each input image in one line, accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_RSS` command and listing of results in the directory output:

.. code-block:: console

    $ GWB_RSS -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_RSS using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    Done with: example.tif
    RSS finished sucessfully
    
    $ ls -R output/
    output/:
    rss8.csv  rss8.log


Summary statistics for each input image showing the normalized degree of network coherence and additional key network parameters:

.. csv-table:: 
    :header: "FNAME", "AREA", "RAC[%]", "NR_OBJ", "LARG_OBJ", "APS", "CNOA", "ECA", "COH[%]", "REST_POT[%]"

    clc3class.tif,957879.00,23.946975,164,176747,5840.7256,180689,281211.93,29.357771,70.642229
    example.tif,428490.00,42.860572,2850,214811,150.34737,311712,221292.76,51.644789,48.355211

Remarks
"""""""

-   :code:`GWB_RSS` provides a succinct summary of key network status attributes including area, extent, patch summary statistics, equivalent connected area, degree of network coherence, and the restoration potential.
-   As a normalized index, Coherence or its complement Restoration Potential, can be used to directly compare the integrity of different networks or to quantitatively assess changes in network integrity over time.
-   The provision of key network status attributes is essential for any restoration planning.
-   The desktop application `GuidosToolbox <https://forest.jrc.ec.europa.eu/en/activities/lpa/gtb/>`_ provides additional, interactive tools for restoration planning.

With the provision of a normalized degree of network coherence and restoration potential, :code:`GWB_RSS` provides a powerful tool to measure and rank the integrity of forest networks for different regions of interest. This feature may be useful to set priorities for restoration planning or to measure implementation progress and overall success of policy regulations.

GWB_SPA
^^^^^^^

This module will conduct the **Simplified Pattern Analysis**. SPA analyses shape and conducts a segmentation of foreground patches into 2, 3, 5, or 6 feature classes. The result are spatially explicit maps and tabular summary statistics. :code:`GWB_SPA` is a simpler version of :code:`GWB_MSPA`. Details on the methodology and input/output options can be found in the `Morphology <https://ies-ows.jrc.ec.europa.eu/gtb/GTB/psheets/GTB-Pattern-Morphology.pdf>`_ product sheet.

Requirements
""""""""""""

Single band geotiff in data format byte: 

-   0 byte: missing (optional)
-   1 byte: background
-   2 byte: foreground (forest)

Processing parameter options are stored in the file :code:`input/spa-parameters.txt`. 

.. code-block:: text 

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_SPA parameter file: 
    ;; NOTE: do NOT delete or add any lines in this parameter file!
    ;;
    ;; SPA: Spatial Pattern Analysis (2, 3, 5, or 6 classes)
    ;; see https://ies-ows.jrc.ec.europa.eu/gtb/GTB/MSPA_Guide.pdf
    ;; Input image requirements: 1b-background, 2b-foreground, optional: 0b-missing
    ;;
    ;; SPAx will provide an image and summary statistics using 8-connectivity.
    ;; Line 19: enter a single number, representing the number of pattern classes:
    ;; 2: SLF, Contiguous
    ;; 3: Core, Core-Openings, Margin  
    ;; 5: Core, Core-Openings, Edge, Perforation, Margin
    ;; 6: Core, Core-Openings, Edge, Perforation, Islet, Margin
    ;;
    ;; an example parameter file would look like this:
    ;; 5
    ****************************************************************************
    2
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_SPA` command and listing of results in the directory output:

.. code-block:: console

    $ GWB_SPA -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_SPA using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    Done with: example.tif
    SPA2 finished sucessfully
    
    $ ls -R output/
    output/:
    example_spa2  spa2.log

    output/example_spa2:
    example_spa2.tif  example_spa2.txt

Statistics and spatial result of the input image :code:`example.tif` showing a 2-class segmentation (SPA2): Contiguous and Small & Linear Features (SLF):

.. code-block:: text

    SPA2: 8-connected Foreground, summary analysis for image: 
    /home/prambaud/input/example.tif
    
    Image Dimension X/Y: 1000/1000
    Image Area =               Data Area                    + No Data (Missing) Area
            = [ Foreground (FG) +   Background (BG)  ]     +          Missing    
            = [        FG       + {Core-Opening + other BG} ] +       Missing    
    
    ================================================================================
            Category              Area [pixels]: 
    ================================================================================
            Contiguous:                 388899
    +              SLF:                  39591
    --------------------------------------------------------------------------------
    = Foreground Total:                 428490
    + Background Total:                 571240
    --------------------------------------------------------------------------------
    =  Data Area Total:                 999730
    
             Data Area:                 999730
    +          Missing:                    270
    --------------------------------------------------------------------------------
    = Image Area Total:                1000000
    
    
    ================================================================================
            Category    Proportion [%]: 
    ================================================================================
       Contiguous/Data:     38.9004
    +         SLF/Data:      3.9602
    --------------------------------------------------------------------------------
               FG/Data:     42.8606
    --------------------------------------------------------------------------------
         Contiguous/FG:     90.7603
    +           SLF/FG:      9.2397
    ================================================================================
    
    
    ================================================================================
            Category          Count [#]: 
    ================================================================================
            Contiguous:             847
            FG Objects:            2850
                   SLF:            6792
    ================================================================================

.. figure:: ../_images/cli/gwb/example_spa2.png
    :width: 50%

Remarks
"""""""

-   The full version, GWB_MSPA provides many more features and classes.
-   Please use :code:`GWB_MSPA` if you need an edge width > 1 pixel and/or to detect connecting pathways. 

:code:`GWB_SPA` is a purely geometric analysis scheme, which can be applied to any type of raster image. It is ideal to describe the morphology of foreground (forest) patches for basic mapping and statistics, which may be sufficient in many application fields. Advanced analysis, including the detection of connecting pathways require using the full version :code:`GWB_MSPA`.

GWB_SPATCON
^^^^^^^^

This module provides full access to the spatial convolution program **spatcon**. Back in 1992, Kurt Riitters started coding a suite of landscape pattern metrics from a categorical raster map (`Riitters et al. (1995) <https://link.springer.com/content/pdf/10.1007/BF00158551.pdf>`_), (`Riitters et al. (2000) <https://www.srs.fs.usda.gov/pubs/ja/ja_riitters006.pdf>`_). Over time, **spatcon** grew to now offer 21 spatial convolution metrics, which are summarised in a dedicated `Technical Note <https://github.com/ec-jrc/GWB/blob/main/tools/external_sources/GWB_SPATCON-TechnicalNote.pdf>`_.
Spatcon conducts a moving window, or focal analysis and results in a spatially explicit map for the selected metric.

Requirement
"""""""""""

Single band geotiff in data format byte. Metric-specific requirements and processing options are stored in the file :code:`input/p223-parameters.txt`. 

.. code-block:: text

    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; GWB_SPATCON parameter file: 
    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; For all output types, missing indicates the input window contained only
    ;; missing pixels or missing pixel adjacencies.
    ;; Missing values are coded as 0 (rounded byte), or -0.01 (float precision).
    ;;
    ;; Rule options at the end of this file and between the lines in *****
    ;; 1 = Majority (most frequent) pixel value
    ;; 6 = Landscape mosaic (19-class version)
    ;; 7 = Landscape mosaic (103-class version)
    ;; 10 = Number of unique pixel values
    ;; 20 = Median pixel value
    ;; 21 = Mean pixel value
    ;; 5x = Pixel diversity:
    ;;    51 = Gini-Simpson pixel diversity
    ;;    52 = Gini-Simpson pixel evenness
    ;;    53 = Shannon pixel evenness
    ;;    54 = Pmax
    ;; 7x = Pixel adjacency (with regard to order of pixels in pairs):
    ;;    71 = Angular second moment
    ;;    72 = Gini-Simpson adjacency evenness
    ;;    73 = Shannon adjacency evenness
    ;;    74 = Sum of diagonals
    ;;    75 = Proportion of total adjacencies involving a specific pixel value
    ;;    76 = Proportion of total adjacencies which are between two specific pixel values
    ;;    77 = Proportion of adjacencies involving a specific pixel value which are adjacencies with that same pixel value
    ;;    78 = Proportion of adjacencies involving a specific pixel value which are adjacencies
    ;;             between that pixel value and another specific pixel value
    ;; 8x = Pixel value density and ratios
    ;;    81 = Area density
    ;;    82 = Ratio of the frequencies of two specified pixel values
    ;;    83 = Combined ratio of two specific pixel values
    ;;
    ;; for more details on SPATCON, see the Technical Note and/or source code at:
    ;; https://github.com/ec-jrc/GWB/tree/main/tools/external_sources/
    ;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
    ;; spatcon parameters for all rules (some rules use only a subset of all parameters)
    ;; R (mapping rule in {1,6,7,10,20,21,51,52,53,54,71,72,73,74,75,76,77,78,81,82,83})
    ;; W (window size - minimum 3, maximum < x or y dimension of input map)
    ;; A (first target code - required for mapping rules 75, 76, 77, 78, 81, 82, 83. Default = 0)
    ;; B (second target code - required for mapping rules 76, 78, 82, 83. Default = 0)
    ;; H (handling of missing values or adjacencies: 1-ignore. 2-include;  -no effect for mapping rules 21, 82, 83. Default = 1)
    ;; F (output precision: 0 = 8-bit byte. 1 = 32-bit float. Float is not available for mapping rules 1, 6, 7, 10. Default = 0)
    ;; Z (Request re-code of input pixels. 0 = No. 1 = Yes. Default = 0)
    ;; M (AFTER optional re-coding (z = 1), the pixel value that is missing. Default = 0)
    ;;
    ;; NOTE: parameters R and W are mandatory. Parameters that are not specified will use their default value.
    ;; Example parameter file for running spatcon rule Majority and using a 27x27 window:
    ;; R 1
    ;; W 27
    ****************************************************************************
    R 1
    W 27
    A 2
    B 3
    H 1
    F 0
    Z 0
    M 0
    ****************************************************************************

Example
"""""""

The results are stored in the directory :code:`output`, one directory for each input image accompanied by a log-file providing details on computation time and processing success of each input image.

:code:`GWB_SPATCON` command and listing of results in the directory output:

.. code-block:: console

    $ GWB_SPATCON -i=/home/prambaud/input -o=/home/prambaud/output
    IDL 8.8.3 (linux x86_64 m64).
    (c) 2022, Harris Geospatial Solutions, Inc.

    GWB_SPATCON using:
    dir_input= /home/prambaud/input
    dir_output= /home/prambaud/output
    % Loaded DLM: TIFF.
    Done with: clc3class.tif
    Done with: example.tif
    RULE 1 finished sucessfully
    
    $ ls -R output/
    output/:
    clc3class_rule1_27 example_rule1_27  rule1_27.log

    output/clc3class_rule1_27:
    clc3class_rule1_27.tif
    
    output/example_rule1_27:
    example_rule1_27.tif

Example spatial result of the input image :code:`example.tif` for Rule 1, showing the majority pixel value in a 27x27 moving window:


.. figure:: ../_images/cli/gwb/example_rule1_27.png
    :width: 50%

Remarks
"""""""

-   All density or adjacency metrics are scale-dependent (specified by the size of the moving window).
-   Some **spatcon** moving window metrics form the base for other derived analysis schemes, such as :code:`GWB_LM` (Rule 6,7), :code:`GWB_FAD` (Rule 81), :code:`GWB_FRAG` (Rule 76, 81).

Both, Density (Rule 81) and Contagion (Rule 77) add a first spatial information content on top of the primary information of forest, forest amount. Information on forest Density and Contagion is an integral part of many national forest inventories and forest resource assessments. However, the derived products Fragmentation (:code:`GWB_FRAG`) and Landscape Mosaic (:code:`GWB_LM`) may be easier to communicate.



.. spelling:word-list::

    cp
    sav
    clc
