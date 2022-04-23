---
title: 测试驱动开发
teaching: 10
exercises: 0
questions:
- "您如何使测试成为代码编写过程的一部分？"
objectives:
- "了解测试驱动开发的优点和缺点。"
- "在写代码之前写一个测试。"
keypoints:
- "测试驱动开发是一种常见的软件开发技术"
- "通过先写测试，功能需求非常明确"
- "TDD 并不适合所有人"
---

测试驱动开发 (TDD) 完全颠覆了编写代码然后测试的工作流程。TDD是一个软件开发过程，您首先编写测试。在编写单行函数之前，首先要为该函数编写测试。

编写测试后，您可以继续编写您正在测试的函数。但是，您只应该实现足够的功能以便测试通过。如果该函数没有执行所需的操作，则编写另一个测试，然后返回并修改该函数。您重复这个测试然后实施的过程，直到该功能完全满足您当前的需求。

> ## 创意
>
> Kent Beck在他的《测试驱动开发：通过示例》一书中最强烈地提出了这种设计理念。
>
> TDD的核心主张是，在流程结束时，您有一个针对您的用例进行了良好测试的实现，并且流程本身更加高效。当您的测试通过并且您不再需要任何功能时，您就停止了。您不会花任何时间来实现功能，因为它们稍后会被证明是有用的。当你需要它时，你会得到你需要的东西。TDD 是一个非常强大的想法，尽管很难虔诚地遵循它。
{: .callout}

测试驱动开发最重要的一点是，在您开始编写代码的那一刻，您应该考虑如何测试该代码。测试应该与实现一起编写和呈现。**测试太重要了，不能事后才想到。**

> ## 自己决定是否使用TDD
>
> 实践严格TDD的开发人员会告诉你，这是自切片数组以来最好的东西。但是，做对你有用的事。是否追求经典 TDD 的选择是个人决定。
{: .callout}

以下示例说明了标准差函数“std()”的经典 TDD。

首先，我们编写一个测试来计算来自数字列表的标准差，如下所示：

~~~
from mod import std

def test_std1():
    obs = std([0.0, 2.0])
    exp = 1.0
    assert obs == exp
~~~
{: .python}

接下来，我们编写 `std()` 的 _minimal_ 版本，这将导致 `test_std1()` 通过：

~~~
def std(vals):
    # surely this is cheating...
    return 1.0
~~~
{: .python}

如您所见，最小版本仅返回我们正在测试的唯一测试用例的预期结果。如果我们只想取数字 0.0 和 2.0，或 1.0 和 3.0 等的标准差，那么这个实现将完美地工作。如果我们想进行分支，那么我们可能需要编写更健壮的代码。但是，在我们编写更多代码之前，我们首先需要添加另外一两个测试：

~~~
def test_std1():
    obs = std([0.0, 2.0])
    exp = 1.0
    assert_equal(obs, exp)

def test_std2():
    # Test the fiducial case when we pass in an empty list.
    obs = std([])
    exp = 0.0
    assert_equal(obs, exp)

def test_std3():
    # Test a real case where the answer is not one.
    obs = std([0.0, 4.0])
    exp = 2.0
    assert_equal(obs, exp)
~~~
{: .python}

使这些测试通过的简单函数实现如下：

~~~
def std(vals):
    # a little better
    if len(vals) == 0: # Special case the empty list.
        return 0.0
    return vals[-1] / 2.0 # By being clever, we can get away without doing real work.
~~~
{: .python}

我们完了吗？不，当然不。即使测试全部通过，这显然仍然不是通用的标准偏差函数。为了创建更好的实现，TDD 声明我们再次需要扩展测试套件：

~~~
def test_std1():
    obs = std([0.0, 2.0])
    exp = 1.0
    assert_equal(obs, exp)

def test_std2():
    obs = std([])
    exp = 0.0
    assert_equal(obs, exp)

def test_std3():
    obs = std([0.0, 4.0])
    exp = 2.0
    assert_equal(obs, exp)

def test_std4():
    # The first value is not zero.
    obs = std([1.0, 3.0])
    exp = 1.0
    assert_equal(obs, exp)

def test_std5():
    # Here, we have more than two values, but all of the values are the same.
    obs = std([1.0, 1.0, 1.0])
    exp = 0.0
    assert_equal(obs, exp)
~~~
{: .python}

此时，我们不妨尝试实现一个通用的标准差函数。 记起：

![标准偏差](../img/std.png)

我们将花费更多的时间来尝试对标准偏差进行巧妙的近似，而不是实际编码它。

1. 将上面的五个测试复制到一个名为test_std.py的文件中
2. 打开mod.py
3. 添加一个实际计算标准偏差的实现。
4. 运行上述测试。 他们通过了吗？

需要注意的是，我们可以通过编写进一步的测试来改进此功能。例如，这个 `std()` 忽略了无穷大是值列表元素的情况。总是有更多可以测试的。TDD 通过告诉您在完成所有用例后停止测试来防止您过火。
