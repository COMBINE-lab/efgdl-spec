More with ``map``
=================

Here we will go through an example of harnessing the power of the ``map`` transformation for processing the `SPLiT-seq protocol <https://teichlab.github.io/scg_lib_structs/methods_html/SPLiT-seq.html>`_. SPLiT-seq is an example of a new protocol which requires specialized pre-processing pipelines given its use of complex combinatorial indexing. EFGDL has no problem specifying the complexity of the SPLiT-seq protocol.

We will make use of the powerful ``map`` transformation in order to process the combinatorial indexing of a SPLiT-seq read. 

As the write-up explains the information which we intend to isolate is in the second read. Additionally, in the geometry there are two linker segments flanked by eight base barcodes and starting with a 10 base UMI. We will build our EFGDL from the bottom up.

.. code-block::

    brc1: b[8]
    brc2: b[8]
    brc3: b[8]
    umi: u[10]
    1{r<read1>:}2{<umi><brc1>x[30]<brc2>x[30]<brc3>}

Now we have a very simple description of the geometry of the SPLiT-seq protocol. At this point we want to make use of the ``map`` transformation. For each instance of a barcode, we need to map it to a library map it to another known sequence.

Here is a short example of what we can do with the ``map`` transformation and it should give some intuition for using ``map`` in ones own transformations. Here is an example of a file which contains the barcodes and the sequences which the barcodes are to be mapped to.

.. code-block::

    # ./barcode_map.txt
    # brc       mapped-to
    CATTTGGA	AGGTAATA
    GAGCACAA	CGTGGTTG
    GTCGCGCG	GACAAAGC
    GTTACGTA	GGGCGATG
    ...

Consder a read ``AGAGTCATTAGAGCACAA...ATG``, ``umi`` binds to the first 10 bases, then ``brc1`` binds to the next 8. For this we look up in the ``./barcode_map.txt`` and we find a hit. Line two we match the barcode and the ``map`` function will replace the mapped barcode with the sequence in the second column.

.. code-block::

    AGAGTCATTAGAGCACAA...ATG -> AGAGTCATTACGTGGTTG...ATG

Here we mapped the barcode without considering mismatches. There is a complementary transformation to ``map``, which allows for a specification of number of mismatches to allow when mapping sequences in reads. In this example we will use ``map_to_mismatch`` and allow for a single mismatch. There are two important notes on using ``map_with_mismatch``, firstly, if multiple barcodes in the map file match to a read barcode with a number of mismatches, the first matched sequence will be used. Additionally, time complexity increases with the introduction of mismatches. 

The ``map_with_mistmatch`` takes the interval, map file, a fallback, and number of mismatches allowed. In this case, if a barcode is not mapped we want to leave it in place. So for the fallback we will use the ``self`` argument on its own. For SPLiT-seq we will use a single mismatch. So we can put this all together to write the final EFGDL description for SPLiT-seq.

.. code-block::

    brc1: map_with_mismatch(b[8], $0, self, 1)
    brc2: map_with_mismatch(b[8], $0, self, 1)
    brc3: map_with_mismatch(b[8], $0, self, 1)
    umi: u[10]
    1{r<read1>:}2{<umi><brc1>x[30]<brc2>x[30]<brc3>}