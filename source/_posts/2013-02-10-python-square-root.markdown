---
layout: post
title:  "Python Square Root"
date:   2013-02-10
categories: python
---
{% codeblock lang:python NewtonRaphsonSquareRoot.py %}
#! /usr/bin/python2.7
# sqrt by Newton-Raphson
def sqrtNR(x, epsilon):
    x = float(x)
    guess = x/2.0
    diff = guess**2 - x
    count = 0
    while abs(diff) > epsilon:
        count += 1
        print "count =", count, "guess =", guess, "diff =", diff
        guess = guess - diff/(2.0*guess)
        diff = guess**2 - x
    print "number of guesses =", count
    print "guess =", guess
	# sqrt by bisection
def sqrt(x,epsilon):
    numguesses = 0
    low = 0.0
    high = x
    guess = (high + low)/2.0
    while abs(guess**2 - x) >= epsilon:
        numguesses += 1
        print "count =", numguesses, "low =", low, "high =", high, "guess =", guess
        if guess**2 < x:
            low = guess
        else:
            high = guess
        guess = (high + low)/2.0
    print "number of guesses =", numguesses
    print guess, "is close to the square root of", x
print "sqrtNR"
print sqrtNR(2,.00000001)
print
print "sqrt"
print sqrt(2,.00000001)
{% endcodeblock %}