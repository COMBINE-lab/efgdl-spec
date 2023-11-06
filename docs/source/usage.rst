Usage
=====

Basic FGDL Examples
-------------------

The EFGDL builds off of the FGDL (where the E stands for "extended").  The FGDL is a simple but powerful grammar for describing fragment geometries.  Below are some example FGDL specifications, which are also valid EFGDL.  A basic description of the FGDL, and the formal grammar for that simplified version, can be found `here <https://hackmd.io/@PI7Og0l1ReeBZu_pjQGUQQ/rJMgmvr13>`_.

* 10x Chromium v3 3' : ``1{b[16]u[12]}2{r:}``
* 10x Chromium v2 3' : ``1{b[16]u[10]}2{r:}``
* SciSeq3 : ``1{b[9-10]f[CAGAGC]u[8]b[10]}2{r:}``

FGDL vs. EFGDL
--------------

EFGDL builds on the basic FGDL in several ways.  One such way is by allowing components of the description to be defined across 
multiple lines and bound to variables that are later used in the description.  For example, consider the following description:

.. code-block::

   anchor = f[CATAGC]
   1{x:<anchor>x:}2{r:}


here, the first line defines a variable called "anchor", which is then referred to in the fragment description on the second line.
When a variable ``x`` is created in the EFGDL, it can be referred to in the fragment description with the syntax ``<x>``.


In addition to having the ability to *match* input sequences against a description, the EFGDL also has the ability to describe
*transformations* of an input string into an output string.  Consider the following example:

.. code-block::

   brc = b[6]
   anchor = f[TTTTT]
   1{<brc><anchor>x:}2{r<read>:}
   -> 1{<brc>pad(<anchor>, 3, A)<read>}

This example demonstrates 3 separate features.  It pre-defined two variables, ``brc`` and ``anchor``. It also creates an *inline* variable
binding ``<read>``.  It subsequently transforms the input geometry into the output geometry (the string specified after the ``->``).
Let's break down these components.

In the string ``1{<brc><anchor>x:}2{r<read>:}``, the component ``r<read>:`` is an *inline* variable binding.  It says to match ``r:`` (i.e. 
the string starting from the current offset until the next geometry component, in this case the rest of the string, and bind it to a variable
with the name given inside of the ``<>``, in this case ``read``).  The inline variable binding defined here can then be used in the transformation
that follows.

The ``->`` syntax says that, upon succesfully matching an input sequence to ``1{<brc><anchor>x:}2{r<read>:}``, we wish to produce an 
output sequence that consists of the geometry following the ``->``, with the bound variables properly interpolated.  In this case, the 
output should look like ``1{<brc>pad(<anchor>, 3, A)<read>}``.  That is, it is a single read that will consist of whatever matched the 
``bcr`` variable, followed by the anchor padded with 3 occurrences of the ``A`` character, followed by whatever matched the ``read`` variable 
defined inline before the transformation.  The only component of this output we have not yet described is the ``pad`` function.

Unlike FGDL, the EFGDL defines a number of functions that can be applied during transformation to one or more bound variables. The 
``pad`` function takes a bound variable (in this case ``anchor``), an integer (in this case ``3``), and a nucleotide (in this case ``A``)
and produces an output string that is equivalent to ``<anchor>AAA``.  The ``pad`` function is one of the simplest EFGDL functions.  We 
given an example of a more sophisticated one below. For a description of all supported functions, please read the documentation 
section on the EFGDL functions.


.. code-block::

   brc = b[8]
   1{map(<brc>, $0, pad_to(self, 10, A))x:}2{r:}
   -> 1{<brc>}
  

The above example attempts to match a read pair consisting of a barcode of length 8, followed by anything else in read 1 (i.e. ``x:``), and then 
a second read that can consist of anything.  The interesting component of this description though is the composition of the ``map`` and ``pad_to``
fuctions. Let's look at them from the inside out.

The ``pad_to`` function is much like the ``pad`` function, except that, rather than padding *with* a specified number of the given character, it 
pads the first input argument *to* the length specified by the second argument, using the character provided in the third argument.  For example, 
``pad_to(ACCG, 6, T)`` would produce an output of ``ACCGTT``.  In the example above, the first input arguemnt to ``pad_to`` is the special ``self``
parameter.  To understand what this is, we must understand what the ``map(P, F, R)`` function does.

The ``map`` function is one of the more sophisticated functions in the ``EFGDL``.  It takes 3 arguments; the first is a pattern to match or bound variable.
The second is a "filter list", which is a mapping of input patterns to output patterns.  If the variable matches a listed input pattern, then it will be 
substituted with the corresponding output patterm.  The third argument is a function that will be run on the first input in the case that it *does not*
match some pattern in the filter list.  For example, consider that ``<brc>`` is ``AACTGGTG``, and consider that our filter list is the simple mapping:

```
AACTGGTG  GTGCACCTGG
```

Then in this case the result of our ``map`` expression will simply be ``GTGCACCTGG``.  However, assume instead that our filter list was:

```
AAGCAGTC  GTGCACCTGG
```

In this case, our ``<brc>`` pattern does not match a pattern in our filter list.  In that case, the output of the ``map`` expression will be 
``AACTGGTGAA``.  That is, it is simply our input pattern ``<brc>`` padded to length 10 with ``A``. This sophisticated example also demonstrates
another capability of the EFGDL - composition.  The functions defined in EFGDL can be composed to build larger and more complex expressions.
This composition allows a relatively small number of functions to cover a large number of different use cases.


