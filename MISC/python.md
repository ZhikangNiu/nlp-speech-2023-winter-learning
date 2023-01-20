# Python拾遗

## 高级函数 

1. map/reduce

   1. `map()`函数接收两个参数，一个是函数，一个是`Iterable`，`map`将传入的函数依次作用到序列的每个元素，并把结果作为新的`Iterator`返回。

   2. ```python
      >>> def f(x):
      ...     return x * x
      ...
      >>> r = map(f, [1, 2, 3, 4, 5, 6, 7, 8, 9])
      >>> list(r)
      [1, 4, 9, 16, 25, 36, 49, 64, 81]
      ```

   3. `map()`传入的第一个参数是`f`，即函数对象本身。由于结果`r`是一个`Iterator`，`Iterator`是惰性序列，因此通过`list()`函数让它把整个序列都计算出来并返回一个list。

   4. 再看`reduce`的用法。`reduce`把一个函数作用在一个序列`[x1, x2, x3, ...]`上，这个函数必须接收两个参数，`reduce`把结果继续和序列的下一个元素做累积计算，其效果就是：

      ```python
      reduce(f, [x1, x2, x3, x4]) = f(f(f(x1, x2), x3), x4)
      ```

   5. 可以很快速实现序列的求和和对序列的操作

      ```python
      >>> from functools import reduce
      >>> def add(x, y):
      ...     return x + y
      ...
      >>> reduce(add, [1, 3, 5, 7, 9])
      25
      
      >>> from functools import reduce
      >>> def fn(x, y):
      ...     return x * 10 + y
      ...
      >>> reduce(fn, [1, 3, 5, 7, 9])
      13579
      ```

2. filter -> 返回的也是惰性队列

   1. `filter()`也接收一个函数和一个序列。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`True`还是`False`决定保留还是丢弃该元素。-> 快速过滤掉一些东西

   2. PyTorch里面可以这样写 -> 只优化需要梯度的函数

      ```python
      optimizer = optim.Adam(filter(lambda p: p.requires_grad, net.parameters()), lr=0.1)
      ```

3. sorted

   1. `sorted()`函数也是一个高阶函数，它还可以接收一个`key`函数来实现自定义的排序，例如按绝对值大小排序：

      ```python
      >>> sorted([36, 5, -12, 9, -21], key=abs)
      [5, 9, -12, -21, 36]
      ## 实现字符穿的排序
      >>> sorted(['bob', 'about', 'Zoo', 'Credit'], key=str.lower)
      ['about', 'bob', 'Credit', 'Zoo']
      ```

4. lambda -> 匿名函数

   1. 关键字`lambda`表示匿名函数，冒号前面的`x`表示函数参数。

      匿名函数有个限制，就是只能有一个表达式，不用写`return`，返回值就是该表达式的结果。

      用匿名函数有个好处，因为函数没有名字，不必担心函数名冲突。此外，匿名函数也是一个函数对象，也可以把匿名函数赋值给一个变量，再利用变量来调用该函数：

      ```
      >>> f = lambda x: x * x
      >>> f
      <function <lambda> at 0x101c6ef28>
      >>> f(5)
      25
      ```

      同样，也可以把匿名函数作为返回值返回，比如：

      ```
      def build(x, y):
          return lambda: x * x + y * y
      ```

## 返回函数

1. 将函数作为返回值

   1. 高阶函数除了可以接受函数作为参数外，还可以把函数作为结果值返回。

      我们来实现一个可变参数的求和。通常情况下，求和的函数是这样定义的：

      ```python
      def calc_sum(*args):
          ax = 0
          for n in args:
              ax = ax + n
          return ax
      ```

      但是，如果不需要立刻求和，而是在后面的代码中，根据需要再计算怎么办？可以不返回求和的结果，而是返回求和的函数：

      ```python
      def lazy_sum(*args):
          def sum():
              ax = 0
              for n in args:
                  ax = ax + n
              return ax
          return sum
      ```

      当我们调用`lazy_sum()`时，返回的并不是求和结果，而是求和函数：

      ```python
      >>> f = lazy_sum(1, 3, 5, 7, 9)
      >>> f
      <function lazy_sum.<locals>.sum at 0x101c6ed90>
      ```

      调用函数`f`时，才真正计算求和的结果：

      ```python
      >>> f()
      25
      ```

      在这个例子中，我们在函数`lazy_sum`中又定义了函数`sum`，并且，内部函数`sum`可以引用外部函数`lazy_sum`的参数和局部变量，当`lazy_sum`返回函数`sum`时，相关参数和变量都保存在返回的函数中，这种称为“闭包（Closure）”的程序结构拥有极大的威力。

   2. 返回闭包时牢记一点：返回函数不要引用任何循环变量，或者后续会发生变化的变量。



