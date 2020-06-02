# 信息的表示和处理
## 数字表示的基础
- 无符号编码
- 补码编码
- 浮点数编码 
在计算机中，是用有限数量的bit来表示一个数字，所以用不同的bit数有划分为了各种类型，比如char(byte),short,int,long等，每种类型所占用的字节数是不一样的。比如在C语言中，如果想表示一个小于128的整数，则其实使用char类型就足够了，因为char类型占用大小是一个字节（byte），其能表示的整数范围是[-128,127] 
下面表格列举了C语言不同数据类型的取值范围：   

- 32位 

C数据类型 | MIN | MAX 
:-: | :-: | :-: 
char | xx | xx     
unsigned char | xx| xx         
long | -2 147 483 648 | 2 147 483 647    
unsigned long | 0 | 4 294 967 295     

- 64位 

C数据类型 | MIN | MAX 
:-: | :-: | :-: 
char | xx | xx 
unsigned char | xx| xx         
long |-9 223 372 036 854 775 808 | 9 223 372 036 854 775 807    
unsigned long | 0 | 18 446 744 073 709 551 615     


我们看到在64位系统上，无符号的long的最大值是18 446 744 073 709 551 615 ，即1844亿亿 

下面我们来测试一个小例子
```
#include<stdio.h>
#include<limits.h>
void main(){
    unsigned  long a = 200 * 300 * 400 * 500;// 12 000 000 000（120亿）
    printf("%lu\n",a);
}    

[root@jordy ~]# gcc -o /tmp/limit /tmp/limit.c 
/tmp/limit.c: In function ‘main’:
/tmp/limit.c:4:42: warning: integer overflow in expression [-Woverflow]
   unsigned long long a = 200 * 300 * 400 * 500;
                                          ^
[root@jordy ~]# 
[root@jordy ~]# /tmp/limit 
18446744072824649728
或者提示
Overflow in expression; result is -884901888 with type 'int' [-Winteger-overflow]

```
为什么会这样？？      
https://www.microchip.com/forums/m501193.aspx    
https://blog.csdn.net/qq_30290513/article/details/77948008    



```
➜  /tmp cat /tmp/ulong.c
#include<stdio.h>
#include<limits.h>
int main(){
	printf("%d\n",(int)sizeof(long));
	printf("%d\n",(int)sizeof(long long));
	printf("%d\n",(int)sizeof(unsigned long long));
}


➜  /tmp gcc -o ulong /tmp/ulong.c
➜  /tmp
➜  /tmp ./ulong
8
8
8
```

