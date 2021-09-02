---
title: "Projecteuler_119"
author: Malcolm Smith Fraser
date: 2021-09-02T06:12:47Z
category: 
tags: ["euler","project","challenge"]
series: "biostat823"
draft: true
---

**Digit power sum** (solved by 12212 as of 9/1/2021)

The number 512 is interesting because it is equal to the sum of its digits raised to some power: 5 + 1 + 2 = 8, and 83 = 512. Another example of a number with this property is 614656 = 284.

We shall define an to be the nth term of this sequence and insist that a number must contain at least two digits to have a sum.

You are given that a2 = 512 and a10 = 614656.

Find a30

>My solution:
```python
def nth_digit_power_sum(n):
    """
    For x in (0,100) this function searches x^0 to x^20 for cases where x^n equals the sum of the digits in x.
    Returns the (input) nth term in the sequence of these such values when sorted in ascending order.
    
    **Note the loop as written only returns the first 47 such values but this can be extended as needed.
    """
    power_sums = set()
    for a in range(100):
        x = a
        for b in range(20): 
            x *= a 
            if x < 10: 
                continue 
            if sum(map(int, str(x))) == a: 
                power_sums.add(x) 

    return sorted(power_sums)[n-1]
```
```python
nth_digit_power_sum(30)
>>>248155780267521
```
>My approach:

This one was pretty tricky to think through, buit the solution I came up with was fairly simple.  

On one side we have digits we need to sum, and on the other hand we have powers to calculate. 
The most straightforward approach is to calculate the all (0-20 in this case) the powers for a given range of numbers (0-100).
As we calculate these powers, if its determined that sum the of the digits of one of these powers equals the base of that power, 
we append it to a set - which avoids any duplicates. 
Once we've finished iterating through our sample space we can sort and index for the desired value.