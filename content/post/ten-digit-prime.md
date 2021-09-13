---
title: "Ten Digit Prime"
author: Malcolm Smith Fraser
date: 2021-09-12T23:40:47Z
tags:
series: "biostat823"
draft: true
---

**Finding the first 10 digit prime in the decimal expansion of 17pi**

>Helper functions:
```python
from mpmath import mpmath

def get_pi(n):
    """Returns the first n digits of pi"""
    mp.dps = n
    return mp.pi
```
```python
def check_prime(n):    
    """Checks if n is prime by searching range(2,sqrt(n)) for factors"""
    prime_flag = 0

    if n > 1:
        for i in range(2, int(n**.5) + 1):
            if (n % i == 0):
                prime_flag = 1
                break
        if (prime_flag == 0):
            return True
        else:
            return False
    else:
        return False
```
>My solution:
```python
def check_expansion(expansion, scale=1, prec=50):
    """Check if 10 digit values in the decimal expansion of 17pi are prime"""
    expan = str(scale*expansion(prec)).split(".")[1]
    length = len(expan)
    for i in range(length-10):
        window = expan[i:i+10]
        if check_prime(int(window)):
            print(f"{window} is the first 10 digit prime in the decimal expansion of {scale}*{expansion.__name__.split('_')[1]}")
            return window
            break
    else:
        print("10 digit prime not found, increase the precision")
```
```python
check_expansion()
>>>8649375157 is the first 10 digit prime in the decimal expansion of 17*pi
```

>My approach:

There are three parts to answering this question.  
1) Write a function that generates a decimal expansion of 17pi to a given precision.  
2) Write a function that checks if a given value is prime.  
3) Write a function that applies the prime checker to a sliding 10 digit window of the prime expansion.  

To assist in getting the prime expansion of 17pi used the mpmath which allows you to specify the desired precision of a decimal.  
What the get_pi function return is an mpmath object which must be converted to a string.  

The check if a given value (n) is prime, I wrote a function that checks all values between 2 and the square root of n.  
If any of these numbers is found to be a factor of n, then n is not prime.

Creating the sliding window function is rather trivial using slicing since the decimal expansion of 17pi is already in string form.  
The function converts a given 10 digit value to an integer and applies the prime checker.

>Unit tests:
```python
def test_get_pi():
    assert mp.pi == get_pi(10)
    assert mp.pi == get_pi(100)
    print("Test passed!")

test_get_pi()
>>>Test passed!
```
```python
def test_check_prime():
    assert check_prime(2) == True
    assert check_prime(7) == True
    assert check_prime(4) == False
    assert check_prime(25) == False
    print("Test passed!")

test_check_prime()
>>>Test passed!
```python
def get_e(n):
    """Returns returns the first n digits of e"""
    mp.dps = n
    return mp.e
    
def test_check_expansion():
    e_prime_10 = 7427466391
    
    assert int(check_expansion(expansion = get_e,prec=500)) == e_prime_10
    print("Test passed!")

test_check_expansion()
>>>7427466391 is the first 10 digit prime in the decimal expansion of 1*e
>>>Test passed!
```
