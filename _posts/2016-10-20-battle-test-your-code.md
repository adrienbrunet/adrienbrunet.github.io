---
layout: post
title: Battle test your code
categories: [Python, test]
tags: [code, test, python]
comments: true
description: Discover Hypothesis and beef up your tests!
---

When writing tests, we can easily think of some happy paths along with more complicated cases to try. The fact is: our tests are inevitably biaised. It only catches the corner cases we can think of. When that's not enough, give [**Hypothesis**](https://hypothesis.readthedocs.io/en/latest/) a try.

# A mean function :smiling_imp:

Let's try to explore this library with an easy example: a function that calculates the mean between two elements e.g. (x+y) / 2

Do you wonder why this silly example? Here is a bit of context.
I had a look recently at "[_Beautiful Code_](http://shop.oreilly.com/product/9780596510046.do 'O'reilly link to Beautiful Code Book')".
One of my favorite chapter, called "_Beautiful Test_" takes an example from "[_Programming Pearls_](https://www.amazon.com/Programming-Pearls-2nd-Jon-Bentley/dp/0201657880 "Amazon link to buy Progamming pearls")" about binary search.

The fact is: binary search is quite simple to explain but you have a trillion ways to implement it wrong.

With big enough arrays, you can raise exceptions with some naive implementations. **INTEGER OVERFLOW MAGIC**.

An example writen with java is given in "_Beautiful Code_":

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

If this algorithm is pretty straightforward, the fact that a bug lies on the line `int mid = (low + high) / 2;` can be surprising at first.
If you want to read more on that, you can read this chapter in "_Beautiful Code_". Solutions are provided and jUnit is introduced to overcome the Integer Overflow bug.

With this example, I realized that something as obvious as the mean between two numbers can be a tricky thing to implement correctly.

# Writing our own mean function

Java is fun and all but what happens in Python? Do we have Integer Overflow? *Spoiler Alert: No.*

```python
import sys

m = sys.maxsize
print(type(2 * m))
```
```
> <class 'int'>
```

So... I'm tempted to think that I can safely write a `mean` function like this (file `great_math.py`):

```python
def mean(a, b):
    return (a + b) // 2
```
It seems harmless, right? I hear someone saying my code is not tested, I can't tell if it works. Mmkay. Here we go. (file `test_mean.py`)

```python
import sys
from great_math import mean

def test_mean():
    assert mean(0, 0) == 0
    assert mean(1, 2) == 1
    assert mean(0, 10) == 5
    overflow_test = mean(sys.maxsize, sys.maxsize + 2)
    assert overflow_test == sys.maxsize + 1
```
And... it's OK! All green! 

This implementation feels ok for now. We are sure Integer Overflow is not a problem. Good.

# Beef up your tests!

Let's try to see if Hypothesis can find any other bug. Hypothesis allows you to test code with (a lot of) different inputs and ensure you're not missing some obvious mistakes that a few tests (like my previous `test_mean`) won't cover.

We add decorators to a simple test and Hypothesis takes care of the rest. `@given` will generate randoms values for us.

Here we go, file `test_hypothesis.py`, let's run 50000 random tests. We need to test something simpler than the real value expected. Indeed, the value are generated on the fly, I can't say in advance what the result should be. I used this really stupid assumption: the mean should be in between the two other elements.

```python
from hypothesis import given, settings
from hypothesis import strategies as st

from great_math import mean


@given(st.integers(), st.integers())
@settings(max_examples=50000)
def test_mean(a, b):
    assert min(a, b) <= mean(a, b) <= max(a, b)
```

50000 tests later, all green! Obviously, `assert min(a, b) <= mean(a, b) <= max(a, b)` is not going to raise a lot of errors so you can think this test is absolutely pointless. Bear with me a bit more. :bear:

Now that my mean function works like a charm with integers, let's try to extend it with floats. Here we go, bold move, let's change one character in our mean function.

```python
def mean(a, b):
    return (a + b) / 2
```
Note the division is done with `/` not `//`. Simple enough. Right now, I don't see why this should change anything. Muahaha, how naive am I. That's only because I'm a weak developper. Hypothesis gets my ass (and probably yours) covered...

Let's run our test again... and... it fails ! Wait, what?

```
>       assert min(a, b) <= mean(a, b) <= max(a, b)
E       assert 9007199254740993 <= 9007199254740992.0
E        +  where 9007199254740993 = min(9007199254740993, 9007199254740993)
E        +  and   9007199254740992.0 = mean(9007199254740993, 9007199254740993)
```

Step by step:

```
>>> a = 9007199254740993
>>> b = 9007199254740993
>>> a + b
18014398509481986    All good so far
>>> (a + b) / 2
9007199254740992.0   Meh...
```

Float precision... Great... And yet, I haven't even feed the mean function with anything else than integers. Just by curiosity, let's see what the test will say with floats:

```python
from hypothesis import given, settings
from hypothesis import strategies as st

from great_math import mean


@given(st.floats(), st.floats())
@settings(max_examples=50000)
def test_mean(a, b):
    assert min(a, b) <= mean(a, b) <= max(a, b)
```
That fails: `Assertion Error` with `a` or `b` being `nan`.

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
That time, the test should raise a more useful error...
Looks like `mean(-9.9792015476736e+291, -1.7976931348623157e+308)` equals `-inf`.

Yay :sparkles: Float precision again. Looks like that will be our only problem.

What can we do?

Solutions from beautiful code are irrelevant here (using a substraction instead of an addition):

```python
def mean(a, b):
    c = min(a, b)
    d = max(a, b)
    return c + (d - c) / 2 
```

We need to use `decimal` to have a better precision and we should be good.

```python
from decimal import Decimal, getcontext

def mean(a, b):
    getcontext().prec = 1000
    return (Decimal(a) + Decimal(b)) / 2
```

And..... that finally works! Under 50 000 different tests! Let's celebrate! :fireworks:

# Conclusion

Hypothesis found some cases where my mean function wasn't working as expected anymore.
You probably don't need to deal with big floats and the first implementation should do the job. My point is just to show you that even a really simple function can raise an error with the right input. Never trust your instinct. Trust your tests. And have a lot of them. That's why Hypothesis is great. It may raise errors that are irrelevant for your use case but at least, you'll be fully aware of your code limits.

If you like this introduction to Hypothesis, check their [documentation](https://hypothesis.readthedocs.io/en/latest/quickstart.html 'Hypothesis documentation'), you have a lot more than what I exposed here (for example, stateful testing is appealing).

Want more? Here is a [djangocon talk on Hypothesis](https://vimeo.com/134190370).


Cheers :beer:
