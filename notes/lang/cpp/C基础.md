- 一、C程序结构
- 二、数据类型

# 一、C程序结构

C 程序主要包括以下部分：
- 预处理器指令
- 函数
- 变量
- 语句 & 表达式
- 注释

```c
// 预处理器指令，告诉C编译器在实际编译之前要包含 stdio.h 文件
#include <stdio.h>
// int main() 主函数，程序从这里开始执行
int main() {
	// 下面是注释，将会编译器忽略
	/* 我的第一个 C 程序 */
	// printf是C中打印的函数，会在屏幕上显示消息"Hello World!"
	printf("Hello World! \n");
	// return 0；是终止main()函数，并返回值0
	return 0;
}
```

**编译 & 执行C程序**  

- 在终端中输入 `gcc hello.c` 进行代码编译
- 编译成功后，会生成 `hello.out` 可执行文件
- 执行 `hello.out` 会在屏幕上输出 `Hello World!`

```shell
$ gcc hello.c
$ ./hello.out
Hello World!
```

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

# 三、常量与变量

## 3.1、常量

## 3.2、变量

# 运算符

# 程序结构语句

# 函数

# 数组

# 指针

# 结构体、共用体和枚举类型

# 位运算