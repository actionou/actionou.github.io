---
title: Python中的单例模式
date: 2019-12-02 22:50:20
tags: [python]
---

## 什么是单例模式

​单例模式`Singleton Pattern`是一种常用的**软件设计模式**，该模式的主要目的是确保某一个类只有一个实例存在。

**拓展**：软件设计模式
在特定环境下人们解决某类重复出现问题的一套成功或有效的解决方案。
refer:[http://c.biancheng.net/view/1320.html](http://c.biancheng.net/view/1320.html)

| 范围\目的 | 创建型模式                            | 结构型模式                                                   | 行为型模式                                                   |
| --------- | ------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 类模式    | 工厂方法                              | (类)适配器                                                 | 模板方法、解释器                                             |
| 对象模式  | 单例<br/>原型<br/>抽象工厂<br/>建造者 | 代理<br/>(对象)适配器<br/>桥接<br/>装饰<br/>外观<br/>享元<br/>组合 | 策略<br/>命令<br/>职责链<br/>状态<br/>观察者<br/>中介者<br/>迭代器<br/>访问者<br/>备忘录 |

<!--more-->

## Python中实现单例模式的方法

### 1.使用模块

**即文件导入的形式：**
Python 的模块就是天然的单例模式，因为模块在第一次导入时，会生成 .pyc 文件，当第二次导入时，就会直接加载 .pyc 文件，而不会再次执行模块代码。因此，我们只需把相关的函数和数据定义在一个模块中，就可以获得一个单例对象了。

**pyc是什么文件?**
pyc是一种二进制文件，是由py文件经过编译后，生成的文件，是一种byte code，py文件变成pyc文件后，运行加载的速度会有所提高；另一反面，把py文件编译为pyc文件，从而可以实现部分的源码隐藏，保证了python做商业化软件时的安全性。

```python
# s1.py 文件中

class Foo(object):
    def test(self):
        print("123")
        
# v是Foo的实例
foo = Foo()
```

```python
# s2.py 文件中

from s1 import foo as f1
print(f1,id(f1))  #<s1.Foo object at 0x0000000002221710>  35788560

from s1 import foo as f2
print(f2,id(f2))   #<s1.Foo object at 0x0000000002221710>  35788560

# 两个的内存地址是一样的
# 第一次导入后，再次导入时不会再重新加载，而是直接加载.pyc文件
```

### 2.基于__new__方法实现的单例模式

当我们实例化一个对象时，是先执行了类的__new__方法（我们没写时，默认调用object.__new__）实例化对象；然后再执行类的__init__方法，对这个对象进行初始化等操作，
所以我们可以基于这个，对__new__方法做一些手脚，实现单例模式

```

```python
#=============基于__new__方法一=============
class Foo(object):

    def __new__(cls):
        # 这个函数是判断指定对象中是否有某属性，内部实现是调用了getattr()，并捕获AttributeError异常。
        # 利用反射看看这个类有没有instance属性
        if not hasattr(cls, 'instance'):
            # 调用 __new__ 方法申请内存
            # 如果不重写 __new__ 方法，会调用 object 的 __new__方法申请内存
            # 如果重写了 __new__ 方法，需要自己手动的申请内存
            cls.instance = super(Foo, cls).__new__(cls)
        return cls.instance
```

**什么是反射？**
在面向对象的思想中，把对象能够访问、查询、修改自身的状态或行为称为“反射”。

**python中的反射**
在python中，可以通过字符串的的形式来操作对象的属性。这种行为称之为python中的反射。

```python
#=============基于__new__方法二=============
class Singleton(object):
    # 定义一个类属性，用来保存实例化的对象
    # 私有类属性
    __instance = None  # 类属性

    def __new__(cls, *args, **kwargs):
        # 1.判断类属性是否为空
        if cls.__instance is None:
        # 2.调用父类的方法，为第一个对象分配空间
            cls.__instance = super(Singleton, cls).__new__(cls)
            print(cls.__instance)
        # 3.返回类属性保存的对象的应用
        return cls.__instance
```

### 3.使用类方法

```python
# 思路就是,调用类的instance方法,这样有一个弊端就是在使用类创建的时候,并不是单例了.也就是说在创建类的时候一定要用类里面规定的方法创建
class Singleton(object):
    def __init__(self, *args, **kwargs):
        pass

    @classmethod
    def get_instance(cls, *args, **kwargs):
        # 利用反射 看看这个类有没有_instance属性
        if not hasattr(Singleton, '_instance'):
            Singleton._instance = Singleton(*args, **kwargs)
        return Singleton._instance

s1 = Singleton()  # 这样创建的对象并不是单例
s2 = Singleton.get_instance() # 要调用特定的方法创建单例对象
```

### 4.使用装饰器(推荐使用)

```python
#=============函数装饰器=============
# 1.把类当成函数传递给装饰器
# 2.创建类的实例的时候调用Foo已经变成了调用inner。
# 3.在inner内部实现单例类的创建
# 4.因此foo1、foo2的值都等于inner返回值。
def singleton(fun):
    _instance = {}

    def inner():
        if fun not in _instance:
            # 使用不可变的类地址作为键，其实例作为值，
            # 每次创造实例时，首先查看该类是否存在实例，
            # 存在的话直接返回该实例即可，
            # 否则新建一个实例并存放在字典中。
            _instance[fun] = fun()
        return _instance[fun]

    return inner

@singleton
class Foo(object):
    pass

foo1 = Foo()
foo2 = Foo()
print(foo1 is foo2)  # True
```

```python
# ===========类装饰器============
class Singleton:
    def __init__(self, cls):
        self.cls = cls

    def __call__(self, *args, **kwargs):
        if not hasattr(Singleton, '_instance'):
            Singleton._instance = self.cls(*args, **kwargs)
        return Singleton._instance

@Singleton
# Demo已经变了，Demo已经是Singleton的实例了，在Singleton里面加入了__call__方法，让实例可以被调用。
class Demo(object):
    pass

do1 = Demo()
do2 = Demo()
print(id(do1), id(do2))
```

### 5.共享属性（不推荐使用）

```python
#所谓单例就是所有引用(实例、对象)拥有相同的状态(属性)和行为(方法)  
#同一个类的所有实例天然拥有相同的行为(方法),  
#只需要保证同一个类的所有实例具有相同的状态(属性)即可  
#所有实例共享属性的最简单最直接的方法就是__dict__属性指向(引用)同一个字典(dict)
class Singleton(object):
    _state = {}

    def __new__(cls, *args, **kw):
        ob = super(Singleton, cls).__new__(cls)
        ob.__dict__ = cls._state
        return ob

class MyClass2(Singleton):
    def __init__(self, name):
        self.name = name

    def foo(self):
        print(f'最后的结果是{self.name}') # 实例化N个对象打印的都是最后创建的对象的name属性
```

### 6.基于metaclass(元类)实现的单例模式

**什么是元类？**
实例对象是由类来创建，那么类又是由什么来创建的呢？ 答案就是元类。

```python
class SingletonType(type):

    def __call__(cls, *args, **kwargs):
        if not hasattr(cls, "_instance"):
            cls._instance = super(SingletonType, cls).__call__(*args, **kwargs)

        return cls._instance


# 类() 执行type的 __call__方法(类的__new__方法,类的__init__方法)
# metaclass=SingletonType 这样就表示调用SingletonType的__call__方法来创建Foo类
# 判断实例化的对象是否有_instance这个属性
# 然后调用父类type的__call__方法创建Foo对象(类)
# 同时Foo创建了就会调用Foo类的__new__方法和__init__方法实例化对象
# 返回这个实例化对象
class Foo(metaclass=SingletonType):
    def __init__(self, name):
        self.name = name


obj1 = Foo('张三')
obj2 = Foo('李四')
print(obj1, obj2)
print(id(obj1), id(obj2))
```

元类实现单例：

```python
class SingletonMeta(type):
    def __init__(cls,*args, **kwargs):
    # 这个self是指定了使用SingletonMeta作为元类的类对应的对象
    	super().__init__(*args, **kwargs)
        cls.__instance = None
    def __call__(cls,*args, **kwargs):
        if cls.__instance is None:
            cls.__instance = super().__call__(*args, **kwargs)
        return cls.__instance

# 加了metaclass=type的话，就会实现单例
class President(object, metaclass=type):
    pass

President() # 这个类是SingletonMeta创建出的对象，那么在类后面加()，相当于调用了元类(type)的__call__魔术方法。所以可以在__call__里面做文章，来实现单例。


# 加了metaclass=type的话，就会实现单例
class Foo(metaclass=SingletonMeta):
    pass
```
