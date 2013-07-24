# Notes on the Implementation and Semantics of omd

Since there are no errors in  Markdown, it means taht everything has a
meaning.  Sometimes, one has to imagine a meaning that is not too much
nonsense.


## Lists

### Problem Description
There are several semantics for a "broken" list such as the following one:
```
 * Indentation 1, Element 1
 * Indentation 1, Element 2
   * Indentation 3, Element 1
   * Indentation 3, Element 2
  * Indentation 2, Element 1
  * Indentation 2, Element 2
   * Indentation 3, Element 1 (not 3)
 * Indentation 1, Element 3
```

I have chosen the following semantics, because to me that it's the less nonsense I have ever thought about:

### Semantics
Let N be the indentation level of the current element.
- If N is equal to the previous indentation, then it's still the same list as the current one.
- If N is greater than the previous indentation, then it's a new list.
- If N is lesser than the previous indentation, then I check its predecessors: 
  * if N is the level of a predecessor and no other level inbetween is lesser, then it means that the current item belongs to a list that hasn't been closed yet, so I close the current list and I delegate the rest to the closest parent (which does mean that the current item will be processed _again_).
  * else, it means that it's a kind of wrong level (don't forget N is smaller than the previous indentation), so I close the current list and open a new one with the current item.


```
 * Indentation 1, Element 1
 * Indentation 1, Element 2
% here I do not close (I1), and I open for the next one (I3)
   * Indentation 3, Element 1
   * Indentation 3, Element 2
% here I close (I3) and open for the next one (I2)
  * Indentation 2, Element 1
  * Indentation 2, Element 2
% here I close (I2) and open for the next one (I3)
   * Indentation 3, Element 1 (not 3)
% here I close (I3) and continue for the next one (I1)
 * Indentation 1, Element 3
```