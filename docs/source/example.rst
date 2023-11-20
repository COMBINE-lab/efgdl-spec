Example
=======

Here we will walk you through writing your first EFGDL file for the **SciSeq3** protocol.

As specified in the usage tab the **SciSeq3** procotol in FGDL is 

.. code-block::

    1{b[9-10]f[CAGAGC]u[8]b[10]}2{r:}

This tells us that there will be two reads, in the first read ther will be a variable length barcode of length 9-10. To the right of the barcode is known sequence, ``CAGAGC``. Then a 8 base UMI and then a 10 base barcode. In the second read we will have an unbounded read.

Now this is simple but how can be make this clearer, and what would make further analysis of these sequences easier.

Let's start with the known anchor sequence. It makes sense to match this sequence with at least one error so let's make a variable name called anchor and wrap it in a ``hamming`` transformation.

.. code-block::

    anchor = hamming(f[CAGAGC], 1)
    1{b[9-10]<anchor>u[8]b[10]}2{r:}

Now it is easy to see at a glance that there is an anchor sequence in the middle of the read. Let's also name our other intervals with variables so we can group barcodes together at the end of the transformation.


.. code-block::

    anchor = hamming(f[CAGAGC], 1)
    brc1 = b[9-10]
    brc2 = b[10]
    umi = u[8]
    read2 = r:
    1{<brc1><anchor><umi><brc2>}2{<read2>}

Now that we have variables, we can organize what we want our final transformed sequence to look like. We don't want the anchor to be present at the end and we would like the barcodes to be grouped together. We can do this with a transformation expression at the end of our EFGDL file.

.. code-block::

    anchor = hamming(f[CAGAGC], 1)
    brc1 = b[9-10]
    brc2 = b[10]
    umi = u[8]
    read2 = r:
    1{<brc1><anchor><umi><brc2>}2{<read2>}
    -> 1{<brc1><brc2><umi>}2{<read2>}

Now we have a simple final representation of our sequence with barcodes grouped at the beginning and no unneccessary information.

Let's make use of one more transformation -- ``norm``. This transformation will make ``brc1`` the same length for all reads no matter if it was 9 or 10 bases to begin with. For downstream tools which need the exact coordinates with barcodes and UMI's this transformation makes every read the exact same.

.. code-block::

    anchor = hamming(f[CAGAGC], 1)
    brc1 = norm(b[9-10])
    brc2 = b[10]
    umi = u[8]
    read2 = r:
    1{<brc1><anchor><umi><brc2>}2{<read2>}
    -> 1{<brc1><brc2><umi>}2{<read2>}

Now we have a final representation of the **SciSeq3** protocol in EFGDL.