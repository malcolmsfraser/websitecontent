---
title: "Project Euler Problem 9"
author: Malcolm Smith Fraser
date: 2021-09-02T05:09:31Z
draft: true
---

**Special Pythagorean triplet** (solved by 360,641 as of 8/25/2021)

A Pythagorean triplet is a set of three natural numbers, a < b < c, for which,

$a^2 + b^2 = c^2$
For example, $3^2 + 4^2 = 9 + 16 = 25 = 5^2$.

There exists exactly one Pythagorean triplet for which a + b + c = 1000.
Find the product abc.

>My solution:
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

            c = math.sqrt(a * a + b * b)

            if c % 1 == 0: # check if c is an integer.. ie, is this a pythagorean triplet
                if int(a+b+c) == value: # check if special triplet
                    print(f"Special Triplet found! {(a,b,int(c))}")
                    found = True
                    break
    else:
        print(f"There is no pythagorean triplet that sums to {value}.")
```
>My approach:
The first part of this question is to come up with a way to generate pythagorean triplets. 
I accomplished this my looping through all integers between 0 and 1000 for both `a` and `b`.
`c` was then calculated following the given formula. To ensure a pythagorean triplet, for which all values are integers,
I check to to make sure `c` is evenly divisible by 1.

I then used this method for continuously generate new pythagorean triplets until the one that sums to the given value (in this case 1000) is found.
