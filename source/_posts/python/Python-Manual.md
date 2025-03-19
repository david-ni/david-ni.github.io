---
title: Python 入门手册
description: 万丈高楼平地起，带你敲开Python的大门...
categories: 
- Python
---

# Python Manual

## 如何运行Python程序

可以使用以下几种不同的方式来运行Python程序

1. 使用Python解释器
   打开终端并输入`python3`，然后在终端中输入Python程序代码，然后按下`Enter`键即可运行程序。

2. 运行python文件
   在终端中输入`python3 filename.py`，然后按下`Enter`键即可运行程序。

3. 在VSCode中运行   
   [Getting Started with Python in VS Code](https://code.visualstudio.com/docs/python/python-tutorial)

## 基础语法

### 变量

变量的命名规则：

1. 由 字母、汉字（包括表情符，但尽量别用）下划线 和 数字 组成
2. 大小写敏感（就是大小写不一样）
3. 不能以数字开头
4. 不能与关键字重名

```python
name = "Roger"
```

### 代码行

1. 单行代码

每行代码结尾不需要加标点

```python
a = 123
```

2. 同一行显示多条语句

用分号隔开

```python
a = 123; b = 456; c = 789
```

3. 多行代码

多行代码，直接换行

```python
a = 123
b = a + 1
c = b + 2
```

### 缩进

Python中的缩进是有意义的。同一级别的代码，缩进必须相同
您不能像这样随意缩进：

```python
name = "Flavio"
    print(name)
```

### 注释

```python
# 这是单行注释
```

### 运算符

#### 算术运算符

在python中除了`+`、`-`、`*`、`/`、`%`这几种常规运算符外还有`**`（求幂）和 `//`（向下取整除法）

```python
1 + 1  # 2
2 - 1  # 1
2 * 2  # 4
4 / 2  # 2
4 % 3  # 1
4 ** 2  # 16
4 // 2  # 2
```

#### 布尔运算符

python的布尔运算符：`not`逻辑非、`and`逻辑与和`or`逻辑或

```python
condition1 = True
condition2 = False

not condition1  # False
condition1 and condition2  # False
condition1 or condition2  # True
```

#### 成员检测

运算符`in` 和 `not in` 用于成员检测操作，即判断一个元素是不是容器全集中的一员，如果是，返回 `True`，否则返回 `False`。

```python
'a' in 'abc' # True
'abb' in ['abc', 'efg'] # False


```







## Python 数据类型

| **类型**         | **描述**                                               | **示例**                                                 |
| ---------------- | ------------------------------------------------------ | -------------------------------------------------------- |
| 数字 number      | int(整数) 、float(有小数位)以及complex（复数）         | 1313，-323，0, 3.14                                      |
| 布尔类型 boolean | 逻辑真假，属于数字类型                                 | True, False                                              |
| 字符串 str       | 文本字符，空格也是一个字符                             | 'Hello World!', '123', ''                                |
| 列表 list        | 一个列表的每个元素被分配一个数字来表示它的位置或索引   | [1, 2, 3, 4, 5 ], ['apple', 'orange', 'lemon', 'cherry'] |
| 元组 tupple      | 可以简单地理解为“只读列表”                             | (1, 2, 3)， ('physics', 'math', 1997, 2000)              |
| 字典 dict        | 每个键与其值使用一个冒号(:)分开，键-值对是使用逗号分隔 | {'Name': 'Maxsu', 'Age': 7, 'Class': 'First'}            |
| 集合 set         | 一组不重复key的集合，不存储value                       | set([1, 1, 2, 2, 3, 3]), `{'5元', '10元', '20元'}`       |



### 数据类型判断

Python 内置的 `type()` 函数可以查看数据的类型，如：

```python
type(123) # int 
type(1.23) # float
type("string") # str
type(True) # bool
type([1,2,3]) # list
type({"a":1,"b":2}) # dict
type((1,2,3) # tupple
     
if type(123) == int:
    print("123 is int")
```

也可以用 `isinstance` 来判断它是不是一个指定的类型：

```python
isinstance(123, int) # 123 是不是一个数字整型
```

### 数字类型

| 类型      | 描述                                                         | 示例                                                         |
| --------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `int`     | 有符号整数，表示没有小数点的0、正或负整数，在Python3 中的整数是无限大小的 | `1`、`-1`、`0b01100001`(二进制)、`0o301`8 进制、`0x61`(16进制) |
| `float`   | 有小数点的数字，`float('inf')` 表示正无穷，`float("-inf")` 负无穷。`float("nan")` 代表缺失值。 | `1.10`                                                       |
| `complex` | 复数是以a + bJ的形式，其中a和b是浮点，J(或j)表示-1的平方根(虚数)。数字的实部是a，虚部是b。 |                                                              |

> Python 3.6 开始支持新的数字下划线功能，对于很大的数字以提高可读性
>
> ```python
> 123_456_789 # 123456789 可以在较大的数字用下划线连接
> 123_456.888 # 123456.888
> ```



如果不同数字类型进行计算，则：

- 全 int 加减乘还是 int，除会变为 float
- float 和 int 计算为 float

转换类型可以使用 `int(a)` 和 `float(a)` 将数字进行类型转换，float 转 int 时会丢失小数点。



### 布尔值

如果我们检测变量，以下情况会得到假，其他情况为真。

- `None`,` False`
- 数值中的 `0`,` 0.0`, `0j`(虚数), `Decimal(0)`, `Fraction(0, 1)`
- 空字符串 `""`、空元组 `()`、空列表 `[]`
- 空字典 `{}`、空集合 `set()`
- 类的对象特殊方法 `__bool__()` 返回 False 或是定义了 `__len__() ` 方法且返回 0

可以使用 `bool()` 来判断真假：

```python
bool(None) # False
bool(0) # False
bool([]) # False
bool(()) # False
```

### 字符串

字符是一个不可变的序列类型，单引号、双引号都可以定义字符串

```python
str = "hello" # 双引号
str = 'world' # 单引号

# 三个单引号或者双引号，可以定义多行字符
str = ''' hello
world
I love python
'''
#  加前缀 u，显式说明是 unicode 类型，一般不加
str = u'world'
```

> 字符的运算
>
> ```python
> 'good' + 'bye' # goodbye 拼接字符
> 'good'*3 # goodgoodgood 重复3次
> ```

### 列表

列表是用方括号组织起来，每个元素用逗号隔开，每个具体的元素**可以是任意类型的内容**，**通常类型是相同的**。

```python
x = [] # 空列表
x = [1, 2, 3, 4, 5]
x = ['a', 'b', 'c']
x = ['a', 1.5, True, [2, 3, 4]] # 各种类型混杂
```

#### 类型转换

可以将可迭代的内容转换成列表

```python
# 字符串
y = '1345'
list(y) # ['1', '3', '4', '5'] 将字符串转换成列表

# 元组
z = ('a', 'b', 'c')
list(z) # ['a', 'b', 'c'] 将元组转换成列表

# 字典
d = {'Name': 'Tom', 'Age': 7, 'Class': 'First'}
list(d) # ['Name', 'Age', 'Class'] 字典 key 转成列表
list(d.values()) # ['Tom', 7, 'First'] 字典 value 转成列表
# 字典键值对（一个元组）转成列表
list(d.items()) # [('Name', 'Tom'), ('Age', 7), ('Class', 'First')]
```

### 元组

元组的结构和列表一样，元组用圆括号把所有项括起来，元素之间用逗号分隔：

> **元组**（tuple）跟列表（list）非常相似，二者之间的差异就是**元组（tuple）不可改变（包括值和位置顺序）**，列表（list）是可以改变的。元组使用小括号`(...)`，列表使用方括号。

```python
a = () # 空元组
a = (1, ) # 只有一个元素
a = (1, 2, 3) # 定义一个元组
tuple() # 生成空元组
tuple('hello') # ('h', 'e', 'l', 'l', 'o')

# 没有括号也可以定义一个元组
a = 1,23,4,56 # a: (1, 23, 4, 56)
a = 1, # a: (1, )

# 元组内元素可以混杂其他类型
a = ('a', 1.5, True, [2, 3, 4]) # 各种类型混杂
```

### 字典

字典的基本结构：`{key:value,...}`

```python
d = {} # 定义空字典
d = dict() # 定义空字典
d = {'a': 1, 'b': 2, 'c': 3}
d = {'a': 1, 'a': 1, 'a': 1} # { 'a': 1} key 不能重复, 取最后一个
d = {'a': 1, 'b': {'x': 3}} # 嵌套字典
d = {'a': [1,2,3], 'b': [4,5,6]} # 嵌套列表

# 以下均可定义如下结果
# {'name': 'Tom', 'age': 18, 'height': 180}
d = dict(name='Tom', age=18, height=180)
d = dict([('name', 'Tom'), ('age', 18), ('height', 180)])
d = dict(zip(['name', 'age', 'height'], ['Tom', 18, 180]))
```

### 集合Set

**集合**（set）是存放无顺序无索引内容的容器。在 Python 中，集合用花括号`{}` 编写。我们用集合可以消除重复的元素，也可以用它作交、差、并、补等数学运算。集合本质是字典的键，不重复。

**集合里不能有列表、集合、字典等可变的内容。无法更改集合里的元素，但可以添加新元素**。

```python
s = {'5元', '10元', '20元'} # 定义集合
s = set() # 空集合
s = set([1,2,3,4,5]) # {1, 2, 3, 4, 5} 使用列表定义
s = {1, True, 'a'}
s = {1, 1, 1} # {1} 去重
type(s) # set 类型检测
```

我们不能使用 `{}` 语法创建空集，因为它会创建一个空字典。要创建一个空集，我们使用 set()。

定义集合时的每个元素必须是不可变对象，如列表作为元素时会报错：

```python
{[1,2], 2}
# TypeError: unhashable type: 'list'
```

### 序列

序列（Sequence）是有顺序的数据列，Python 有三种基本序列类型：`list`,` tuple` 和 `range` 对象，序列（Sequence）是有顺序的数据列，二进制数据（bytes） 和 文本字符串（str）也是序列类型，它们是特殊序列类型，会有一些特殊的性质和操作。



Python 的内置序列类型有：

| 类型     | 创建方法     | 可变性 |
| -------- | ------------ | ------ |
| 列表     | list()       | 可变   |
| 元组     | tuple()      | 不可变 |
| 等差数列 | range()      | 不可变 |
| 字符串   | str()        | 不可变 |
| 字节串   | bytes()      | 不可变 |
| 字节数组 | bytearray()  | 可变   |
| 内存视图 | memoryview() | 不可变 |

> 要注意的是，集合、字典不是序列类型，虽然字典在最新的 Python 版本中具备了元素顺序特性，但这不是一种「保证」。

> 如何判断一个数据是不是序列类型呢？

```python
from collections import abc

a = [1, 2, 3]
b = {1, 2, 3}
c = (1, 2, 3)

isinstance(a, abc.Sequence)
# True
isinstance(b, abc.Sequence)
# False

# 可变类型
isinstance(a, abc.MutableSequence)
# True
isinstance(c, abc.MutableSequence)
# False
```

#### 序列操作

序列的特点是由若干元素组成，元素的分布有顺序，因此根据这个特点，它们支持一些共性的操作。

#### 通用操作

以下是所有序列类型均支持的操作：

| 运算                   | 结果：                                                       | 备注                                             |
| :--------------------- | :----------------------------------------------------------- | :----------------------------------------------- |
| `x in s`               | 如果 s 中的某项等于 x 则结果为 True，否则为 False            |                                                  |
| `x not in s`           | 如果 s 中的某项等于 x 则结果为 False，否则为 True            |                                                  |
| `s + t`                | s 与 t 相拼接                                                |                                                  |
| `s * n` 或 `n * s`     | 相当于 s 与自身进行 n 次拼接                                 |                                                  |
| `s[i]`                 | s 的第 i 项，起始为 0                                        | [切片操作](https://gairuo.com/p/python-slicings) |
| `s[i:j]`               | s 从 i 到 j 的切片                                           |                                                  |
| `s[i:j:k]`             | s 从 i 到 j 步长为 k 的切片                                  |                                                  |
| `len(s)`               | s 的长度                                                     |                                                  |
| `min(s)`               | s 的最小项                                                   |                                                  |
| `max(s)`               | s 的最大项                                                   |                                                  |
| `s.index(x[, i[, j]])` | x 在 s 中首次出现项的索引号 （索引号在 i 或其后且在 j 之前） | [count 方法](https://gairuo.com/p/python-count)  |
| `s.count(x)`           | x 在 s 中出现的总次数                                        | [index 方法](https://gairuo.com/p/python-index)  |
| `for i in x:pass`      | 迭代                                                         |                                                  |
| `hash(x)`              | 对象的哈希值                                                 | 仅不可变序列                                     |
| `sorted(x)`            | 排序                                                         |                                                  |
| `all(x)` 或者 `any(x)` | 全真或者有真检测                                             |                                                  |
| `iter(x)`              | 生成迭代器                                                   |                                                  |

注：以上部分操作需要额外的特殊方法实现。当我们在处理数据量大且需频繁查找元素（如 in 操作）时，最好使用 set、dict ，这样将会大幅度提升处理速度。

#### 可变序列类型

以下是仅可变序列支持的操作：

| 运算                      | 结果：                                                   | 备注 |
| :------------------------ | :------------------------------------------------------- | ---: |
| `s[i] = x`                | 将 s 的第 i 项替换为 x                                   |      |
| `s[i:j] = t`              | 将 s 从 i 到 j 的切片替换为可迭代对象 t 的内容           |      |
| `del s[i:j]`              | 等同于 `s[i:j] = []`                                     |      |
| `s[i:j:k] = t`            | 将 `s[i:j:k]` 的元素替换为 t 的元素                      |      |
| `del s[i:j:k]`            | 从列表中移除 `s[i:j:k]` 的元素                           |      |
| `s.append(x)`             | 将 x 添加到序列的末尾 (等同于 `s[len(s):len(s)] = [x]`)  |      |
| `s.clear()`               | 从 s 中移除所有项 (等同于 `del s[:]`)                    |      |
| `s.copy()`                | 创建 s 的浅拷贝 (等同于 `s[:]`)                          |      |
| `s.extend(t)` 或 `s += t` | 用 t 的内容扩展 s (基本上等同于 `s[len(s):len(s)] = t`)  |      |
| `s *= n`                  | 使用 s 的内容重复 n 次来对其进行更新                     |      |
| `s.insert(i, x)`          | 在由 i 给出的索引位置将 x 插入 s (等同于 `s[i:i] = [x]`) |      |
| `s.pop()` 或 `s.pop(i)`   | 提取在 i 位置上的项，并将其从 s 中移除                   |      |
| `s.remove(x)`             | 删除 s 中第一个 `s[i]` 等于 x 的项目。                   |      |
| `s.reverse()`             | 就地将列表中的元素逆序。                                 |      |

## 函数

### 定义函数

语法：

```python
def <函数名>(<参数>):
	"""<函数说明>"""
	<函数代码>
	<pass> # 如果函数什么都不做，或者部分分支逻辑不干啥，可以用它
	return <返回内容>
```

#### 默认值参数

我们在定义函数时可以给一参数给定默认值，在调用的时候就不需要传此参数了，在有需要的时候再传。

```python
# 定义
def foo(name, age=18, boy=True):
    pass
# 调用
foo('小刚', age=22)
```

#### 位置参数

指在固定位置的参数，在调用时按位置顺序传入。

```python
# 定义
def foo(x, y):
    return x-y

# 调用
foo(10, 8)
```

#### 关键字参数

调用函数时我们可以使用调用关键字参数，格式为 `关键字=参数值`，这样就不用关心顺序问题了。

```python
def say(name, age=18, words='hello'):
    print(f'{age}岁的{name}说：{words}')
# 调用
say(name='tom', age=20) # 20岁的tom说：hello
```

关键字参数后面必须都是关键字参数：

```python
say('tom', age=20) # 20岁的tom说：hello
say('tom', age=20, 'good') # 报错
```

#### 可变参数

有时候我们不确定参数的数量，可使用可变参数，类似 `**kwargs`。其中：

- `*args` 一个星号，以元组(tuple)的形式导入
- `**kwargs` 两个星号，以字典的形式导入

```python
def foo(*t):
    print(t)
# 调用
foo(1) # (1,)
foo(1,4,5,6) # (1, 4, 5, 6)

def foo(**d):
    print(d)
# 调用
foo(a=1) # {'a': 1}
foo(a=1,b=4,c=5) # {'a': 1, 'b': 4, 'c': 5}
```

可变参数与其他类型的参数混用时，采用元组拆包（解包）的原则。

```python
def foo(a, b,*c, d):
    print(a,b, c, d)
# 调用 
foo(1,2,3,4,5,d=6) # 1 2 (3, 4, 5) 6 d 要用关键字传入
```

可以通过 `**` 用字典传递关键字参数：

```python
def say(name, age=18, words='hello'):
    print(f'{age}岁的{name}说：{words}')
# 调用
d = {'name':'tom', 'age':20, 'words': 'hello'}
say(**d) # 20岁的tom说：hello
```

### 匿名函数

语法结构：

```python
lambda <参数>: <逻辑表达式代码>
```

```python
add = lambda x,y: x+y
add(1,4) # 5
```

## 类

语法：

```python
class ClassName(object):
    # 类属性
    class_attribute = "I am a class attribute"
		
    # 构造函数
    def __init__(self, instance_attribute):
        # 实例属性
        self.instance_attribute = instance_attribute

    # 实例方法
    def instance_method(self):
        print(f"This is an instance method. Instance attribute: {self.instance_attribute}")

    # 类方法
    @classmethod
    def class_method(cls):
        print(f"This is a class method. Class attribute: {cls.class_attribute}")

    # 静态方法
    @staticmethod
    def static_method():
        print("This is a static method.")
        
# 实例化
classInstant = ClassName("I am an instance attribute outside the class")
classInstant.instance_method()
ClassName.class_method()
ClassName.static_method()
```

> 类方法和静态方法区别：
>
> - **类方法**：使用 `@classmethod` 装饰器来定义，类方法的第一个参数通常命名为 `cls`，它代表类本身。
> - **静态方法**：使用 `@staticmethod` 装饰器来定义，静态方法没有类似 `self` 或 `cls` 这样特殊的第一个参数。



## 错误与异常

### 异常的处理

基本的语法结构如下：

```python
try:
    <语句>        # 正常运行的代码
except <错误类型>：
    <语句>        # 如果在 try 引发 '错误类型' 异常后执行的代码
except <错误类型2>:
    <语句>        # 如果引发了'错误类型2'异常后执行的代码
else:
    <语句>        # 如果没有异常发生
finally:
	  <语句>    
```

### 自定义异常

Exception 是异常的一个基类，所有内置的非系统退出类异常都派生自此类

```python
## 示例
class CusError(Exception):
    def __init__(self, errorinfo):
        super().__init__(self) # 初始化父类
        self.errorinfo = errorinfo
    def __str__(self):
        return self.errorinfo
```



## 模块

### 模块

模块是一个包含Python 代码的文件，通常用于组织相关功能的代码片段。模块可以定义函数、类和变量，还可以包含可执行的代码.模块有助于将代码分割成更小的部分，使代码更易于维护和重用.

示例：假设有一个文件 `math_operations.py`，里面定义了一些数学操作函数：

```python
# math_operations.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b
```

使用该模块：

```python
import math_operations

result = math_operations.add (5, 3)
print (result)
# 输出 8
```

### 包

包是一个包含多个模块或子包的目录，用于组织模块。需要有 `__init__.py` 文件才能让 Python 将包含该文件的目录当作包来处理（除非使用 [namespace package](https://docs.python.org/zh-cn/3.13/glossary.html#term-namespace-package)，这是一个相对高级的特性）。

```
# 包的结构
mypackage/
    __init__.py
    module1.py
    module2.py
    subpackage/
        __init__.py
        module3. py
```

使用包中的模块：

```python
from mypackage import module1
from mypackage.subpackage import module3

result = modulel.some_function()
print(result)
```

> `__init__.py` 可以包含包的初始化代码，这些代码会在包被导入时执行。例如，可以在这里初始化包级别的变量、导入子模块等。
>
> ```python
> # mypackage/__init__.py
> print("Initializing mypackage")
> from .module1 import func1
> from .module2 import func2
> 
> # __all__的用法和含义 https://docs.python.org/zh-cn/3.13/tutorial/modules.html#importing-from-a-package
> __all__ = ['func1', 'func2']
> 
> ```

## 常用框架

1. [Django](https://docs.djangoproject.com/zh-hans/4.2/intro/overview/) 一款流行的Web应用框架

## 其他

### 如何在 Python 中创建一个虚拟环境

- 创建虚拟环境

  ```python
  python<version> -m venv /path/to/new/virtual/environment
  ```

- 激活虚拟环境

  ```python
  source /path/to/new/virtual/environment/bin/activate
  ```

## 参考

1. [Python 中文手册](https://docs.python.org/zh-cn/3/index.html)

2. [Python 教程](https://gairuo.com/p/python-data-types)