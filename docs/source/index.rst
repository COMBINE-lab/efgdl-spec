Welcome to EFGDL's specification and documentation!
===================================================

The extended sequence fragment geometry descriptions language (EFGDL) is a grammar designed to describe the layout of information encoded in sequenced fragments. Specifically, it is initially designed to support parsing different sequencing chemistries and protocols that are used to encode information in the reads (for example, cell barcodes and UMIs in single-cell sequencing). It is capable of processing both “simple” and “complex” geometries, and it is also capable of "transforming" complex geometries into simpler ones to be directly used with existing downstream tools, or to simplify subsequent parsing and analysis.

Check out the :doc:`usage` section for further information.

.. note::

   This project is under active development.

Contents
--------

.. toctree::

   usage
   intervals
   transformations
   example
