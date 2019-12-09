### 1. python概述

Python是一种解释型语言。
>  这就是说，与C语言和C的衍生语言不同，Python代码在运行之前不需要编译。
  其他解释型语言还包括PHP和Ruby。
  
Python是动态类型语言。

> 指的是你在声明变量时，不需要说明变量的类型。
  你可以直接编写类似x=111和x="I'm a string"这样的代码，程序不会报错。

Python非常适合面向对象的编程（OOP）

> 因为它支持通过组合（composition）与继承（inheritance）的方式定义类（class）。Python中没有访问说明符（access  specifier，类似C++中的public和private）。

在Python语言中，函数是第一类对象（first-class objects）

> 这指的是它们可以被指定给变量，函数既能返回函数类型，也可以接受函数作为输入。类（class）也是第一类对象。

Python代码编写快，但是运行速度比编译语言通常要慢

> 好在Python允许加入基于C语言编写的扩展，因此我们能够优化代码，消除瓶颈，这点通常是可以实现的。numpy就是一个很好地例子，它的运行速度真的非常快， 因为很多算术运算其实并不是通过Python实现的。

Python用途非常广泛

> 网络应用，自动化，科学建模，大数据应用，等等。它也常被用作“胶水语言”，帮助其他语言和组件改善运行状况。Python让困难的事情变得容易，因此程序员可以专注于算法和数据结构的设计，不用处理底层的细节。

### 2. Python装饰器模式

装饰器是一个很著名的设计模式，经常被用于有切面需求的场景，较为经典的有插入日志、性能测试、事务处理、缓存、权限校验等。装饰器是解决这类问题的绝佳设计，有了装饰器，就可以抽离出大量函数中与函数功能本身无关的雷同代码并继续重用。 概括的讲，装饰器的作用就是为已经存在的对象添加额外的功能。

**简单添加日志**
```
import logging

def use_logging(func):
    def wrapper():
         logging.warn("%s is running" % func.__name__)
         return func()
    return wrapper

@use_logging
def foo():
    print("i am foo")

foo()
```

**根据level添加日志**
```
def use_logging(level):
    def decorator(func):
        def wrapper(*args, **kwargs):
            if level == 'warn':
                logging.warn("%s is running" % func.__name__)
            elif level == 'info':
                logging.info("%s is running" % func.__name__)
            return func(*args)
        return wrapper
    return decorator

@use_logging(level='warn')
def foo(name='foo'):
    print("i am %s" % name)

foo()
```

**通用装饰函数**

```
import logging

def logged(func):
    def with_logging(*args, **kwargs):
        print(func.__name__)
        print(func.__doc__)
        return func(*args, **kwargs)
    return with_logging

@logged
def f(x):
    """ do something  """
    return x + x * x

logged(f)
```
**装饰类**

```
class Foo():
    def __init__(self, func):
        self._func = func

    def __call__(self):
        print("class decorator is running")
        self._func()
        print("class decorator ending")

@Foo
def bar():
    print("bar")

bar()
```

### 3. Python单例模式

单例模式是一种常用的软件设计模式。该模式的主要目的是确保某一个类只有一个实例存在，当你希望在整个系统中，某一个类只能出现一个实例的时候，单例对象就能派上用场。

**使用模块实现**

Python的模块就是天然的单例模式，因为模块在第一次导入时，会生成pyc文件，当第二次导入的时候，会直接加载pyc文件，
而不会再执行模块代码。我们只需要把相关的函数和数据定义在一个模块中，就可以获得一个单例对象了。


```
# mysingleton.py
class MySingleton():
    def foo(self):
        pass

my_sigleton = MySingleton()


# 在其他模块中调用
# from mysingleton import my_sigleton
# my_sigleton.foo()
```

**使用__new__实现单例模式**


```
class Singleton():
    _instance = None

    def __new__(cls, *args, **kwargs):
        if not cls._instance:
            cls._instance = super(Singleton, cls).__new__(cls, *args, **kwargs)
        return cls._instance

class MyClass(Singleton):
    a = 1
    
```

**使用装饰器**

```
from functools import wraps

def singleton(cls):
    instances = {}
    @wraps(cls)
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    return get_instance

@singleton
class MyClass():
    a = 1
```

**使用元类metaclass实现**

```
"""
元类可以控制类的创建过程，它主要做三件事情：
1. 拦截类的创建
2. 修改类的定义
3. 返回修改后的类
"""

class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]

# Python2
class MyClass(object):
    __metaclass__ = Singleton

# python3
class MyClass(metaclass=Singleton):
    pass
```

### 4.Python装饰器函数声明即刻执行


```
registery = []

def register(func):
    print('running register(%s))' % func)
    registery.append(func)
    return func

@register
def f1():
    print('running f1()')


@register
def f2():
    print('running f2()')


def f3():
    print('running f3()')

def main():
    print('running main()')
    print('registery - >', registery)
    f1()
    f2()
    f3()

if __name__ == '__main__':
    main()
```

