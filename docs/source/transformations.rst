Transformations
===============

Here is where you will find a detailed description with examples for the allowable transformations with EFGDL.

All transformations operate on a matched sequence interval. 

Just like statically typed functions transformations in EFGDL can only transform specific types of intervals into other types of intervals. With that in mind, transformations
can be commposed together to allow for more complicated transformations of certain intervals. Like ambiguous interval specification invalid composition of functions is indicated
to the user at compile time with readable error messages. 

All transformations cannot transform ``void`` intervals. These would be intervals either from a discard specifier or one that was previously removed with ``remove`` transformation. 

Transformations and Usage
-------------------------

```
rev(I)
```

Operates on an arbitrary non-void interval and reverses the sequence.


```
revcomp(I)
```

Operates on an abritrary non-void interval and applies a reverse compliment transformation


```
trunc(I, n)
```

Operates on an abitrary non-void interval and requires an additional input of a non-negative integer and applies a truncate **by** transformation. 
The interval will be truncated **by** the specified length from the *right* end of the sequence. 


```
trunc_left(I, n)
```

Operates on an abitrary non-void interval and requires an additional input of a non-negative integer and applies a truncate **by** transformation. 
The interval will be truncated **by** the specified length from the *left* end of the sequence. 


```
trunc_to(I, n)
```

Operates on an abitrary non-void interval and requires an additional input of a non-negative integer and applies a truncate **to** transformation.
The interval will be truncated **to** the specified length from the *right* end of the sequence.

```
trunc_to_left
```

Operates on an abitrary non-void interval and requires an additional input of a non-negative integer and applies a truncate **to** transformation.
The interval will be truncated **to** the specified length from the *left* end of the sequence.

```
remove
```

Operates on an arbitrary non-void interval. Remove will remove the interval from the sequence and return a void interval, thus no further transformations
can be applied to intervals which have been removed.


```
pad(I, n, char)
```

Operates on an abritrary non-void interval, requires two additional arguments a non-negative integer and the character to pad the sequence with and applies
a pad **by** transformation. The interval will be padded **by** the specified number of instances of the character to the *right* of the sequence.


```
pad_left(I, n, char)
```

Operates on an abritrary non-void interval, requires two additional arguments a non-negative integer and the character to pad the sequence with and applies
a pad **by** transformation. The interval will be padded **by** the specified number of instances of the character to the *left* of the sequence.

```
pad_to(I, n, char)
```

Operates on an abritrary non-void interval, requires two additional arguments a non-negative integer and the character to pad the sequence with and applies
a pad **to** transformation. The interval will be padded **to** the specified number of instances of the character to the *right* of the sequence.

```
pad_to_left(I, n, char)
```

Operates on an abritrary non-void interval, requires two additional arguments a non-negative integer and the character to pad the sequence with and applies
a pad **to** transformation. The interval will be padded **to** the specified number of instances of the character to the *left* of the sequence.

```
norm(I)
```

Operates on variable length intervals. The normalize method will make the length of variable lengthed intervals all the same length. The final length is calculated by the following: 

```final length = interval upper bound - matched sequence length + ceiling(log4(interval upper bound - interval lower bound + 1))```

The ``log4`` calculation is the extra length to add to normalize, for each additional base a different nucleotide is added depending on the starting length of the matched interval.
This is to ensure two sequences which were not the same cannot become the same with additional bases added to the end.

```
map(I, A, F)
```

Operates on a ranged or fixed interval and takes two additional arguments. The first is a map list of sequences and the associated sequence to replace if the former is matched in the provided interval.
The last argument is the fallback option if no sequence is found in the map list. The fallback option can be a transformation with ``self`` as the interval or just ``self`` if the interval should be left alone if not mapped.

For an example of the usage of ``map`` see usage page.

```
map_with_mismatch(I, A, F, n)
```

Operates on a ranged or fixed interval and takes three additional arguments. The only difference between this transformation and the above ``map`` transformation is that the ``n`` argument is an allowable edit
distance which is used to match the interval to sequences in the map list. 

```
filter_within_dist(I, A, n)
```

Operates on a ranged or fixed interval and takes two additional arguments, the allow list and the distance to consider when matching the interval sequence to sequences in the allow list. This transformation will filter out
reads if the given interval does not match, within a distance, any of the entries in the allow list.

```
hamming(F, n)
```

Operates on a fragment specified fixed length interval and takes an additional non-negative integer argument. Hamming is a bit different from the other transformation above, hamming must be the first composed transformation on 
a fragment specified fixed length interval to be valid. Unlike the above transformations, hamming operates when the interval is attempting to match to the read, rather than after the interval has been matched. A fragment specified 
fixed length interval without the hamming transformation matches only to perfect matches to the provided sequence, hamming allows for matching of sequences within a certain hamming distance. 

