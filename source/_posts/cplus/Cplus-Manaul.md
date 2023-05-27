# C++ 语法手册

> C++是一种通用的高级编程语言，由Bjarne Stroustrup于1980年代早期在C语言的基础上开发而来。它继承了C语言的高效性和低级别控制，同时添加了面向对象和泛型编程的特性，使得开发人员能够以高效和可维护的方式构建复杂的软件系统。

## 基本概念

### 注释

与大多数语言一样，C++ 的注释有两种表示方法：

1. 单行注释：`//这里是单行注释内容`
2. 多行注释：`/* 多行注释 */`

### 标识符

标识符是用于表示以下内容之一的字符序列：

- 对象或变量名称
- 类、结构或联合名称
- 枚举类型名称
- 类、结构、联合或枚举的成员
- 函数或类成员函数
- typedef 名称
- 标签名称
- 宏名称
- 宏参数

C++ 规定标识符由`英文字母`、`数字`和`_`组成，需要注意的是：

1. 首字符不能为数字
2. 区分大小写
3. 以两个下划线或下划线和大写字母开头的名称被保留给实现（编译器及其使用的资源）使用。以一个下划线开头的名称被保留给实现，用作全局标识符

### 关键字

> [C++ 关键词](https://zh.cppreference.com/w/cpp/keyword)

### 头文件

> 头文件的引入详见《预处理器》一章
>
> 本章扩展 [理解 C++ 中的头文件和源文件的作用](https://www.runoob.com/w3cnote/cpp-header.html)

由于一个头文件可能会被多个文件执行 include 操作，因此它不能包含可能生成多个同名定义的定义。 **不允许以下操作，否则会被视为非常糟糕的做法**：

- 命名空间或全局范围内的内置类型定义
- 非内联函数定义
- 非常量变量定义
- 聚合定义
- 未命名的命名空间
- using 指令

使用 `using` 指令不一定会导致错误，但可能会导致问题，因为它将命名空间引入每个直接或间接包含该标头的 .cpp 文件中的范围

### 临时对象[todo]

>  Todo

### 数据对齐

> 参考
>
> 1. [浅析数据对齐](https://blog.csdn.net/jiasike/article/details/76731702)
>
> 2. [数据对齐详解](https://www.cnblogs.com/bakari/archive/2012/08/27/2658956.html)

## 基本(内置)类型

内置类型（也称基本类型）由 C++ 语言标准指定，内置于编译器中， 使用时无需引入任何头文件。 内置类型分为三个主要类别：**整型、浮点和 void**。

### `void`类型

[`void`](https://learn.microsoft.com/zh-cn/cpp/cpp/void-cpp?view=msvc-170) 类型描述值的空集。 无法指定类型为 **`void`** 的变量。 **`void`** 类型主要用于声明不返回值的函数，或用于声明指向非类型化或任意类型化数据的一般指针。 

```c++
void return_nothing(){
  // A void function can have a return with no argument,
   // or no return statement.
}
```

如果指针类型为 `void*`，则该指针可以指向任何未使用 `const` 或 `volatile` 关键字声明的变量。 `void*` 指针不能取消引用，除非它被强制转换为另一种类型。 `void*` 指针可以转换为任何其他类型的数据指针。

### `nullptr`类型

**`nullptr`** 关键字表示空指针值

```c++
int *p = nullptr;
```

> Todo: `nullptr`、`NULL`和`0`的区别

### `bool`类型

布尔类型

### `int`整型

| 类型            | 说明                           | 别名                                                         |
| :-------------- | :----------------------------- | :----------------------------------------------------------- |
| `short int`     | 至少2字节                      | `short`, `short int`, `signed short`, `signed short int`     |
| `int`           | 至少与`short`一样长            | `int`, `signed`, `signed int`                                |
| `long int`      | 至少4字节，且至少与`int`一样长 | `long`, `long int`, `signed long`, `signed long int`         |
| `long long int` | 至少8，且至少与`long`一样长    | `long long`, `long long int`, `signed long long`, `signed long long int` |

> 备注：
>
> 1. `short`、`long` 和 `long long` 为大小修饰符， `signed`和`unsigned`为符号修饰符
> 2. 不同于java， 对于C++不同平台整型的大小可能不同

### 浮点型

| 类型              | 说明                                                         |
| :---------------- | :----------------------------------------------------------- |
| **`float`**       | 在 C++ 中，**`float`** 类型是最小的浮点类型。至少32位（4字节） |
| **`double`**      | **`double`** 类型至少48位，是大于或等于 **`float`**类型的大小但小于或等于 **`long double`**类型的大小的浮点类型。 |
| **`long double`** | **`long double`** 类型是大于或等于 **`double`**类型的浮点类型。 |

### 字符型

**`char`** 类型用于存储字符，实际上也是一种整型。C++ 编译器将 **`char`**, **`signed char`**和 **`unsigned char`** 类型的变量视为不同类型。

```c++
char ch = 'c'; // c++ 对字符使用单引号

char ch[] = "string"; // 字符数组
```

**`wchar_t`** 类型的变量是宽字符或多字节字符类型。 在字符或字符串文本前使用 **`L`** 前缀可指定宽字符类型。

```c++
wchar_t wch = L'c';
```

## 复合类型

### 指针

> 指针是一个变量，其存储的是值的地址，而不是值本身

指针在 C 和 C++ 中广泛用于三个主要用途：

- 在堆上分配新对象，
- 将函数传递给其他函数
- 循环访问数组或其他数据结构中的元素。

在 C 样式编程中，原始指针用于所有这些场景。 但是，原始指针会导致许多严重的编程错误。 因此，**强烈建议不要使用它们**，除非它们提供了显著的性能优势，并且对于哪个指针是负责删除对象的指针没有歧义。 新式 C++ 提供了智能指针用于分配对象，提供了迭代器用于遍历数据结构，还提供了 Lambda 表达式用于传递函数。 通过使用这些语言和库设施，而不是原始指针，可使程序更安全、更易于调试，以及更易于理解和维护。

#### 声明与初始化指针

```c++
int n = 1;
int *p = nullptr; // 声明p并初始化为空指针
p = &n; // 把n的地址赋给p
```

在👆的例子中，`p`表示的是一个地址，`*p`表示存在在该地址的具体值，也就是`n`的值`1`

#### 智能指针[todo]

> Todo

### 数组

数组是一种数据格式，能够存储多个同类型的值，它们占据一块连续的内存区。

#### 数组的声明

- 堆栈声明

  格式：`typeName` `arrayName[arraySize]`

  以下示例声明了要在堆栈上分配的 4 个整型的数组，元素数量必须以整数文本或常量表达式的形式提供，这是因为，编译器必须知道要分配多少堆栈空间，它不能使用在运行时计算的值。 

  > ⚠️：
  >
  > 1. 如果没有初始化定义数组， 则其元素的值是不确定的。
  > 2. 只有定义数组的时候才能初始化，以后就不能使用了，也不能将一个数组赋值给另一个数组。

  ```c++
  int cards[4] = {3,4,5}; // okay
  int hand[4]; 
  hand[4] = {3,4,5}; // not allowed
  hand = cards; // not allowed
  ```

- 堆声明

  你可能需要一个太大的、以致无法在堆栈上分配的数组，或者需要一个其大小在编译时未知的数组。 可以使用`new`表达式在堆上分配此数组。这时数组的大小将在运行时确定，使用完后 ，应使用`delete[]` 来释放内存

  ```c++
  void do_something(size_t size)
  {
      // Declare an array of doubles to be allocated on the heap
      double* numbers = new double[size]{ 0 };
      // ...
      delete [] numbers;
  }
  ```

#### 将数组传递给函数

将数组传递给函数时，该数组将作为指向第一个元素的指针传递，无论它是基于堆栈的数组还是基于堆的数组。 指针不包含其他大小或类型信息。 此行为称为指针衰减。 将数组传递给函数时，始终必须在单独的参数中指定元素数量。 此行为还意味着将数组传递给函数时不会复制数组元素。 若要防止函数修改元素，请将参数指定为指向 **`const`** 元素的指针。

以下示例演示了一个接受数组和长度的函数。 **指针指向原始数组而不是副本**。 由于参数不是 **`const`**，因此该函数可以修改数组元素。

```c++
void process(double *p, const size_t len)
{
    std::cout << "process:\n";
    for (size_t i = 0; i < len; ++i)
    {
        // do something with p[i]
    }
}
```

将数组参数 `p` 声明并定义为 **`const`**，使其在函数块中为只读：

```c++
void process(const double *p, const size_t len);
```

也可以用这些方式声明相同的函数，而无需改变行为。 数组仍作为指向第一个元素的指针传递：

```c++
// Unsized array
void process(const double p[], const size_t len);

// Fixed-size array. Length must still be specified explicitly.
void process(const double p[1000], const size_t len);
```

#### 数组的替代

##### 模版类`vector`

`vector`是一种动态数组，可以在运行阶段设置`vector`的长度，可以在末位添加数据，也可以在中间插入数据等。

```c++
vector<typeName> vt(size);
```

使用`vector`时，必须包含头文件`<vector>`，其次，`vector`包含在命名空间`std`中

```c++
#include <vector>
using namespace std;
vector<int> vi; // create a zero-size array of int
int n;
cin >> n;
vector<double> vd(n); // create an array of double; 
```

##### 模版类`array` 

如果你需要的是长度固定的数组，也可以使用`array`，它也位于命名空间`std`中。注意，`array`对象的长度是固定的

```c++
#include <array>
using namespace std;

array<int, 5> ai; // create array of 5 int
array<double, 4> ad = {1.2, 1.3, 1.4, 1.5};
```

### 字符串[todo]

> Todo

### `struct` 结构类型

> [struct 结构](https://wangdoc.com/clang/struct)

### `union `联合类型

> [Union 结构](https://wangdoc.com/clang/union)

### `Enum` 枚举类型

```c++
enum Colors { 
  RED, 
  GREEN, 
  BLUE
};

Colors color;
```

## 预处理器

### `include`

使用`#include`指令引入头文件，有两种形式：

```c++
// 形式一
#include <iostream> // 加载系统提供的文件

// 形式二
#include "my_class.h" // 加载用户提供的文件
```

## 命名空间

## 函数

## 类

## 运算符重载

## 模版

## 异常处理

## 附录

## 参考

- [C++ 参考手册](https://zh.cppreference.com/w/cpp)
- [C 语言简介](https://wangdoc.com/clang/intro)
- [Microsoft C++ 语言文档](https://learn.microsoft.com/zh-cn/cpp/cpp/?view=msvc-170)