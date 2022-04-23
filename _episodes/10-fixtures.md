---
title: 测试固件（test fixtures）
teaching: 10
exercises: 0
questions:
- "如何创建和清理测试代码所需的数据？"
objectives:
- 了解测试固件如何帮助编写测试。
keypoints:
-   可能需要设置组成测试环境的“固件”。
---

上面的示例不需要太多setup或teardown。但是，请考虑以下与第三方程序通信时可能出现的示例。你有一个函数`f()`，它会将一个名为`yes.txt`的文件写入磁盘，值为42，但前提是文件`no.txt`不存在。要真正测试该功能是否有效，您需要确保在运行测试之前既不存在“yes.txt”也不存在“no.txt”。测试结束后，你想在下一次测试到来之前自己清理一下。您可以编写测试、setup和teardown函数，如下所示：

~~~
import os

from mod import f

def f_setup():
    # The f_setup() function tests ensure that neither the yes.txt nor the
    # no.txt files exist.
    files = os.listdir('.')
    if 'no.txt' in files:
        os.remove('no.txt')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def f_teardown():
    # The f_teardown() function removes the yes.txt file, if it was created.
    files = os.listdir('.')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def test_f():
    # The first action of test_f() is to make sure the file system is clean.
    f_setup()
    exp = 42
    f()
    with open('yes.txt', 'r') as fhandle:
        obs = int(fhandle.read())
    assert obs == exp
    # The last action of test_f() is to clean up after itself.
    f_teardown()
~~~
{: .python}

上述setup和teardown的实现通常很好。但是，它不保证会调用 `f_setup()` 和 `f_teardown()` 函数。这是因为在 `f()` 或 `test_f()` 的任何地方出现意外错误会导致测试在达到拆卸功能之前中止。

当必须创建 _test fixtures_ 时，需要这些setup和teardown行为。fixture是测试成功运行所需的任何环境状态或对象。

如上所述，在测试之前执行以准备fixture的函数称为 _setup_ 函数。在运行测试后执行以消除副作用的一种称为_teardown_函数。通过为我们的 setup 和 teardown 函数提供特殊名称，pytest将确保它们在我们的测试函数之前和之后运行，而不管测试函数中发生了什么。这些特殊名称是 `setup_function` 和 `teardown_function`，每个都需要一个参数：正在运行的测试函数（在这种情况下，我们不会使用参数）。

~~~
import os

from mod import f

def setup_function(func):
    # The setup_function() function tests ensure that neither the yes.txt nor the
    # no.txt files exist.
    files = os.listdir('.')
    if 'no.txt' in files:
        os.remove('no.txt')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def teardown_function(func):
    # The f_teardown() function removes the yes.txt file, if it was created.
    files = os.listdir('.')
    if 'yes.txt' in files:
        os.remove('yes.txt')

def test_f():
    exp = 42
    f()
    with open('yes.txt', 'r') as fhandle:
        obs = int(fhandle.read())
    assert obs == exp
~~~
{: .python}

setup 和 teardown 函数使我们的测试更简单，并且即使在我们的测试中发生异常，也可以保证运行 teardown 函数。 此外，设置和拆卸函数将自动为给定文件中的_every_测试调用，以便每个测试以干净状态开始和结束。（Pytest有自己简洁的 [fixture 系统](http://pytest.org/latest/fixture.html#fixture)，我们不会在这里介绍。）
