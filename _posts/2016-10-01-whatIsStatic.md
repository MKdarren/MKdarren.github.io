---
layout: post
title:  "C++ static的作用"
date:   2016-10-01 18:25:01 +0800
tag: C++
---

### static的作用
（1）先来介绍它的第一条也是最重要的一条：隐藏。

当我们同时编译多个文件时，所有未加static前缀的全局变量和函数都具有全局可见性。为理解这句话，我举例来说明。我们要同时编译两个源文件，一个是a.c，另一个是main.c。

下面是a.c的内容
```cpp
char a = 'A'; // global variable
void msg()
{
    printf("Hello\n");
}
```
下面是main.c的内容
```cpp
int main(void)
{
    extern char a;    // extern variable must be declared before use
    printf("%c ", a);
    (void)msg();
    return 0;
}
```
程序的运行结果是：

A Hello

你可能会问：为什么在a.c中定义的全局变量a和函数msg能在main.c中使用？前面说过，所有未加static前缀的全局变量和函数都具有全局可见性，其它的源文件也能访问。此例中，a是全局变量，msg是函数，并且都没有加static前缀，因此对于另外的源文件main.c是可见的。

如果加了static，就会对其它源文件隐藏。例如在a和msg的定义前加上static，main.c就看不到它们了。利用这一特性可以在不同的文件中定义同名函数和同名变量，而不必担心命名冲突。Static可以用作函数和变量的前缀，对于函数来讲，static的作用仅限于隐藏，而对于变量，static还有下面两个作用。

（2）static的第二个作用是保持变量内容的持久。存储在静态数据区的变量会在程序刚开始运行时就完成初始化，也是唯一的一次初始化。共有两种变量存储在静态存储区：全局变量和static变量，只不过和全局变量比起来，static可以控制变量的可见范围，说到底static还是用来隐藏的。虽然这种用法不常见，但我还是举一个例子。
```cpp
#include <stdio.h>

int fun(void){
    static int count = 10;    // 事实上此赋值语句从来没有执行过
    return count--;
}

int count = 1;

int main(void)
{
    printf("global\t\tlocal static\n");
    for(; count <= 10; ++count)
        printf("%d\t\t%d\n", count, fun());

    return 0;
}
```
程序的运行结果是：

global          local static

1               10

2               9

3               8

4               7

5               6

6               5

7               4

8               3

9               2

10              1


（3）static的第三个作用是默认初始化为0。其实全局变量也具备这一属性，因为全局变量也存储在静态数据区。在静态数据区，内存中所有的字节默认值都是0x00，某些时候这一特点可以减少程序员的工作量。比如初始化一个稀疏矩阵，我们可以一个一个地把所有元素都置0，然后把不是0的几个元素赋值。如果定义成静态的，就省去了一开始置0的操作。再比如要把一个字符数组当字符串来用，但又觉得每次在字符数组末尾加’\0’太麻烦。如果把字符串定义成静态的，就省去了这个麻烦，因为那里本来就是’\0’。不妨做个小实验验证一下。
```cpp
#include <stdio.h>

int a;

int main(void)
{
    int i;
    static char str[10];

    printf("integer: %d;  string: (begin)%s(end)", a, str);

    return 0;
}
```
程序的运行结果如下
integer: 0; string: (begin)(end)

最后对static的三条作用做一句话总结。首先static的最主要功能是隐藏，其次因为static变量存放在静态存储区，所以它具备持久性和默认值0。