```

➜  tmp cat char.c
#include<stdio.h>
int main(){
	char i = 128;
	printf("%d\n",i);
}
➜  tmp
➜  tmp gcc -o c char.c
char.c:3:11: warning: implicit conversion from 'int' to 'char' changes value from 128 to -128 [-Wconstant-conversion]
        char i = 128;
             ~   ^~~
1 warning generated.


[root@jordy ~]# gcc -o c /tmp/c.c
[root@jordy ~]# 
[root@jordy ~]# cat /tmp/c.c
#include<stdio.h>
int main(){
    char i = 128;
    printf("%d\n",i);
}
[root@jordy ~]# 
[root@jordy ~]# gcc -o c /tmp/c.c
[root@jordy ~]# 
[root@jordy ~]# 
[root@jordy ~]# ./c
-128

对于有符号的char[-128,127]：   
mac上给赋值128，编译或报错的时候就直接提示越界了，  
但是linux下可以输出结果，本质也是越界了，所以输出-128
```
我们再来一起看看 linux下的/usr/include/limits.h中的定义
完整代码见 [limits-h.md](https://github.com/jordy1024/csapp-study/blob/master/chapter2/limits-h.md)

```
#include <bits/wordsize.h>

/* We don't have #include_next.
   Define ANSI <limits.h> for standard 32-bit words.  */

/* These assume 8-bit `char's, 16-bit `short int's,
   and 32-bit `int's and `long int's.  */

/* Number of bits in a `char'.  */
#  define CHAR_BIT  8

/* Minimum and maximum values a `signed char' can hold.  */
#  define SCHAR_MIN (-128)
#  define SCHAR_MAX 127

/* Maximum value an `unsigned char' can hold.  (Minimum is 0.)  */
#  define UCHAR_MAX 255

/* Minimum and maximum values a `char' can hold.  */
#  ifdef __CHAR_UNSIGNED__
#   define CHAR_MIN 0
#   define CHAR_MAX UCHAR_MAX
#  else
#   define CHAR_MIN SCHAR_MIN
#   define CHAR_MAX SCHAR_MAX
#  endif

``` 


## 2.1信息存储
### 2.1.2 字（word）
通俗地讲，`字`是计算机中一个计量单位，用来表示数据块大小的计量单位。人们为了方便，将这一计量单位的大小简称为`字长`，那到底这个字长是干嘛的？到底多长呢？
下面我们先来了解下，人类定义这个单位的用途何在。所谓`字`，是指机器能直接处理的二进制数据的位数，也可以更狭义地理解为CPU同时处理的二进制数位数的能力,能同时处理8位二进制数数据的CPU叫8位CPU,类推,能同时处理64位二进制数数据的CPU叫64位CPU，机器字长一般等于内部寄存器的大小。

```
[root@jordy ~]# getconf WORD_BIT
32
[root@jordy ~]# 
[root@jordy ~]# 
[root@jordy ~]# getconf  LONG_BIT
64
[root@jordy ~]# 
[root@jordy ~]# 
[root@jordy ~]#  uname -r
3.10.0-957.21.3.el7.x86_64
```
[root@jordy ~]# vim /usr/include/limits.h
```
 86 /* Minimum and maximum values a `signed long int' can hold.  */
 87 #  if __WORDSIZE == 64
 88 #   define LONG_MAX 9223372036854775807L
 89 #  else
 90 #   define LONG_MAX 2147483647L
 91 #  endif
 92 #  define LONG_MIN  (-LONG_MAX - 1L)
 93 
 94 /* Maximum value an `unsigned long int' can hold.  (Minimum is 0.)  */
 95 #  if __WORDSIZE == 64
 96 #   define ULONG_MAX    18446744073709551615UL
 97 #  else
 98 #   define ULONG_MAX    4294967295UL
 99 #  endif
```
```
#include<stdio.h>
#include<limits.h>
void main(){
    printf("%lu,%lu\n",LONG_MIN,ULONG_MAX);

    printf("%ld,%ld\n",LONG_MIN,LONG_MAX);
}

[root@jordy ~]# ./limit 
9223372036854775808,18446744073709551615
-9223372036854775808,9223372036854775807
```
- 32位

C数据类型 | MIN | MAX 
:-: | :-: | :-: 
char | xx | xx     
unsigned char | xx| xx         
long | -2 147 483 648 | 2 147 483 647    
unsigned long | 0 | 4 294 967 295     

- 64位  

C数据类型 | MIN | MAX 
:-: | :-: | :-: 
char | xx | xx 
unsigned char | xx| xx         
long |-9 223 372 036 854 775 808 | 9 223 372 036 854 775 807    
unsigned long | 0 | 18 446 744 073 709 551 615  

这里我们看到，当前我自己的机器输出的limit大小符合64位系统的特征，所以猜想我的系统是64位的系统.等下，这里是指机器是64位还是说系统是64位？
哦，这个……这个我也有点模糊……； 哦……哦……不行，这个必须不能模糊。
截至目前，我们已经接触了非常多的概念，字，字长，32/64位机器（CPU），32/64位操作系统。   
等下，也许有人已经懵了，通常我们所说的32位，64位，到底是在说什么呀？如何判断一个计算机的硬件（即机器位数或CPU位数）是32位还是64位？    
又如何判断一个计算机的操作系统是32位还是64位呢？     
感觉必须要搞清楚这两者，不然n年之后还是很痛苦，因为一直模模糊糊，似懂非懂的状态。     
下面我们就来做一次老生常谈的透析，让您一次过瘾，一次学个透；  

### 2.1.4多字节对象内存编址与字节排列顺序透析
```
#include<stdio.h>
typedef unsigned char *byte_pointer;

void show_bytes(byte_pointer start ,int len) {
    int i;
    for(i=0;i<len;i++){
        printf("%.2x",*(start + i));//*(start + i) 等于 start[i]
    }
    printf("\n");
}
void show_int(int x){
    show_bytes((byte_pointer)&x,sizeof(int));
}

void show_float(float x){
    show_bytes((byte_pointer)&x,sizeof(float));
}

void show_pointer(void *x){
    show_bytes((byte_pointer)&x,sizeof(void *));
}

int main(){
    int a = 5;
    show_int(a);
    float b = 40.2;
    show_float(b);
    int *p = &a;
    show_pointer(p);
}
```
要深入掌握以上的这个经典程序，首先我们来回忆和学习一下C语言的经典知识；   
- 指针与地址
- 多字节变量在内存中的存储

有些类型的变量在内存中是占用1个以上的字节，即多字节，比如咱们常用的int类型（就以C语言的int类型为例吧）。  
int类型在内存中占用4个字节（即32个比特位），那这4个字节是如何排列的呢？这就涉及到一个字节顺序的问题了。   
在计算机中，不同的硬件或策略决定了在内存上存储这个变量时的字节顺序，我们来举个int的实例。
比如有一个变量int a = x ; 
那么它在内存中存储时，这个变量的值就占4个字节，对应的二进制位就是32位，例如：
[x<sub>31</sub>,x<sub>30</sub>,x<sub>29</sub>,……x<sub>28</sub>]   
我看到它的最高位的字节对应的8个位分别是[x<sub>31</sub>,x<sub>30</sub>,x<sub>29</sub>,x<sub>28</sub>,x<sub>27</sub>,x<sub>26</sub>,x<sub>25</sub>,x<sub>24</sub>]    
它的最低位的字节对应的8个位分别是[x<sub>7</sub>,x<sub>6</sub>,x<sub>5</sub>,x<sub>4</sub>,x<sub>3</sub>,x<sub>2</sub>,x<sub>1</sub>,x<sub>0</sub>]
在有些机器上，存储这个变量的时候，是以高位字节到低位字节的顺序存储的。
而在另外一些机器上，存储这个变量的时候，是以低位字节到高位字节的顺序存储的。  
这就是内存中变量存储的字节顺序的本质。至于哪个叫大端法，哪个叫小端法，如果容易混淆，可以不用过度纠结，重在理解原理。    
一句话概述：因为不同机器存储数据的字节顺序不同，所以在某些场景下，尤其开发网络应用，就需要可以考虑这个。   

- sizeof

在C语言中，我们可以通过sizeof关键字来动态的获取某种数据类型在内存中所占用的字节数目，如：
```
int bytes_num_of_int_type = sizeof(int);
float bytes_num_of_float_type = sizeof(float); 
```

- typedef语法糖

在C语言中，我们可以用关键字typedef将自建类型或自定义类型重定义为我们自己命名的类型，达到语义上的清晰，举例：
```
typedef INT_POINTER int;
typedef ONLINE_PEOPLE struct OnlinePeople;
struct OnlinePeople{
	char *name;
	int age;
	int online_status;
}
```

有了以上的基础知识做铺垫后，下面我们来深入分析下show_bytes的例程；   
- 一句话功能概述show_bytes程序的功能 

show_bytes可以打印出任何类型的变量在内存中的字节表示（为了方便人类查看，这里是用16进制来表示的，注意，2个16进制字符表示一个字节，即8个bit位）


- 为什么要转换为char*

```
首先声明清楚下，在C语言中，char类型可以用来存储单个字符，因为单个字符刚好占用一个字节；所以char类型也可以用来定义单个字节的整型变量，如
//定义一个有符号char变量；取值范围为[-128,127]
char int_var1 = 127;
//定义一个无符号char变量；取值范围为[0,255]
unsigned char int_var2 = 255;
//说实话，C语言虽然不像Java或Go等语言，用额外的一个关键字byte来声明字节变量或数组，而是直接很省事的用char来表示了占用一个字节的变量；
//但对于初学者而已，其实很容易搞混或造成难以理解；就如上面的举例中，好多C语言的教材中会将，可以将单个字符存储在char类型的变量中；
//也可以将单字节的整数存储在char类型的变量中，对于一个初学计算机或编程的人来讲，就会很迷惑，因可以的去区分char到底是字符类型还是整型；
//因为初学计算机的新手，也许刚好是一位还未来得及了解计算机发展历史、CS课程、以及ASCII编码等知识的人。
//这是个人观点，仅供参考！ 
因此这里强制转换的目的在于：无论&x以前是什么类型，这里统统转换为char *类型，这样的话，在循环遍历的时候，指针每加1，就表示移动一个字节长度的地址。   
从而实现了逐个打印出该变量的每个字节的目的。额外记着，上述例子中start[i]等价于*(start + i)，
这是c语言的基础，可以额外补充性学习C语言中数组名称的含义。

```
- show_bytes的程序中，我们使用sizeof的目的和意义。

因为我们的目的是以字节为单位来遍历性的输出变量的值，所以在打印变量的每个字节前，我们必须要知道这个变量在内存中所占用的字节；
而每个变量都是对应一个类型的，如整型，浮点型，指针类型void * 等，所以我们就可以用sizeof(type T)来计算出某变量的字节数；  

经过上述的铺垫和深入学习，我相信咱门已经彻底理解了show_bytes程序，下面让我们用一个例子实际的运行一下该程序，如
```
#include<stdio.h>
typedef char* byte_pointer;

void show_bytes(byte_pointer obj,int byte_num){
        int i;
        for(i=0;i<byte_num;i++){
            printf("%.2x",obj[i]);
        }
        printf("\n");
}
void show_int(int a){
    show_bytes((byte_pointer)&a,sizeof(int));
}

void show_float(float f){
    show_bytes((byte_pointer)&f,sizeof(float));
}

void show_pointer(void * p){
    show_bytes((byte_pointer)&p,sizeof(void *));
}
int main(){
    int a = 15;
    show_int(a);

    float b = (float)a;
    show_float(b);

    int *p = &a;
    show_pointer(p);
}

 gcc -o /tmp/show_byte  /tmp/show_bytes.c
 
 [root@jordy tmp]# /tmp/show_byte 
0f000000
00007041
fffffffc50ffffff88ffffff92fffffffd7f0000

```
下面让我们来分析以下以上输出结果：


```go
/*
0f   00  00  00 -> 00 00 00 0f 
00  00  70  41  -> 41 70 00 00   


00000000 00000000 00000000  00001111
0100 0001 01110000   00000000  00000000 


在语言层面，可以很方便开发者玩转二进制的语言中，go语言的binary包提供的功能相当实用，然后开启愉快的钻研之旅吧。
https://golang.org/pkg/encoding/binary 
https://golang.org/pkg/encoding/binary/#ByteOrder 
A ByteOrder specifies how to convert byte sequences into 16-, 32-, or 64-bit unsigned integers.
*/

type ByteOrder interface {
    Uint16([]byte) uint16
    Uint32([]byte) uint32
    Uint64([]byte) uint64
    PutUint16([]byte, uint16)
    PutUint32([]byte, uint32)
    PutUint64([]byte, uint64)
    String() string
}

Example(Get)

package main

import (
	"encoding/binary"
	"fmt"
)

func main() {
	b := []byte{0xe8, 0x03, 0xd0, 0x07}
	x1 := binary.LittleEndian.Uint16(b[0:])
	x2 := binary.LittleEndian.Uint16(b[2:])
	fmt.Printf("%#04x %#04x\n", x1, x2)
}

//Example(Put) 

package main

import (
	"encoding/binary"
	"fmt"
)

func main() {
	b := make([]byte, 4)
	binary.LittleEndian.PutUint16(b[0:], 0x03e8)
	binary.LittleEndian.PutUint16(b[2:], 0x07d0)
	fmt.Printf("% x\n", b)
}


package main

import (
    "encoding/binary"
    "fmt"
)

func main() {
    b := []byte{0xe8, 0x03, 0xd0, 0x07}
    x1 := binary.LittleEndian.Uint16(b[0:])
    x2 := binary.LittleEndian.Uint16(b[2:])
    littleEndian := binary.LittleEndian.Uint32(b[0:])
    bigEndian := binary.BigEndian.Uint32(b[0:])
    fmt.Printf("%#04x %#04x %#08x %#08x\n", x1, x2, littleEndian, bigEndian)
}

```
OK，下面我们就写一个完整的例子：   
```c
#include<stdio.h>
#include<string.h>
typedef char* byte_pointer;
void show_bytes(byte_pointer start,int byte_num){
    int i;
    for(i=0;i<byte_num;i++){
        printf("%.2x",*(start + i));
    }
    printf("\n");
}

void show_int(int x){
    show_bytes((byte_pointer)&x,sizeof(int));
}
void show_float(float y){
    show_bytes((byte_pointer)&y,sizeof(float));
}
void show_pointer(void * p){
    show_bytes((byte_pointer)&p,sizeof(void *));
}

void show_str(char * s){
    show_bytes((byte_pointer)s,strlen(s));
}

void test_show_bytes(int x){
    show_int(x);
    show_float((float)x);
    show_pointer(&x);
}

void test_string_show_bytes(char * s){
    show_str(s);
}

int main(){
    test_show_bytes(12345);

    char *s = "ABCDE";
    test_string_show_bytes(s);
}

```

我们看到，上面分别对多种类型对变量做了字节透析，类型包括整型、整形指针、浮点型、字符串。  


### 一些额外的参考资料
[https://www.jianshu.com/p/e140dedb6422](https://www.jianshu.com/p/e140dedb6422)    
[https://songlee24.github.io/2015/05/02/endianess/](https://songlee24.github.io/2015/05/02/endianess/)

### 2.1.5 表示字符串
- ASCII编码 

man ascii（有些机器可能没有安装）

- 什么是二进制安全

主要是针对字符串处理函数而已

举个Redis的字符串的例子

C语言字符串 VS Redis字符串 


### 2.1.6 表示代码

### 2.1.7 布尔代数和环

### 2.1.8 C位级运算

- 练习题2.11

因为位运算的高效性，在日程开发中，经常会用到以掩码的方式生成我们想要的目标值，此练习是一个经典的例子之一，应该深刻掌握。
0x98FDECBA -> 0xFFFFFFBA

源 | 运算 | 目标
---|---|---
0x98FDECBA  | \|0xFFFFFF00| 0xFFFFFFBA


```c
#include<stdio.h>
int main(){
    int a = 0x98FDECBA;
    int res = (a | 0xffffff00);
    printf("%x\n",res);

    int res2 = (a & 0xffffff00)  + ~(a & 0xFF);
    printf("%x\n",res2);

    int res3 = (a & 0xffffff00);
    printf("%x\n",res3);
}
```

### 2.1.9 C中的逻辑运算

### 2.1.10 C中的位级运算  



## 2.2整数表示

### 2.2.1 认识整数  
在计算机中，我们用不同字节长度的位来表示不同范围的整数值。所以，如果待声明的变量的大小范围是确定，则尽量使用占用字节数最少的范围类型，以节省内存。     
比如在C语言中，char 整型占1个字节，short占2个字节，以此类推。所以能用char的场景中则尽量别用short。能用short的场景中则尽量别用int，……。

64位机器上C语言的整型数据类型的典型取值范围
C数据类型 | 最小值 | 最大值
---|---|---
char  | -128| 127
unsigned char | 0 | 255
short [int] | -32768 | 32767
unsigned short [int] | 0 | 65535
int | -2147483648 | 2147483647
unsigned[int] | 0 | 4294967295
long[int] | |   
unsigned long [int] | | 
long long [int] | |
unsigned long long[int] | | 

- 对比Java的整型

Java 除了没有long long 型外，其他的跟64位环境下C整型的范围是一模一样的，如：
Java整型类型 | 最小值 | 最大值
---|---|---
byte | | 
short | | 
int | |
long | |

### 2.2.2无符号数

### 2.2.3有符号数
- stdint.h（ISO C99标准引入的）明确定义了位长和有/无符号     

代码见[stdint.h](https://github.com/jordy1024/csapp-study/blob/master/chapter2/stdint-h.md)

go语言的整型

- 练习2.18  

十六进制|二进制|十进制
---|---|---
0x1b8| 00011 0111000| 
 | | 
 | | 
 | | 
 | | 
 | | 
 | | 
 | | 
 | | 
 | | 

### 2.2.4 有符号数和无符号数之间的转换！！



## 2.3整数运算

## 2.4浮点数
- 遗留问题，计算机中是如何表示负的小数的？？

- 

# 本章概要总结

- 信息在计算机中表示的本质：二进制位！
- 人类约定的惯用二进制位长度：8位，也叫一个字节。
- 整数在计算机中的2种常用编码：无符号编码与有符号编码（补码）
- 浮点数在计算机中的表示原理 
- 

如果您没有读懂，也许不是您的问题，可能是我没有表述清楚。   
欢迎留言与讨论:  jordy1024@163.com  