参考：https://foofish.net/python-decorator.html

参考：https://foofish.net/decorator.html

参考：https://foofish.net/understand-decorator.html

参考：https://www.jianshu.com/p/a58d6f71b1ce

### 5. Python2和Python3区别



1. print在python3中是function，在python2中是statement
2. python3中使用range返回生成器，python2中用xrange
3. python3中默认字符串类型Unicode，python2中默认字符串类型asii
4. 声明元类的方式不同
```
# python 3
class NewClass(metaclass=Metaclass):
    pass

# python 2
class NewClass():
    __metaclass__ = MetaClass
```

5.python3中只有int类型，python2中区分int和long类型，最大值不超过sys.maxint

6.python3中增加nonlocal，解决嵌套函数使用外部函数变量问题

7.python3中两个整数使用/相除，结构浮点数；python2的/结果整数

8.exception 添加as关键字

```
# python 2 
try:
    ...
except Exception, e:
    ...
    
# python 3

try:
    ...
except ZeroDivisionError:
    ...
except (TypeError, ValueError) as e:
    ...
```

参考：https://docs.python.org/3.1/whatsnew/3.0.html


### 6.python字符串单词首字母大写

把字符串s="hello world"的单词首字母大写。

**复杂方法**

```
s = "hello world"
arr = s.split(" ")
s = arr[0].capitalize() + ' ' + arr[1].capitalize()
```

**使用title**
```
print("hello world".title())
```
### 7. python中global、nonlocal用法

python引用变量的顺序： 当前作用域局部变量->外层作用域变量->当前模块中的全局变量->python内置变量 。

**7.1 global关键字用来在函数或其他局部作用域中使用全局变量。但是如果不修改全局变量也可以不使用global关键字**

```
gcnt = 0

def global_test():
    gcnt += 1
    print(gcnt)

global_test()

# 报错，第一行定义了一个全局变量，（可以省略global关键字）。
# 在global_test函数中程序会因为“如果内部函数有引用外部函数的同名变量或者全局变量,并且对这个变量有修改.那么python会认为它是一个局部变量”，又因为函数中没有gcnt的定义和赋值，所以报错
```

**7.2 声明全局变量，如果要在局部要对全局变量修改，需要在局部先声明全局变量**

```
gcnt = 0

def global_test():
    global gcnt
    gcnt += 1
    print(gcnt)
    
global_test()
# >>> 1
```
**7.3 在局部如果不声明全局变量，并且不修改全局变量，则可以正常使用全局变量**

```
gcnt = 0

def global_test():
    print(gcnt)
    
global_test()

# >> 0
```

**7.4 nonlocal关键字用来在函数或其他作用域中使用外层（非全局）变量**

```
def make_counter():
    count = 0
    def counter():
        nonlocal count
        count += 1
        return count
    return counter

def make_counter_test():
    mc = make_counter()
    print(mc())
    print(mc())
    print(mc())

make_counter_test()

# >>> 1
# >>> 2
# >>> 3
```
参考：https://stackoverflow.com/questions/1261875/python-nonlocal-statement

### 8.Python的生成器(generator)

#### 8.1 实现generator有两种方式

python中的generator保存的是算法，真正需要计算出值的时候才会去往下计算出值。它是一种惰性计算（lazy evaluation）。

要创建一个generator有两种方式。

第一种方法：把一个列表生成式的[]改成()，就创建了一个generator：

```
L = [x * x for x in range(10)]
print(L) 
# >>> [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
g = (x * x for x in range(10))   # 注意把[]改成()后，不是生成一个tuple，而是生成一个generator
print(g) 
# >>> <generator object <genexpr> at 0x1022ef630>
```
第二种方式：在函数中使用yield关键字，函数就变成了一个generator。

函数里有了yield后，执行到yield就会停住，当需要再往下算时才会再往下算。所以生成器函数即使是有无限循环也没关系，它需要算到多少就会算多少，不需要就不往下算。

```
def fib():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

f = fib()
print f, next(f), next(f), next(f)
# >>> <generator object fib at 0x7f89769d1fa0> 0 1 1
```

如上例，第一次输出f，它就是一个generator，之后每次next，它就执行到yield a。

当然其实平常很少用到next()，我们直接用for循环就可以遍历一个generator，其实for循环的内部实现就是不停调用next()。

生成器可以避免不必要的计算，带来性能上的提升；而且会节约空间，可以实现无限循环（无穷大的）的数据结构。


#### 8.2 可迭代对象(Iterable)和迭代器(Iterator)的概念

可以直接作用于for循环的对象统称为可迭代对象：Iterable。

包括集合数据类型（list、tuple、dict、set、str等）和生成器（generator）。

