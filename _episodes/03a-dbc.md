---
title: 契约式设计
teaching: 5
exercises: 10
questions:
- "什么是契约式设计？"
objectives:
- "学习使用Python契约，PyContracts。"
- "学习定义简单和复杂的契约。"
- "了解契约的前置、后置和不变条件。"
keypoints:
- "契约式设计是一种将断言用于接口规范的方式。"
- "前置条件是您在调用函数时同意遵守的承诺。"
- "后置条件是函数同意服从返回给您的承诺。"
---

在契约式设计中，应用程序和库中的函数之间的交互是由*契约*管理的。一项功能的契约通常涉及三种不同类型的需求。

* 前提条件：在函数开始工作之前必须为真的事情。
* 后置条件：函数完成工作后保证为真的事情。
* 不变条件：保证不会随着函数工作而改变的事物。

在此处的示例中，我们使用[PyContracts](https://andreacensi.github.io/contracts/index.html#)，它使用Python decorator符号。**注意**：在PyContracts的当前实现中，仅实现了前置条件和后置条件。如果需要，可以使用普通断言来处理不变量。最后，为了简化此处的示例，假设以下导入...

~~~ {.python}
from math import sqrt, log
from contracts import contract, new_contract
~~~

为了演示契约的使用，在这里的示例中，我们实现了我们自己版本的完美平方整数平方根函数，称为“perfect_sqrt”。我们定义了一个契约，指示调用者需要传递一个大于或等于零的整数值。这是一个前置条件的例子。接下来，该函数需要返回一个大于或等于零的整数。 这是一个后置条件的例子。

~~~ {.python}
@contract(x='int,>=0',returns='int,>=0')
def perfect_sqrt(x):
    retval = sqrt(x)
    iretval = int(retval)
    return iretval if iretval == retval else retval
~~~

现在，让我们看看当我们使用这个函数来计算平方根时会发生什么。

~~~ {.output}
>>> perfect_sqrt(4)
2
>>> perfect_sqrt(81)
9
~~~

4和81的值都是整数。因此，在这些情况下，调用者已经遵守了契约的先决条件。此外，因为4和81都是完全平方，所以函数正确地返回它们的整数平方根。因此，该功能符合契约的后置条件。

现在，让我们看看当调用者通过传递一个负数来会发生什么(没有遵守契约的先决条件时)。

~~~ {.output}
>>> perfect_sqrt(-4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<decorator-gen-2>", line 2, in perfect_sqrt
  File "/Library/Python/2.7/site-packages/PyContracts/contracts/main.py", line 253, in contracts_checker
    raise e
contracts.interface.ContractNotRespected: Breach for argument 'x' to perfect_sqrt().
Condition -4 >= 0 not respected
checking: >=0       for value: Instance of <type 'int'>: -4   
checking: int,>=0   for value: Instance of <type 'int'>: -4   
Variables bound in inner context:
~~~

引发异常，指示未能遵守传递大于或等于零的值的先决条件。接下来，让我们看看当函数不能遵守契约的后置条件时会发生什么。

~~~ {.output}
>>> perfect_sqrt(83)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<decorator-gen-2>", line 2, in perfect_sqrt
  File "/Library/Python/2.7/site-packages/PyContracts/contracts/main.py", line 264, in contracts_checker
    raise e
contracts.interface.ContractNotRespected: Breach for return value of perfect_sqrt().
.
.
.
checking: Int|np_scalar_int|np_scalar,array(int)      for value: Instance of <type 'float'>: 9.1104335791443   
checking: $(Int|np_scalar_int|np_scalar,array(int))   for value: Instance of <type 'float'>: 9.1104335791443   
checking: int                                         for value: Instance of <type 'float'>: 9.1104335791443   
checking: int,>=0                                     for value: Instance of <type 'float'>: 9.1104335791443   
Variables bound in inner context:
~~~

对于83的值，虽然调用者遵守了契约的前置条件，但函数并没有返回整数值。它使后置条件失败并引发异常。

### 扩展契约

有时，定义契约的简单内置语法是不够的。在这种情况下，可以通过定义实现新契约的函数来扩展契约。例如，[数论告诉我们](http://mathworld.wolfram.com/SquareNumber.html) 所有完美正方形都以1、4、5、6或9的数字结尾，或者以偶数个零结尾。 我们可以定义一个检查这些条件的新契约。

~~~ {.python}
@new_contract
def ends_ok(x):
    ends14569 = x%10 in (1,4,5,6,9)
    ends00 = int(round((log(x,10)))) % 2 == 0
    if ends14569 or ends00:
        return True
    raise ValueError("%s doesn't end in 1,4,5,6 or 9 or even number of zeros"%x)
~~~

然后我们可以在契约规范中使用这个函数，`ends_ok`

~~~ {.python}
@contract(x='int,ends_ok,>=0',returns='int,>=0')
def perfect_sqrt2(x):
    return int(sqrt(x))
~~~

让我们看看当我们尝试对以奇数个零结尾的数字使用这个`perfect_sqrt2`函数时会发生什么。

~~~ {.output}
>>> perfect_sqrt2(49)
7
>>> perfect_sqrt2(1000)
Traceback (most recent call last):
  File "../foo.py", line 24, in <module>
    print "Perfect square root of 1000 = %d"%perfect_sqrt2(1000)
  File "<decorator-gen-3>", line 2, in perfect_sqrt2
  File "/Library/Python/2.7/site-packages/PyContracts/contracts/main.py", line 253, in contracts_checker
    raise e
contracts.interface.ContractNotRespected: Breach for argument 'x' to perfect_sqrt2().
1000 doesn't end in 1,4,5,6 or 9 or even number of zeros
checking: callable()       for value: Instance of <type 'int'>: 1000   
checking: ends_ok          for value: Instance of <type 'int'>: 1000   
checking: int,ends_ok,>=0  for value: Instance of <type 'int'>: 1000   
Variables bound in inner context:
~~~

### 性能注意事项

根据情况，检查契约的有效性相对于函数应该执行的_real_工作来说可能是昂贵的。例如，假设一个函数被设计为对一个排序好的数字列表执行二分查找。该操作的一个合理的前提条件是它提供给搜索的列表确实是排序的。如果列表很大，检查它是否正确排序比执行二进制搜索更昂贵。

换句话说，契约会对性能产生负面影响。出于这个原因，调用者最好有一种方法来禁用契约检查，以避免总是支付他们产生的任何性能成本。 在PyContracts中，任何`@contracts`语句由Python解释器处理来完成之前，这可以通过设置环境变量`DISABLE_CONTRACTS`或调用`contracts.disable_all()`。这允许开发人员在开发代码时保持检查到位，然后在他们确定他们的代码按预期工作时禁用它们。

契约在_开发_代码的过程中最有帮助。因此，在函数实现之前_为函数编写契约通常是一种好习惯。后来，当开发完成并且性能变得重要时，可以禁用契约。通过这种方式，契约的处理方式与断言非常相似。它们在开发代码时很有用，然后在开发完成后禁用。

[Learn more about Design by Contract in Python](https://andreacensi.github.io/contracts/index.html#)
