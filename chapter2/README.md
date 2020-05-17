# 信息的表示和处理
## 数字表示的基础
- 无符号编码
- 补码编码
- 浮点数编码 
在计算机中，是用有限数量的bit来表示一个数字，所以用不同的bit数有划分为了各种类型，比如char(byte),short,int,long等，每种类型所占用的字节数是不一样的。比如在C语言中，如果想表示一个小于128的整数，则其实使用char类型就足够了，因为char类型占用大小是一个字节（byte），其能表示的整数范围是[-128,127] 
下面表格列举了C语言不同数据类型的取值范围：   

-32位                
C数据类型 | MIN | MAX 
:-: | :-: | :-: 
char | xx | xx     
unsigned char | xx| xx         
long | -2 147 483 648 | 2 147 483 647    
unsigned long | 0 | 4 294 967 295     

-64位 
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

```
为什么会这样？？ 

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
-32位                
C数据类型 | MIN | MAX 
:-: | :-: | :-: 
char | xx | xx     
unsigned char | xx| xx         
long | -2 147 483 648 | 2 147 483 647    
unsigned long | 0 | 4 294 967 295     

-64位   
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
下面我们就来做一次老生常谈，婆婆妈妈的透析，让您一次过瘾，一次学个透；            

## 2.2整数表示

## 2.3整数运算

## 2.4浮点数