可以使用isinstance()判断一个对象是否是Iterable对象。

```
from collections import Iterable
isinstance([], Iterable)
# >>> True
isinstance({}, Iterable)
# >>> True
isinstance('abc', Iterable)
# >>> True
isinstance((x for x in range(10)), Iterable)
# >>> True
isinstance(100, Iterable)
# >>> False
```

迭代器：Iterator

它表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算。Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

生成器（generator）都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。

把list、dict、str等Iterable变成Iterator可以使用iter()函数：
```
isinstance(iter([]), Iterator)
# >>> True
isinstance(iter('abc'), Iterator)
# >>> True
```
Python的for循环本质上就是通过不断调用next()函数实现的，例如：
```
for x in [1, 2, 3, 4, 5]:
    pass
```
实际上完全等价于：

```
# 首先获得Iterator对象:
it = iter([1, 2, 3, 4, 5])
# 循环:
while True:
    try:
        # 获得下一个值:
        x = next(it)
    except StopIteration:
        # 遇到StopIteration就退出循环
        break
```

#### 8.3 itertools模块

python的内置模块itertools提供了用于操作迭代对象的函数，非常方便实用。举一个例子：
```
islice(iterable, [start, ] stop [, step]):
```
创建一个迭代器，生成项的方式类似于切片返回值： iterable[start : stop : step]，将跳过前start个项，迭代在stop所指定的位置停止，step指定用于跳过项的步幅。与切片不同，负值不会用于任何start，stop和step，如果省略了start，迭代将从0开始，如果省略了step，步幅将采用1.

```
from itertools import islice

def fib():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

f = fib()
print(list(islice(f, 10)))
# >>> [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

参考：https://www.cnblogs.com/zywscq/p/5774567.html

参考：https://wiki.python.org/moin/Generators

### 9.python切片超出下标值时，怎么办？

```
a = [1, 2, 3, 4]
a[99] # 报错 IndexError: list index out of range
a[99:] # 不报错，返回空数组
# >>> []
```

### 10.python中的协程

**协程的概念**

协程，又称为微线程，英文名Coroutine。协程的作用是：在执行函数A时，可以随时中断，去执行函数B，然后中断继续执行函数A（可以自由切换）。但这一过程并不是函数调用（没有调用语句）。这一整个过程看似像多线程，但实际上只有一个线程执行。

**协程的优势**

执行效率高，因为程序切换不是线程切换。由程序自身控制，没有切换线程的开销。所以与多线程相比，线程的数量越多，协程的性能优势越明显。
不需要多线程的锁机制，因为只有一个线程，也不存在同时写变量冲突。在控制共享资源时也不需要加锁。因此执0行效率可以高很多。
无需线程上下文切换的开销。
无需原子操作和锁定及同步的开销。（原子操作是指不会被线程调度机制打断的操作，这种操作一旦开始，就一直运行到结束）
方便切换控制流，简化编程模型。
高并发、高扩展性、低成本：一个cpu支持上万多协程都行，适合于进行高并发处理。
PS：协程可以处理IO密集型程序的效率问题，但是处理CPU密集型任务不是它的长处，如果充分发挥CPU的性能可以结合多进程和协程。

**协程的缺点**

无法利用多核资源
协程的本质是单个线程，它不能同时将单个cpu的多个核用上。
协程需要和进程配合才能运行在多个cpu上。
进行阻塞（Blocking）操作（如IO）时会阻塞掉整个程序。



https://thief.one/2018/06/21/1/index.html

https://www.cnblogs.com/bradleon/p/6106595.html

http://www.dabeaz.com/coroutines/Coroutines.pdf

https://www.jianshu.com/p/84df78d3225a

https://book.pythontips.com/en/latest/index.html

https://stackabuse.com/coroutines-in-python/

https://docs.python.org/3/library/asyncio-task.html#generator-based-coroutines

https://www.jianshu.com/p/839a91dd49e4

https://stackabuse.com/coroutines-in-python/


### 11.Python单元测试


https://huilansame.github.io/huilansame.github.io/archivers/python-unittest

https://docs.python-guide.org/writing/tests/

### 12.Python导入上级包

https://www.cnblogs.com/lipijin/p/3848511.html


### 13.Python3中使用cmp函数进行比较

https://www.polarxiong.com/archives/Python3-%E6%89%BE%E5%9B%9Esort-%E4%B8%AD%E6%B6%88%E5%A4%B1%E7%9A%84cmp%E5%8F%82%E6%95%B0.html

https://docs.python.org/3/howto/sorting.html


### 14.Python中的垃圾回收机制

https://www.jianshu.com/p/1e375fb40506

https://juejin.im/post/5b34b117f265da59a50b2fbe

https://foofish.net/python-gc.html

https://testerhome.com/topics/16556

https://www.cnblogs.com/Xjng/p/5128269.html