## 装饰器

连接：https://www.liaoxuefeng.com/wiki/1016959663602400/1017451662295584

Fairseq，mm系列都使用了装饰器机制，好好学习



## 面向对象

1. 如果要获得一个对象的所有属性和方法，可以使用`dir()`函数，它返回一个包含字符串的list，比如，获得一个str对象的所有属性和方法：

   ```python
   >>> dir('ABC')
   ['__add__', '__class__',..., '__subclasshook__', 'capitalize', 'casefold',..., 'zfill']
   ```

2. `getattr()`、`setattr()`以及`hasattr()`，我们可以直接操作一个对象的状态：

   ```python
   >>> class MyObject(object):
   ...     def __init__(self):
   ...         self.x = 9
   ...     def power(self):
   ...         return self.x * self.x
   ...
   >>> obj = MyObject()
   ```

   紧接着，可以测试该对象的属性：

   ```python
   >>> hasattr(obj, 'x') # 有属性'x'吗？
   True
   >>> obj.x
   9
   In [14]: hasattr(obj, 'power') #也可以看有没有函数
   Out[14]: True
   >>> hasattr(obj, 'y') # 有属性'y'吗？
   False
   >>> setattr(obj, 'y', 19) # 设置一个属性'y'
   >>> hasattr(obj, 'y') # 有属性'y'吗？
   True
   >>> getattr(obj, 'y') # 获取属性'y'
   19
   >>> obj.y # 获取属性'y'
   19
   ```

   如果试图获取不存在的属性，会抛出AttributeError的错误：

   ```python
   >>> getattr(obj, 'z') # 获取属性'z'
   Traceback (most recent call last):
     File "<stdin>", line 1, in <module>
   AttributeError: 'MyObject' object has no attribute 'z'
   ```

   可以传入一个default参数，如果属性不存在，就返回默认值：

   ```python
   >>> getattr(obj, 'z', 404) # 获取属性'z'，如果不存在，返回默认值404
   404
   ```

   也可以获得对象的方法：

   ```python
   >>> hasattr(obj, 'power') # 有属性'power'吗？
   True
   >>> getattr(obj, 'power') # 获取属性'power'
   <bound method MyObject.power of <__main__.MyObject object at 0x10077a6a0>>
   >>> fn = getattr(obj, 'power') # 获取属性'power'并赋值到变量fn
   >>> fn # fn指向obj.power
   <bound method MyObject.power of <__main__.MyObject object at 0x10077a6a0>>
   >>> fn() # 调用fn()与调用obj.power()是一样的
   81
   ```

## @staticmethod

关于@staticmethod,这里抛开修饰器的概念不谈，只简单谈它的作用和用法。  staticmethod用于修饰类中的方法,使其可以在不创建类实例的情况下调用方法，这样做的好处是执行效率比较高。当然，也可以像一般的方法一样用实例调用该方法。该方法一般被称为静态方法。静态方法不可以引用类中的属性或方法，其参数列表也不需要约定的默认参数self。我个人觉得，静态方法就是类对外部函数的封装，有助于优化代码结构和提高程序的可读性。当然了，被封装的方法应该尽可能的和封装它的类的功能相匹配。

```python
class Time():
    def __init__(self,sec):
        self.sec = sec
    #声明一个静态方法
    @staticmethod
    def sec_minutes(s1,s2):
        #返回两个时间差
        return abs(s1-s2)

t = Time(10)
#分别使用类名调用和使用实例调用静态方法
print(Time.sec_minutes(10,5),t.sec_minutes(t.sec,5))
#结果为5 5
```

