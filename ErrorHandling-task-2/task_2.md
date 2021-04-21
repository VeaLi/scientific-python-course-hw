# Task 2
[Matching Random Motifs](http://rosalind.info/problems/rstr/)

Problem
-------

Our aim in this problem is to determine the probability with which a given motif (a known promoter, say) occurs in a randomly constructed genome. Unfortunately, finding this probability is tricky; instead of forming a long genome, we will form a large collection of smaller random strings having the same length as the motif; these smaller strings represent the genome's substrings, which we can then test against our motif.

Given a probabilistic event AAA, the complement of AAA is the collection AcAcA^{\\textrm{c}} of outcomes not belonging to AAA. Because AcAcA^{\\textrm{c}} takes place precisely when AAA does not, we may also call AcAcA^{\\textrm{c}} "not AAA."

For a simple example, if AAA is the event that a rolled die is 2 or 4, then Pr(A)\=13Pr(A)\=13\\mathrm{Pr}(A) = \\frac{1}{3}. AcAcA^{\\textrm{c}} is the event that the die is 1, 3, 5, or 6, and Pr(Ac)\=23Pr(Ac)\=23\\mathrm{Pr}(A^{\\textrm{c}}) = \\frac{2}{3}. In general, for any event we will have the identity that Pr(A)+Pr(Ac)\=1Pr(A)+Pr(Ac)\=1\\mathrm{Pr(A)} + \\mathrm{Pr}(A^{\\textrm{c}}) = 1.

Given: A positive integer N≤100000N≤100000N \\leq 100000, a number xxx between 0 and 1, and a DNA string sss of length at most 10 bp.

Return: The probability that if NNN random DNA strings having the same length as sss are constructed with GC-content xxx , then at least one of the strings equals sss. We allow for the same random string to be created more than once.

Sample Dataset
--------------

90000 0.6
ATAGCCGA

Sample Output
-------------

0.689


```python
import re

import numpy as np
from scipy.stats import binom
```


```python
S = 'ATAGCCGA'
gcProb = 0.6
N = 90000
```


```python
def get_at_least_prob(S, gcProb, N):
    
    """
    Return: The probability that if N random DNA strings having the same length as s 
    are constructed with GC-content x, then at least one of the strings equals s
    """
    
    gc = lambda s: len(re.findall(r"[GC]", s))

    # string probability 0.2*0.2*0.2*0.3*0.3*0.3*0.2*0.3 given GC content
    sProb = (gcProb / 2)**gc(S) * ((1 - gcProb) / 2)**(len(S) - gc(S))

    # get at least one or more, 
    # same as pbinom(0, 90000, 1.2960000000000001e-05, lower.tail = F) in R
    return np.round(1 - binom.cdf(0, N, sProb), 3)
```


```python
get_at_least_prob(S, gcProb, N)
```




    0.689


