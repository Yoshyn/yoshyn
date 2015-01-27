---
layout: post
title:  "Functions Pointers by example (en)"
date:   2013-06-12 13:53:03
categories: article
tags: cpp
---

Today, a small memento on the different ways to practice Function Pointers in C++.

A function pointer is simply the memory address where a function is located. If our program calls code at this address, it will execute the related function.

And as examples are better than a long explanation, let's see some examples :

## Pointers to simple functions :

We start by creating two functions for basic mathematical operations (addition, multiplication).

These two operations have the same profile (parameters, return value).

{% highlight cpp %}
int multiply (int value1, value2 int)
{
     return value1 * value2;
}

int add (int value1, value2 int)
{
     value1 + value2 return;
}
{% endhighlight %}

The goal now is to delegate the processing of this transaction to allow a call like this :

{% highlight cpp %}
function (number_1, number_2, type_of_operation)
{% endhighlight %}

So, let's create this function :

{% highlight cpp %}
void calculate (int value1, value2 int, int (* operation) (int, int))
{
   int res = operation (value1, value2);
   cout << endl << res;
}
{% endhighlight %}

> **Some explanations :**
>
> 1. The two first parameters are integers (value1 and value2).
> 2. Third parameter is a function pointer :
>  * it takes two integers as parameters.
>  * it returns an integer.
> 3. The calculated function call the third parameter function with the 2 first parameters.

We can now call the calculation function :

{% highlight cpp %}
int main ()
{
   calculation (2,3, multiply);
   calculation (2.3, add);

   cout << "EXIT SUCCESS" << endl;
   return 0;
}
{% endhighlight %}

> **Tip:** With a typedef, you can make the writing of this code a bit more readable:
>{% highlight cpp %}
typedef int (* Operation) (int, int);
/* This allows to write the following prototype : */
void calculate (int value1, value2 int, Operation operation);
/* Instead of : */
void calculate (int value1, value2 int, int (* operation) (int, int))
{% endhighlight %}

## Pointers to functions (but not only):

**With templates :**

The objective here is to do the work at compile rather than during the execution.

The calculation function is going to take a template corresponding to an operation:

{% highlight cpp %}
template <typename operation = "">
void calculate (int value1, value2 int, OPERATION operation)
{
   int res = operation (value1, value2);
   cout << endl << res;
}
{% endhighlight %}

The advantage is that you can now pass objects such as the following :

{% highlight cpp %}
struct multiplication
{
   int operator () (int value1, value2 int)
   {
     return value1 * value2;
   }
};

int main ()
{
   multiplication multiply;

   computation (2,3 addition);
   calculation (2,3, multiply);

   cout << "EXIT SUCCESS" << endl;
   return 0;
}
{% endhighlight %}

**With std :: function (from c ++ 0x) :**

Another way is to use the function type of the stl (#include). The calculation function becomes:

{% highlight cpp %}
void calculate (int value1, value2 int, std :: function <int> operation)
// We pass in a parameter function which is called operation that returns an integer and takes two integer parameter.
{
   int res = operation (value1, value2);
   cout << endl << res;
}
{% endhighlight %}

The corresponding call is exactly the same as before :

{% highlight cpp %}
int main ()
{
   multiplication multiply;
   calculation (3.2, addition);
   calculation (3.2, multiply);
   cout << "EXIT SUCCESS" << endl;
   return 0;
}
{% endhighlight %}

But the novelty is that the lamda can also be used (or anonymous functions). For example:

{% highlight cpp %}
int main ()
{
   std :: function func = [] (int value1, value2 int) {return value1 * value2;}; // [&] If you want to return the result by reference.
   calculation (3.2, func);
   // Faster:
   calculation (3.2, [] (int value1, value2 int) {return value1 value2-;});
   cout << "EXIT SUCCESS" << endl;
   return 0;
}
{% endhighlight %}

And, it's over.

This article may contain mistakes, do not hesitate to let me know.