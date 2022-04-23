---
title: 断言
teaching: 10
exercises: 0
questions:
- "我们如何比较观察值和预期值？"
objectives:
- "断言是嵌入在代码中的单行测试。"
- "如果发生意外情况，断言可以停止执行。"
- "断言是测试的基石。"
keypoints:
- "断言是嵌入在代码中的单行测试。"
- "`assert`关键字用于设置断言。"
- "如果参数为假，断言将停止执行。"
- "如果论证为真，则断言什么也不做。"
- "`numpy.testing`模块提供工具数字测试。"
- "断言是测试的基石。"
---

断言是最简单的测试类型。它们被用作在运行时限制可接受行为的工具。python中的asser 关键字具有以下行为：

~~~
>>> assert True == False
~~~
{: .python}
~~~
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  AssertionError
~~~
{: .output}
~~~
>>> assert True == True
~~~
{: .python}
~~~
~~~
{: .output}

也就是说，如果比较结果为假，断言会立即停止代码执行。如果比较是真的，它什么都不做。因此，这些是保护函数免受愚蠢（例如人类）输入的非常好的工具：

~~~
def mean(num_list):
    assert len(num_list) != 0
    return sum(num_list)/len(num_list)
~~~
{: .python}

断言的优点是易于使用。它们很少超过一行代码。缺点是断言会不加选择地停止执行，并且生成的错误消息的帮助通常非常有限。

此外，减少异常情况发生，需要对输入进行检查。当提供给mean函数的输入是字符串而不是数字列表时会发生什么？

1.打开一个[Jupyter Notebook](https://jupyter.org/)
2. 创建以下函数：

~~~
def mean(num_list):
  return sum(num_list)/len(num_list)
~~~
{: .python}

3. 在函数中，插入一个断言来检查输入是否实际上是一个列表。

> ## 提示
>
> 提示：使用[isinstance 函数](https://docs.python.org/2/library/functions.html#isinstance)。
{: .callout}

> ## 测试近似相等
>
> 断言也有助于捕捉异常行为，例如浮点运算引起的异常行为。使用assert关键字，您如何测试某个值是否与另一个值几乎相同？
>
> - 我的包mynum提供数字 a。
> - 使用`assert`关键字检查数字a是否大于2。
> - 使用`assert`关键字检查a是否等于2，误差为0.003。
{: .callout}

~~~
from mynum import a
# greater than 2 assertion here
# 0.003 assertion here
~~~
{: .python}

## NumPy

NumPy数值计算库有一个内置函数`assert_allclose`用于比较容差内的数字：

~~~
from numpy.testing import assert_allclose
from mynum import a
assert_allclose(a, 2, atol=0.003, rtol=0)
~~~
{: .python}
