## 基本数据类型

在 C 语言中，仅有 4 种基本数据类型——整型、浮点型、指针和聚合类型（如数组和结构等）。


# 二、数据类型

## 2.1、类型描述

变量的类型决定了变量存储占用的空间，以及如何解释存储的位模式。

C中的类型可分为以下几种：

- 基本类型：它们是算术类型，包括两种类型：整数类型和浮点类型。
- 枚举类型：它们也是算术类型，被用来定义在程序中只能赋予其一定的离散数值的变量。
- `void`类型：类型说明符`void`表明没有可用的值
- 派生类型：它们包括：指针类型、数组类型、结构类型、共用体类型和函数类型。

*数组类型和结构类型统称为聚合类型。函数的类型指的是函数返回值的类型。*

## 2.2、基本数据类型

|类型|存储大小|值范围|精度|
|---|---|---|---|
|char|1byte|-128到127或0到255||
|unsigned char|1 byte|0到255||
|signed char|1 byte|-128到127||
|int|2 或 4 bytes|-32,768 到 32,767 或 -2,147,483,648 到 2,147,483,647||
|unsigned int|2 或 4 bytes|0 到 65,535 或 0 到 4,294,967,295||
|short|2 bytes|-32,768到32,767||
|unsigned short|2 bytes|0 到 65,535||
|long|4 bytes|-2,147,483,648 到 2,147,483,647||
|unsigned long|4 bytes|0 到 4,294,967,295||
|float|4 bytes|1.2E-38 到 3.4E+38|6位小数|
|double|8 bytes|2.3E-308 到 1.7E+308|15位小数|
|long double|10 bytes|3.4E-4932 到 1.1E+4932|19位小数|

为了得到某个类型或某个变量在特定平台上的准确大小，您可以使用 sizeof 运算符。表达式 sizeof(type) 得到对象或类型的存储字节大小。下面的实例演示了获取 float 类型占用的存储空间以及它的范围值：

```c
#include <stdio.h>
#include <float.h>

int main() {
   printf("Storage size for float : %d \n", sizeof(float));
   printf("Minimum float positive value: %E\n", FLT_MIN );
   printf("Maximum float positive value: %E\n", FLT_MAX );
   printf("Precision value: %d\n", FLT_DIG );
   return 0;
}
```

```shell
Storage size for float : 4
Minimum float positive value: 1.175494E-38
Maximum float positive value: 3.402823E+38
Precision value: 6
```

## 2.3、void类型

void类型指定没有可用的值。它通常用于以下三种情况：

|类型|描述|
|---|---|
|函数返回为空|C 中有各种函数都不返回值，或者您可以说它们返回空。不返回值的函数的返回类型为空。例如 void exit (int status);|
|函数参数为空|C 中有各种函数不接受任何参数。不带参数的函数可以接受一个 void。例如 int rand(void);|
|指针指向 void|类型为 void * 的指针代表对象的地址，而不是类型。例如，内存分配函数 void * malloc( size_t size ); 返回指向 void 的指针，可以转换为任何数据类型。|