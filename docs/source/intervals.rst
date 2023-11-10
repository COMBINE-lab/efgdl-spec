Intervals
=========

Overview
--------

The first part of building an EFGDL specification is enumerating the intervals of the sequence. 

With the EFGDL grammar one can specify three distinct length intervals:

* Fixed Length intervals
* Variable Length intervals
* Unbounded intervals

In addition to the length intervals there are five specifiers to add more information to the type of interval:

* Barcode
* Unique Molecular Identifier (UMI)
* Discard
* Read
* Fragment

These intervals are the building blocks of all EFGDL specifications. 
Length intervals and interval specifiers are to be used together, below is overview of the allowable grammar of composing them.

Barcode, Unique Molecular Identifier, and Read Specifier
--------------------------------------------------------

The barcode, UMI, and read specifier can be used with any of the length intervals. These specifiers have no impact on how the sequence is parsed.

Prefixes for each specifier

* Barcode -> ``b``
* UMI -> ``u``
* Read -> ``r``

Examples (swap ``b`` with other prefixes for other specifiers) 

Fixed Length Barcode Interval

.. code-block::

    b[10]

Variable Length Barcode Interval

.. code-block::

    b[9-10]

Unbounded Length Barcode Interval

.. code-block::

    b:

While there is no difference between using either of these three specifiers, when creating an EFGDL specification the goal is two fold:

(1) Describe how to parse and transform given sequences, and
(2) Have an easily interpretable format for sequencing protocols

Having these three specifiers helps with the second goal. Labeling an interval with ``b`` and another with ``u`` means these two intervals are distinct even though they may be thought of as the same in the case of pre-processing sequences.

Discard Specifier
-----------------

The discard specified can be used with any of the length interval. A discard specifier will remove the matched interval. To specify a discard use prefix ``x`` the same way as
used above with ``b`` for barcode.


Fragment Specifier
------------------

The fragment specifier does not operate in the same way as the above specifiers. The fragment specifier is mean to match known sequences based on their sequence rather than just length.

To declare a fragment use prefix ``f`` and provide the known sequence, like below:

.. code-block::
    
    f[CAGAGC]

This will by default search for this sequence without any mismatches. Composable transformations and methods can create fragment matching intervals within certain hamming distances but the primitive fragment specifier operates as an exact match.

The fragment specifier cannot be used with the length intervals. 


Grammar of Intervals
--------------------

Building a specifications from intervals requires creating an unambiguous chain of intervals. Below is an example of an ambiguous chain of intervals.

.. code-block::

    b[9-10]r:

The ambiguoity of this specification is that there is no way to know whether to match the barcode interval with 9 or 10 bases. For a chain of intervals to be complete
all non-fixed length intervals must either be the end of the chain or be followed by a fragment specified interval.

Below is an unambiguous chain of intervals to display how to make specifications unambiguous.

.. code-block::

    r:f[TTTTT]b[9-10]f[AAAAA]b[11-13]

Each of the non-fixed length intervals are anchored to their right with a fragment specified intervals. The interpreter matches intervals like this as a unit. First finding the
fragment specified intervals then matching the variable length intervals. The barcode at the end is completely valid as there is no ambiguity about where to match this interval.

EFGDL compiler provides useful error messages to help with making unambiguous chains of intervals.