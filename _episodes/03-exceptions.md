---
title: 异常
teaching: 10
exercises: 0
questions:
- "代码运行时如何处理异常行为？"
objectives:
- "了解异常实际上是专门的运行时测试"
- "了解何时使用异常以及可用的异常"
keypoints:
- "异常实际上是专门的运行时测试"
- "可以使用 try-except 块捕获和处理异常"
- "许多内置的异常类型可用"
---

异常比断言更复杂。它们是大多数现代编程语言中的标准错误消息系统。从根本上说，当遇到错误时，会“抛出”或“引发”信息异常。

例如，可以使用异常来代替前面案例中的断言。

~~~
def mean(num_list):
    if len(num_list) == 0:
      raise Exception("The algebraic mean of an empty list is undefined. "
                      "Please provide a list of numbers")
    else:
      return sum(num_list)/len(num_list)
~~~
{: .python}

一旦引发异常，它将在程序范围内向上传递。异常用于触发其他错误消息或替代行为。可以使用 try-except 块在上游“捕获”异常，而不是立即停止代码执行。 当包装在try-except块中时，可以在异常到达全局范围并停止执行之前拦截异常。

要在消息传递到上游之前添加信息或替换消息，可以使用try-catch块来捕获并重新引发异常：

~~~
def mean(num_list):
    try:
        return sum(num_list)/len(num_list)
    except ZeroDivisionError as detail :
        msg = "The algebraic mean of an empty list is undefined. Please provide a list of numbers."
        raise ZeroDivisionError(detail.__str__() + "\n" +  msg)
~~~
{: .python}

或者，可以简单地智能处理异常。如果首选替代行为，则可以忽略异常，并且可以像这样实现响应行为：

~~~
def mean(num_list):
    try:
        return sum(num_list)/len(num_list)
    except ZeroDivisionError :
        return 0
~~~
{: .python}

如果单个函数可能引发不止一种类型的异常，则可以分别捕获和处理每个异常。

~~~
def mean(num_list):
    try:
        return sum(num_list)/len(num_list)
    except ZeroDivisionError :
        return 0
    except TypeError as detail :
        msg = "The algebraic mean of an non-numerical list is undefined.\
               Please provide a list of numbers."
        raise TypeError(detail.__str__() + "\n" +  msg)
~~~
{: .python}

> ## 还有什么问题？
>
> 1. 想想try块可能引发的其他类型的异常。
> 2. 通过添加一个except子句来防范它。
> 3. 以三种不同的方式使用均值函数，以便您引发每种异常情况。
{: .callout}

异常的优点是易于使用并且对用户有很大帮助。但是，并非所有行为都可以或应该在运行时异常中找到。大多数行为都应该通过单元测试进行验证。
