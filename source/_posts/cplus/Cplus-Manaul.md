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

C++ 规定标识符由`英文字母（区分大小写）`、`数字`和`_`组成，需要注意的是首字符不能为数字

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

### 临时对象

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

| 类型            | 说明      | 别名                                                         |
| :-------------- | :-------- | :----------------------------------------------------------- |
| `short int`     | 至少2字节 | `short`, `short int`, `signed short`, `signed short int`     |
| `int`           |           | `int`, `signed`, `signed int`                                |
| `long int`      | 至少4字节 | `long`, `long int`, `signed long`, `signed long int`         |
| `long long int` | 至少8     | `long long`, `long long int`, `signed long long`, `signed long long int` |

> 备注：
>
> 1. `short`、`long` 和 `long long` 为大小修饰符， `signed`和`unsigned`为符号修饰符
> 2. 不同于java， 对于C++不同平台整型的大小可能不同

### 浮点型

| 类型              | 说明                                                         |
| :---------------- | :----------------------------------------------------------- |
| **`float`**       | 在 C++ 中，**`float`** 类型是最小的浮点类型。                |
| **`double`**      | **`double`** 类型是大于或等于 **`float`**类型的大小但小于或等于 **`long double`**类型的大小的浮点类型。 |
| **`long double`** | **`long double`** 类型是大于或等于 **`double`**类型的浮点类型。 |

### 字符型

**`char`** 类型是一种字符表示类型，可有效地对基本执行字符集的成员进行编码。 C++ 编译器将 **`char`**, **`signed char`**和 **`unsigned char`** 类型的变量视为不同类型。

**`wchar_t`** 类型的变量是宽字符或多字节字符类型。 在字符或字符串文本前使用 **`L`** 前缀可指定宽字符类型。

## 指针

## 数组

## 字符串

## `struct` 结构类型

## `union `联合类型

## `Enum` 枚举类型

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