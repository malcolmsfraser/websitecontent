---
title: "Project Euler Problem 9"
author: Malcolm Smith Fraser
date: 2021-09-02T05:09:31Z
draft: true
---

**Problem 9 - Special Pythagorean triplet** (solved by 360,641 as od 8/25/2021)

A Pythagorean triplet is a set of three natural numbers, a < b < c, for which,

$a^2 + b^2 = c^2$
For example, $3^2 + 4^2 = 9 + 16 = 25 = 5^2$.

There exists exactly one Pythagorean triplet for which a + b + c = 1000.
Find the product abc.

```python
import math

def find_special(value=1000):
    """
    Finds the first pythagorean triplet who's sums add to the input value. Default value is 1000 for special triplet
    """
    ss = 0
    found = False

    for a in range(1, value): # a can never be as large as "value" so this is where I cap the loop for convinience
        if found == True:
            break

        for b in range(1, value): # b can never be as large as "value" so this is where I cap the loop for convinience
            if found == True:
                break

            c = math.sqrt(a * a + b * b)

            if c % 1 == 0: # check if c is an integer.. ie, is this a pythagorean triplet
                if int(a+b+c) == value: # check if special triplet
                    print(f"Special Triplet found! {(a,b,int(c))}")
                    found = True
                    break
    else:
        print(f"There is no pythagorean triplet that sums to {value}.")
```

