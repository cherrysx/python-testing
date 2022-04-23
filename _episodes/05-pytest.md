---
title: 使用pytest运行测试
teaching: 10
exercises: 0
questions:
- "如何自动化我的测试？"
objectives:
- "了解如何使用pytest框架运行测试套件"
- "了解如何读取pytest测试套件的输出"
keypoints:
- "`pytest`命令收集并运行以`Test`或`test_`开头的测试。"
- "`.` 表示测试通过"
- "`F` 表示测试失败或错误"
- "`x` 是一个已知的失败"
- "`s` 是故意跳过的测试"
---

我们为平均函数创建了一套测试，但一次运行一个测试很烦人。如果有某种方法可以一次运行它们会好很多，只需报告哪些测试失败和哪些成功。

值得庆幸的是，这是存在的。回想一下我们的测试：

~~~
from mean import *

def test_ints():
    num_list = [1,2,3,4,5]
    obs = mean(num_list)
    exp = 3
    assert obs == exp

def test_zero():
    num_list=[0,2,4,6]
    obs = mean(num_list)
    exp = 3
    assert obs == exp

def test_double():
    # This one will fail in Python 2
    num_list=[1,2,3,4]
    obs = mean(num_list)
    exp = 2.5
    assert obs == exp

def test_long():
    big = 100000000
    obs = mean(range(1,big))
    exp = big/2.0
    assert obs == exp

def test_complex():
    # given that complex numbers are an unordered field
    # the arithmetic mean of complex numbers is meaningless
    num_list = [2 + 3j, 3 + 4j, -32 - 2j]
    obs = mean(num_list)
    exp = NotImplemented
    assert obs == exp
~~~
{: .python}

一旦将这些测试写入名为`test_mean.py`的文件中，就可以在终端或命令行上从包含测试的目录运行命令`pytest`（请注意，对于旧版本的`pytest`包，您必须使用`py.test`）：

~~~
$ pytest
~~~
{: .bash}
~~~
collected 5 items

test_mean.py ....F

================================== FAILURES ===================================
________________________________ test_complex _________________________________

    def test_complex():
        # given that complex numbers are an unordered field
        # the arithmetic mean of complex numbers is meaningless
        num_list = [2 + 3j, 3 + 4j, -32 - 2j]
        obs = mean(num_list)
        exp = NotImplemented
>       assert obs == exp
E       assert (-9+1.6666666666666667j) == NotImplemented

test_mean.py:34: AssertionError
===================== 1 failed, 4 passed in 2.71 seconds ======================
~~~
{: .output}

在上述情况下，pytest包“嗅出”目录中的测试并将它们一起运行以生成与正则表达式“[Tt]est[-_]*”匹配的文件和函数总和的报告。

测试框架提供的主要好处正是，它是一个自动查找和运行测试的实用程序。对于pytest，这是一个名为`pytest`的命令行工具。当`pytest`运行时，它将搜索调用它的下面的所有目录，在这些目录中查找名称以`test`开头或结尾的所有Python文件，导入它们，并运行以“test”或“Test”开头的函数和类。这种测试代码的自动注册节省了大量的人力时间，并使我们能够专注于重要的事情：编写更多的测试。

当您运行`pytest`时，它会在屏幕上为每个通过的测试打印一个点 (`.`)，为每个失败的测试或出现意外错误的地方打印一个`F`。在极少数情况下，您可能还会看到一个`s`表示跳过的测试（因为该测试不适用于您的系统）或一个`x`表示已知故障（因为开发人员无法及时修复它）。在点之后，pytest将打印摘要信息。

在不更改测试的情况下，更改上一节中的mean.py文件，直到它通过。当它通过时，`pytest`将产生如下结果：

~~~
$ pytest
~~~
{: .bash}

~~~
collected 5 items

test_mean.py .....

========================== 5 passed in 2.68 seconds ===========================
~~~
{: .output}

> ## 显示执行了哪些测试
>
> 使用`pytest -v`将导致`pytest`列出执行了哪些测试以及它们是否通过：
> ~~~
> $ pytest -v
> ~~~
> {: .bash}
>
> ~~~
> collected 5 items
>
> test_mean.py .....
>
> test_mean.py::test_ints PASSED
> test_mean.py::test_zero PASSED
> test_mean.py::test_double PASSED
> test_mean.py::test_long PASSED
> test_mean.py::test_complex PASSED
>
> ========================== 5 passed in 2.57 seconds ===========================
> ~~~
> {: .output}
>
{: .callout}

随着我们编写更多代码，我们会编写更多测试，而pytest会产生更多点。每次通过测试都是对编写高质量科学软件的小而令人满意的奖励。现在您知道如何编写测试，让我们来看看可能出错的地方。
