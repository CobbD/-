<nav id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline1">1. 什么是TDD</a></li>
<li><a href="#orgheadline2">2. 基本的使用</a></li>
<li><a href="#orgheadline3">3. 更详细的函数清单</a></li>
<li><a href="#orgheadline4">4. 参考资料</a></li>
</ul>
</div>
</nav>


# 什么是TDD<a id="orgheadline1"></a>

在编程中有一个概念TDD，其和具体的编程语言无关，其全称叫做test-driven development。也就是人们常说的测试驱动型开发。

python语言有一个内置模块用来支持测试式开发，名字叫做 **unittest** 。

# 基本的使用<a id="orgheadline2"></a>

基本的使用就简单用下面这个例子来说明了：

```python
import unittest
import math

class TooBigError(Exception):
    pass

def hello(n):
    if n&gt;2:
        raise TooBigError('too big input error')
    else:
        print('hello'*n)

class FirstTest(unittest.TestCase):
    def setUp(self):
        '''setUp函数在每个测试单元执行前被执行，其通常用于预先配置
        一些后面测试单元会用到的参数'''
        pass

    def tearDown(self):
        '''tearDown函数在每个测试单元执行之后再执行。'''
        pass

    def test_bool(self):
        '''具体的测试单元，名字需要以test字符开始'''
        self.assertTrue(True)
        self.assertFalse(False)
    def test_equal(self):
        self.assertEqual(1,1)
        self.assertNotEqual(1,2)
        self.assertAlmostEqual(math.pi, 3.1416,4)
        self.assertNotAlmostEqual(math.pi, 3.1415,4)

    def test_raises(self):
        self.assertRaises(TooBigError, hello, 3)


if __name__ == '__main__':
    unittest.main()
```

1.  unittest模块的main函数具体实际执行各个测试单元类，这些测试单元类继承自unittest的TestCase类。在这些继承自TestCase的类中，setUp函数和tearDown函数有特殊的用途，具体见上面代码的说明。然后里面定义的函数test字符串开头的都是所谓的测试单元，其将被逐个执行。

1.  TestCase有很多方法，比如assertTrue，assertFalse用于断言某个bool值是真或假，然后assertEqual用来断言某两个值是相等的(==)，类似的还有assertNotEqual用来断言两个值不相等。这里值得一提的assertAlmostEqual方法是用来断言某两个float值在多少小数位上是大致相等的，比如上面的例子， \(\pi\) 值具体到小数点4位是3.1416。上面这些方法后面还可以额外接受一个提示字符串参数（Msg参数），用来具体没有断言成功的时的补充信息。

2.  assertRaises方法用来断言某个函数在接受某些参数之后必然返回某个异常。该方法第一个参数是期望捕捉到的异常，第二个参数是具体调用的函数，后面的参数将送给这个函数，所以就不能跟之前谈及的Msg参数了。

# 更详细的函数清单<a id="orgheadline3"></a>

下面列出由 `TestCase` 类提供的一些用于判断的函数:

-   assertEqual(a, b) check for a == b
-   

# 参考资料<a id="orgheadline4"></a>

1.  Dive Into Python 3 － unit testing 一章。
2.  Testing Python  ，作者：DAVID SALE ，2014年