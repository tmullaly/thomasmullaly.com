---
layout: post
title: "Simple Recursion Example in Python"
date: 2014-08-01 09:20:01 -0400
comments: true
categories: python
---
I generally use loops when solving problems that do the same thing over and over again.  I's starting to use recursion when when a small part of the problem resembles the whole problem.  I think I should think of using recursion more often.
{% codeblock lang:python AskRecursively.py %}
def ask_recursively(question):
    reply = input(question)
    reply = reply.strip().lower()

    if reply == 'yes':
        return True
    elif reply == 'no':
        return False
    else:
        print('\nPlease answer "yes" or "no". \n')
        ask_recursively(question)

def main():
    ask_recursively("Do you understand recursion? \n")

if __name__ == '__main__':
    main()
{% endcodeblock %}
Here's my attempt at solving the fibonacci sequence using recursion...
<!-- more -->
{% codeblock lang:python Fibonacci.py %}
def fibonacci(n):
    if n == 0:
        return n
    elif n == 1:
        return n
    else:
        return fibonacci(n - 1) + fibonacci(n - 2)

def main():
    for x in range(0, 15):
        print(fibonacci(x))

if __name__ == '__main__':
    main()
{% endcodeblock %}
{% highlight bash %}
0
1
1
2
3
5
8
13
21
34
55
89
144
233
377
{% endhighlight %}

Here's a simple example of a recursive factorial method...
{% codeblock lang:python Factorial.py %}
def factorial(n):
    if n <= 1:
        return 1
    else:
        return n * factorial(n -1)

def main():
    for x in range(0,15):
        print(factorial(x))

if __name__ == '__main__':
    main()
{% endcodeblock %}
{% highlight bash %}
1
1
2
6
24
120
720
5040
40320
362880
3628800
39916800
479001600
6227020800
87178291200
{% endhighlight %}