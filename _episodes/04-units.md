---
title: 单元测试
teaching: 10
exercises: 0
questions:
- "什么是代码单位？"
objectives:
- "了解函数是软件的原子单元。"
- "了解简单的单元比复杂的单元更容易测试。"
- "了解如何编写单个单元测试。"
- "了解如何运行单个单元测试。"
- "了解测试test fixture如何帮助编写测试。"
keypoints:
- "函数是软件的原子单元。"
- "简单的单元比复杂的单元更容易测试。"
- "单个单元测试是一个包含断言的函数。"
- "这样的单元测试就像任何其他功能一样运行。"
- "一次运行一个测试是相当乏味的，所以我们将使用一个框架来代替。"
---

之所以称为单元测试，是因为它们通过询问单个函数和方法来执行代码的功能。函数和方法通常可以被认为是软件的原子单元，因为它们是不可分割的。然而，什么被认为是最小的代码单元是主观的。函数的主体可以长也可以短，而较短的函数可以说比长函数更有利于单元测试。

因此，合理构成代码单元的内容通常因项目和语言而异。一个好的指导原则是，如果代码不能在逻辑上变得更简单（您不能拆分加法运算符）或实际上（函数是自包含且定义明确的），那么它就是一个单元。

> ## 函数就像段落
>
> 回想一下，人类一次只能在我们的脑海中持有几个想法。例如，书中的段落在几行之后开始变得冗余。通常，函数不应长于段落。
> 《清洁代码》的作者 Robert C. Martin说：“函数的第一条规则是：它们应该小。函数的第二条规则是：它们应该更小。”
{: .callout}

对代码进行单元测试的愿望通常具有鼓励代码和测试尽可能小、定义良好和模块化的效果。在Python中，单元测试通常采用测试函数的形式，这些函数调用并断言代码库中的方法和函数。为了运行这些测试功能，通常需要一个测试框架将它们收集在一起。现在，我们将为mean函数编写一些测试，并简单地单独运行它们以查看它们是否失败。在下一个会话中，我们将使用一个测试框架来收集和运行它们。

## 单元测试只是函数

单元测试通常由三部分组成，一些设置前处理操作、一些断言和一些后处理操作。设置可以像初始化输入值一样简单，也可以像创建和初始化类的具体实例一样复杂。最终，测试发生在做出断言时，比较观察值和预期值。例如，让我们测试我们的均值函数是否成功计算了一个简单列表的已知值。

在运行下一个代码之前，将您的`mean`函数保存到工作目录中名为mean.py的文件中。

您可以使用此代码保存到文件：

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

现在，回到Jupyter Notebook中运行以下代码：

~~~
from mean import *

def test_ints():
    num_list = [1, 2, 3, 4, 5]
    obs = mean(num_list)
    exp = 3
    assert obs == exp
~~~
{: .python}

上面的测试：
- 设置输入参数（简单列表 [1, 2, 3, 4, 5]）；
- 收集观察结果；
- 声明预期结果（用我们的人脑计算）；
- 并将两者与断言进行比较。

一个单元测试套件由许多测试组成，就像这个一样。可以以多种方式测试单个实现的功能。

在名为`test_mean.py`的文件中，实现以下代码：

~~~
from mean import *

def test_ints():
    num_list = [1, 2, 3, 4, 5]
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

使用Jupyter Notebook导入`test_mean`包并像这样运行每个测试：

~~~
from test_mean import *

test_ints()
test_zero()
test_double()
test_long()
test_complex()  ## 请注意，这可能会失败。您将收到一条错误消息，显示哪些测试失败
~~~
{: .python}
