.. py:currentmodule:: lsst.ap.pipe

.. _ap-pipe-getting-started-gen2:

############################################
Getting started with the AP pipeline (Gen 2)
############################################


This page explains how to set up a Gen 2 data repository that can then be processed with the AP Pipeline (see :doc:`pipeline-tutorial-gen2`).
This is the established Science Pipelines workflow, and is compatible with a variety of existing pipelines and tools.
However, it is expected to be phased out in the future in favor of the Gen 3 framework.

If you already have a Gen 3 data repository or want to learn the new framework, see :doc:`getting-started`.

.. _section-ap-pipe-installation-gen2:

Installation
============

:doc:`lsst.ap.pipe <index>` is available from the `LSST Science Pipelines <https://pipelines.lsst.io/>`_.
It is installed as part of the ``lsst_apps`` and ``lsst_distrib`` metapackages.


.. _section-ap-pipe-ingesting-data-files-gen2:

Ingesting data files
====================

LSST-style image processing typically operates on Butler repositories and does not
directly interface with data files. :doc:`lsst.ap.pipe <index>` is no exception.
The process of turning a set of raw data files and corresponding calibration
products into a format the Butler understands is called ingestion. Ingestion
can be somewhat camera-specific, and is outside the scope of the AP Pipeline.

.. TODO: Cut or condense above paragraph and link to ingestion-related docs.

A utility to ingest data before running :doc:`lsst.ap.pipe <index>`
is available in :ref:`ap_verify <ap-verify-run-ingest>`. However, this works
only on datasets which adhere to the :doc:`ap_verify dataset </modules/lsst.ap.verify/datasets>` format.
Alternately, you may use a pre-
ingested dataset or manually ingest files yourself following the directions
for a given ``obs_`` package.

A standard ingestion workflow for DECam looks something like

.. prompt:: bash

   ingestImagesDecam.py input_loc --filetype raw path/to/raw/files --mode=link
   ingestCuratedCalibs.py input_loc --calib calib_loc $OBS_DECAM_DATA_DIR/decam/defects
   ingestCuratedCalibs.py input_loc --calib calib_loc $OBS_DECAM_DATA_DIR/decam/crosstalk
   ingestCalibs.py input_loc --calib calib_loc /path/to/biases/and/flats --mode=link --validity 999


.. _section-ap-pipe-required-data-products-gen2:

Required data products
======================

For the AP Pipeline to successfully process data, the following is required:

- **Raw science images** and `reference catalogs 
  <https://community.lsst.org/t/creating-and-using-new-style-reference-catalogs/1523>`_
  ingested into a main Butler repository

  - The reference catalogs must be in a directory called :file:`ref_cats` with subdirectories
    for each catalog containing the appropriate catalog shards.
    We recommend using Pan-STARRS for photometry and Gaia for astrometry.
    An example :ref:`config file <command-line-task-config-howto-configfile>` for using these two catalogs can be found in the `ap_verify_hits2015`_ repository.

- **Calibration products** (biases, flats, and possibly others)
  ingested into a Butler repository that you must specify with the ``--calib`` flag on
  the command line at runtime

  - To check if this requirement has been satisfied, you can inspect the
    :file:`calibRegistry.sqlite3` created in this repository and ensure the information
    in the tables is accurate

- **Template images** (of type ``deepCoadd`` by default) for difference imaging
  must be either in the main Butler repository or in another location you may
  specify with the ``--template`` flag on the command line at runtime

.. TODO: update default for DM-14601

.. _ap_verify_hits2015: https://github.com/lsst/ap_verify_hits2015/

A sample dataset from the `DECam HiTS survey <http://iopscience.iop.org/article/10.3847/0004-637X/832/2/155/meta>`_ 
that works with ``ap_pipe`` in the :doc:`/modules/lsst.ap.verify/datasets` format
is available as `ap_verify_hits2015`_. However, this dataset must be
ingested as described in :ref:`section-ap-pipe-ingesting-data-files-gen2`, and the reference
catalog files must be decompressed and extracted.

Please continue to :doc:`Pipeline Tutorial <pipeline-tutorial-gen2>` for more
details about running the AP Pipeline and interpreting the results.
