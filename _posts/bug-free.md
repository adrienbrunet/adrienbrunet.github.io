---
layout: post
title: And you think you're bug-free
categories: [Python, test]
tags: [code, test, python]
comments: true
description: Integer overflow is not "in" anymore. Find the new hype. And write battle-tested code with Hypothesis.
---

Old and classic computing problems can't get old. A bug might be hidding another one.

<p align="center"><img src="/assets/images/ladybug.jpg" alt="a cute ladybug"></p>

I had a look recently at "[_Beautiful Code_](http://shop.oreilly.com/product/9780596510046.do 'O'reilly link to Beautiful Code Book')".

One of my favorite chapter, called "_Beautiful Test_" takes an example from "[_Programming Pearls_](https://www.amazon.com/Programming-Pearls-2nd-Jon-Bentley/dp/0201657880 "Amazon link to buy Progamming pearls")" about binary search.

The fact is: binary search is quite simple to explain but you have a trillion ways to implement it wrong.

In particular, with big anough arrays, you can raise exceptions in most implementation. **INTEGER OVERFLOW MAGIC**.

The naive implementation in java given is

```java
public static int buggyBinarySearch(int[] a, int target) {
    int low = 0;
    int high = a.length - 1;
    while (low <= high) {
        int mid = (low + high) / 2;
        int midVal = a[mid];
        
        if (midVal < target)
            low = mid + 1;
        else if (midVal > target)
            high = mid - 1;
        else
            return mid;
    }
     return -1;
}
```

If this is pretty straightforward, the fact that the bug lies on the line ` int mid = (low + high) / 2;` can be surprising at first.
If you want to read more on that, you can read this chapter in "_Beautiful Code_", jUnit is introduced and other implementations are discussed.

Reading that, I realized that even something as obvious as the mean between two numbers can be a tricky thing to implement correctly. And don't even try to do that without test.

So, it's fun and all but what happens in Python? Do we have Integer Overflow? Spoiler Alert: No.

```python
import sys

m = sys.maxsize
print(type(2 * m))
```
```bash
> <class 'int'>
```

So... I'm tempted to think that I can safely write a `mean` function like this (file `great_math.py`):

```python

def mean(a, b):
    return (a + b) / 2
```
It seems harmless, right? I hear someone saying my code is not tested, I can't tell if it works. Mmkay. Here we go. (file `test_mean.py`)

```python
import sys
from great_math import mean

def test_mean():
    assert mean(0, 0) == 0
    assert mean(1, 2) == 1.5
    overflow_test = mean(sys.maxsize, sys.maxsize + 2)
    assert overflow_test == sys.maxsize + 1
    assert type(overflow_test) == float
```
And... it'is OK !

But wait, we were talking about integer overflow, we don't have it but now we're using floats (as the division gives back a float).

That's not my point. This implementation just feels ok for now. Indeed, I can't think of something that could go wrong. Muahaha, how naive am I. If a bug is solved, that does not mean we now are bug-free.
We are just sure one particular bug won't happen again. Thanks you beautiful test.

If I can't think of a failing test, that's only because I'm a weak developper. Some people get my ass (and yours probably) covered... Let's discover [**Hypothesis**](https://hypothesis.readthedocs.io/en/latest/).

Hypothesis allow you to test code a thousand times with different inputs and ensure you're not missing some obvious mistakes that a few tests (like my previous `test_mean`) won't cover.

Here we go, file `test_hypothesis.py`, let's run 50000 random tests. We need to test something simpler than the real value expected. Indeed, the value are generated on the fly, I can't say in advance what the result should be. I used this really stupid assumption: the mean should be in between the two other elements.

```python
from hypothesis import given, settings
from hypothesis import strategies as st

from mean import mean


@given(st.floats(), st.floats())
@settings(max_examples=50000)
def test_mean(a, b):
    assert min(a, b) <= mean(a, b) <= max(a, b)
```

And.... That fails: `Assertion Error` with `a` or `b` being `nan`.

Well, :clap: :clap: Hypothesis, good job. But, that's something I don't even want to think about. Let's get rid of that.

I saved you one iteration and added a check for both "nan" values and "infinity".
`@assume` skips the test if the expression inside returns false.

```python
from math import isinf, isnan

from hypothesis import assume, given, settings
from hypothesis import strategies as st

from mean import mean


@given(st.floats(), st.floats())
@settings(max_examples=50000)
def test_mean(a, b):
    assume(not isnan(a) and not isnan(b) and not isinf(a) and not isinf(b))
    assert min(a, b) <= mean(a, b) <= max(a, b)

```
That time should be good right? And..... No ! It fails again!
Looks like `mean(-9.9792015476736e+291, -1.7976931348623157e+308)` equals `-inf`

Whaaaaat?

Here we go, we faced a similar problem than before but with floats. I guess you want to do some computing magic, you now have your `mean` function, you have your tests setup and you NEED it to work even for big enough floats.
What can we do?

Here are two attempts:

```python
def mean(a, b):
    c = min(a, b)
    d = max(a, b)
    return c + (d - c) / 2
```

That one was full of promises but failed for the same reason.

Let's try a last one:

```python

```



Cheers :beer:
