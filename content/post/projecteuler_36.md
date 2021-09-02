---
title: "Project Euler: Problem 36"
author: Malcolm Smith Fraser
date: 2021-09-02T05:38:26Z
category:
tags: ["euler","project","challenge"]
series: "biostat823"
draft: true
---

**Double-base palindromes** (solved by 89539 as of 8/25/25)

The decimal number, 585 = 10010010012 (binary), is palindromic in both bases.

Find the sum of all numbers, less than one million, which are palindromic in base 10 and base 2.

(Please note that the palindromic number, in either base, may not include leading zeros.)

>Helper functions:
```python
def get_binary(x):
    """Returns the binary form of the base 10 number without leading zeroes"""

    b = bin(x).split("b")[1]
    return b
```
```python
def check_palindrome(num):
    """Checks if the base 10 and its binary are both palindromes"""

    string = str(num)
    bstring = get_binary(num)
    if string == string[::-1] and bstring == bstring[::-1]:
        return True
    else:
        return False
```
>My solution:
```python
def sum_palindromes(limit=1000000):
    """Sums all base2/binary palidromes less than the limit value"""
    ss = 0
    for x in range(limit):
        if check_palindrome(x):
            ss += x
    return ss
```
```python
sum_palindromes()
>>>872187
```
>My approach:

There are three parts to this question.

The first is generating the binary value of the given base10 number without any leading zeros. 
This is accomplished in the `get_binary` function which uses python's built in `bin` operator. 

The second involves checking if the both the base10 number and its binary are both palindromes.
This is rather trivial using basic string manipulations. See `check_palindrome`.

Lastly, to reach the final answer we need to sum all the palindromes that exist for numbers less than 1 million.
This can be accomplished with a loop through all such values. See `sum_palindromes`.