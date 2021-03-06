本书精髓：旨在帮助读者了解，当在系统上执行以下hello程序时，系统发生了什么，以及为什么。         
```      
#include<stdio.h>    
int main(){
    printf("hello,world\n");
}
```


## 1.1一切都是二进制    
在计算机中，要表示宇宙之一切信息，其本质上都是用二进制位，如10101011；      
举个例子，假如我们编写了一个用来打印**hello world**的C语言源程序，然后将这个源程序保存到hello.c文件中。    
这样，该文件就被保存到了计算机的硬盘上，其中的的每个字符，在底层都是用二进制来表示和存储的。        
下面，就让我们一起来深入了解下，文件中的内容在计算机中是如何被表示或存储的。           
## /root/hello.c  
```
#include<stdio.h>
void main(){
    printf("hello world\n");
}
``` 
## 查看hello.c源码文件二进制表示   
在Linux系统下，可以直接使用xxd工具来查看某文件的内容，默认打开后是16进制的。      
```             
[root@jordy ~]# xxd hello.c
0000000: 2369 6e63 6c75 6465 3c73 7464 696f 2e68  #include<stdio.h
0000010: 3e0a 696e 7420 6d61 696e 2876 6f69 6429  >.int main(void)
0000020: 7b0a 0909 7072 696e 7466 2822 6865 6c6c  {...printf("hell
0000030: 6f20 776f 726c 645c 6e22 293b 0a7d 0a    o world\n");.}.
[root@jordy ~]# xxd -b hello.c
0000000: 00100011 01101001 01101110 01100011 01101100 01110101  #inclu
0000006: 01100100 01100101 00111100 01110011 01110100 01100100  de<std
000000c: 01101001 01101111 00101110 01101000 00111110 00001010  io.h>.
0000012: 01101001 01101110 01110100 00100000 01101101 01100001  int ma
0000018: 01101001 01101110 00101000 01110110 01101111 01101001  in(voi
000001e: 01100100 00101001 01111011 00001010 00001001 00001001  d){...
0000024: 01110000 01110010 01101001 01101110 01110100 01100110  printf
000002a: 00101000 00100010 01101000 01100101 01101100 01101100  ("hell
0000030: 01101111 00100000 01110111 01101111 01110010 01101100  o worl
0000036: 01100100 01011100 01101110 00100010 00101001 00111011  d\n");
000003c: 00001010 01111101 00001010                             .}.   

```
通过以上输出，我们清楚地看到，我们编写的源代码源代码文件本质上也是以一系列的二进制序列被存储起来的。        
比如第一个字符#，它对应的ASCII码值的十进制是35，转换为16进制就是23，其对应的二进制是00100011，即表示第一个字符#        
同样地，第二个字节是69，我们算得它对应的十进制是（6 * 16 + 9 ）105，查看ASCII码表，105表示的是字符i，以此类推。        
下面我们自己手动来实现另外一个工具类程序，该程序的功能是读取/root/hello.c源文件的内容，        
并按不同的格式输出，如二进制，十六进制，ASCII十进制等等            
```        
package main    
     
import (    
    "fmt"
    "io/ioutil"
    "log"
    "os"
)

func main() {
    file, err := os.Open("/root/hello.c")
    if err != nil {
        log.Fatal(err)
    }
    data, err := ioutil.ReadAll(file)
    if err != nil {
        log.Fatal(err)
    }
    dataByte := []byte(data)
    length := len(dataByte)
    for i := 0; i < length; i++ {
        fmt.Printf("%8x", dataByte[i])
        fmt.Printf(" ")
        if (i+1)%16 == 0 {
            fmt.Printf("\n")
        }
    }
    fmt.Println("\n")
    for i := 0; i < length; i++ {
        fmt.Printf("%8b", dataByte[i])
        fmt.Printf(" ")
        if (i+1)%16 == 0 {
            fmt.Printf("\n")
        }
    }
    fmt.Println("\n")
    for i := 0; i < length; i++ {
        fmt.Printf("%8v", dataByte[i])
        fmt.Printf(" ")
        if (i+1)%16 == 0 {
            fmt.Printf("\n")
        }
    }

    fmt.Println("\n")
}

```
运行该程序
```
[root@jordy ~]# go run show_bytes.go
```
得到如下结果：
```
[root@jordy ~]# go run show_bytes.go 
      23       69       6e       63       6c       75       64       65       3c       73       74       64       69       6f       2e       68 
      3e        a       69       6e       74       20       6d       61       69       6e       28       76       6f       69       64       29 
      7b        a        9        9       70       72       69       6e       74       66       28       22       68       65       6c       6c 
      6f       20       77       6f       72       6c       64       5c       6e       22       29       3b        a       7d        a 

  100011  1101001  1101110  1100011  1101100  1110101  1100100  1100101   111100  1110011  1110100  1100100  1101001  1101111   101110  1101000 
  111110     1010  1101001  1101110  1110100   100000  1101101  1100001  1101001  1101110   101000  1110110  1101111  1101001  1100100   101001 
 1111011     1010     1001     1001  1110000  1110010  1101001  1101110  1110100  1100110   101000   100010  1101000  1100101  1101100  1101100 
 1101111   100000  1110111  1101111  1110010  1101100  1100100  1011100  1101110   100010   101001   111011     1010  1111101     1010 

      35      105      110       99      108      117      100      101       60      115      116      100      105      111       46      104 
      62       10      105      110      116       32      109       97      105      110       40      118      111      105      100       41 
     123       10        9        9      112      114      105      110      116      102       40       34      104      101      108      108 
     111       32      119      111      114      108      100       92      110       34       41       59       10      125       10 

```
我们清晰地看到，我们手动写程序打印的hello.c这个文件的二进制表示，跟上述直接用xxd工具生成的二进制序列是一模一样的。  
换句话说，假如换一个其他文件，比如：hello.txt，或者一个图片文件hello.png，或者一个视频文件hello.mp4等等，本质均如此。    
通过以上的分析和学习，我们建立了一个初步的印象：在宇宙万物中，一切事物都可以用阴和阳来表示；    
同样地，在计算机中，一切信息都可以用0 和 1来表示，其本质均是二进制位(bit)。

## 1.2   
在上小节中，我们通过工具和自定义程序简单的分析了一个源代码文件中的信息（即每一行源代码）在计算机中是如何被表示和存储的。   
仅知道了这个，一切还远未开始。试想，我们辛苦编写一个源程序，其目的肯定并不是让它永久的呆在硬盘上，我们编写它的目的是为了    
让源程序最终能运行起来，然后让程序我们人类做事情。
当然现在提`运行`二字为时还过早，本小节我们先来概括性的了解下，在让计算机`运行`我们的程序指令前，如何先让计算机`认识`我们编写的指令。    
我们的hello.c程序编写完毕了，通过学习我们也知道了这个程序在计算机上其实是以二进制的形式被表示和保存的。  
但是现在如何让计算机执行这个程序，貌似计算机根本就不认识我们写的什么呀。比如第一行中的`#include`，我们人类知道，这是我们发明的
C语言中包含头文件的一行程序，但计算机才不关心这是C语言还是D语言呢，也不认识#include是干嘛的呢。计算机只能认识0,1位，用我们宇宙语  
来表示，也可以理解为阴阳，宇宙的一切都是由阴和阳构成。既然计算机只认识0和1，那我们就得想办法让计算机最终`运行`由0和1组成的另一种特殊   
的可运行文件，也叫可执行文件，我们暂且这么理解。但这个文件目前并不存在呀，也不会凭空产生，怎么办？于是，人类编写完了源程序的同时就  
得想办法把自己编写的源代码转换为（或者叫翻译为）计算机能理解和执行的代码，人类给这一过程起了个名字，就暂且统称为“编译”吧。  
并试图想办法发明一种能实现这一转换的工具，我们就暂且可称该工具为编译程序或编译器吧。   
随你怎么叫，叫翻译器，转换器等等，都可以奥。比如在类unix系统下，我们常使用的gcc工具，就是人类发明的一个伟大大编译器工具，    
用它可以把我们刚才编写的hello.c源程序转换为计算机可以“认识”并执行的一种特殊的可执行程序。让我们先来迫不及待的尝试一把吧，^_^          
```    
[root@jordy ~]# pwd   
/root    
[root@jordy ~]# gcc -o hello /root/hello.c    
[root@jordy ~]#     
[root@jordy ~]# ls -al ./hello
-rwxr-xr-x 1 root root 8480 May 16 22:00 ./hello   
```      
看到在当前目录下，已经生产了一个可执行的文件hello，我们先了运行一下，让它帮我们打印出**hello world**  
```
[root@jordy ~]# ./hello
hello world 
```    
嗯，这一切看起来很简单呀，用一个-o参数，一步就搞定了。但真相真是如此吗？其实看似简单的背后，其实包含了好几个连续的步骤。人类将这几个步骤简单的划分为了4步，并对每个步骤进行了命名，它们分别是：预处理（Preprocess）、编译（compile）、汇编（assemble）、链接（link）。   
首先让我们来简单了解下编译器gcc的使用帮助文档：      
```
[root@jordy ~]# gcc --help
Usage: gcc [options] file...
Options:
  -pass-exit-codes         Exit with highest error code from a phase
  --help                   Display this information
  --target-help            Display target specific command line options
  --help={common|optimizers|params|target|warnings|[^]{joined|separate|undocumented}}[,...]
                           Display specific types of command line options
  (Use '-v --help' to display command line options of sub-processes)
  --version                Display compiler version information
  -dumpspecs               Display all of the built in spec strings
  -dumpversion             Display the version of the compiler
  -dumpmachine             Display the compiler's target processor
  -print-search-dirs       Display the directories in the compiler's search path
  -print-libgcc-file-name  Display the name of the compiler's companion library
  -print-file-name=<lib>   Display the full path to library <lib>
  -print-prog-name=<prog>  Display the full path to compiler component <prog>
  -print-multiarch         Display the target's normalized GNU triplet, used as
                           a component in the library path
  -print-multi-directory   Display the root directory for versions of libgcc
  -print-multi-lib         Display the mapping between command line options and
                           multiple library search directories
  -print-multi-os-directory Display the relative path to OS libraries
  -print-sysroot           Display the target libraries directory
  -print-sysroot-headers-suffix Display the sysroot suffix used to find headers
  -Wa,<options>            Pass comma-separated <options> on to the assembler
  -Wp,<options>            Pass comma-separated <options> on to the preprocessor
  -Wl,<options>            Pass comma-separated <options> on to the linker
  -Xassembler <arg>        Pass <arg> on to the assembler
  -Xpreprocessor <arg>     Pass <arg> on to the preprocessor
  -Xlinker <arg>           Pass <arg> on to the linker
  -save-temps              Do not delete intermediate files
  -save-temps=<arg>        Do not delete intermediate files
  -no-canonical-prefixes   Do not canonicalize paths when building relative
                           prefixes to other gcc components
  -pipe                    Use pipes rather than intermediate files
  -time                    Time the execution of each subprocess
  -specs=<file>            Override built-in specs with the contents of <file>
  -std=<standard>          Assume that the input sources are for <standard>
  --sysroot=<directory>    Use <directory> as the root directory for headers
                           and libraries
  -B <directory>           Add <directory> to the compiler's search paths
  -v                       Display the programs invoked by the compiler
  -###                     Like -v but options quoted and commands not executed
  -E                       Preprocess only; do not compile, assemble or link
  -S                       Compile only; do not assemble or link
  -c                       Compile and assemble, but do not link
  -o <file>                Place the output into <file>
  -pie                     Create a position independent executable
  -shared                  Create a shared library
  -x <language>            Specify the language of the following input files
                           Permissible languages include: c c++ assembler none
                           'none' means revert to the default behavior of
                           guessing the language based on the file's extension

Options starting with -g, -f, -m, -O, -W, or --param are automatically
 passed on to the various sub-processes invoked by gcc.  In order to pass
 other options on to these processes the -W<letter> options must be used.

```

- Preprocess  
-E                       Preprocess only; do not compile, assemble or link
gcc -E  hello.c -o hello.i
```
  1 # 1 "hello.c"
  2 # 1 "<built-in>"
  3 # 1 "<command-line>"
  4 # 1 "/usr/include/stdc-predef.h" 1 3 4
  5 # 1 "<command-line>" 2
  6 # 1 "hello.c"
  7 # 1 "/usr/include/stdio.h" 1 3 4
  8 # 27 "/usr/include/stdio.h" 3 4
  9 # 1 "/usr/include/features.h" 1 3 4
 10 # 375 "/usr/include/features.h" 3 4
 11 # 1 "/usr/include/sys/cdefs.h" 1 3 4
 12 # 392 "/usr/include/sys/cdefs.h" 3 4
 13 # 1 "/usr/include/bits/wordsize.h" 1 3 4
 14 # 393 "/usr/include/sys/cdefs.h" 2 3 4
 15 # 376 "/usr/include/features.h" 2 3 4
 16 # 399 "/usr/include/features.h" 3 4
 17 # 1 "/usr/include/gnu/stubs.h" 1 3 4
 此处省略800多行………………
 837 # 2 "hello.c" 2
 838 int main(void){
 839   printf("hello world\n");
 840 }

```
- compile   
gcc -S  hello.c -o hello.s
```
1     .file   "hello.c"
  2     .section    .rodata
  3 .LC0:
  4     .string "hello world"
  5     .text
  6     .globl  main
  7     .type   main, @function
  8 main:
  9 .LFB0:
 10     .cfi_startproc
 11     pushq   %rbp
 12     .cfi_def_cfa_offset 16
 13     .cfi_offset 6, -16
 14     movq    %rsp, %rbp
 15     .cfi_def_cfa_register 6
 16     movl    $.LC0, %edi
 17     call    puts
 18     popq    %rbp
 19     .cfi_def_cfa 7, 8
 20     ret
 21     .cfi_endproc
 22 .LFE0:
 23     .size   main, .-main
 24     .ident  "GCC: (GNU) 4.8.5 20150623 (Red Hat 4.8.5-39)"
 25     .section    .note.GNU-stack,"",@progbits
```
- assemble  
-c                       Compile and assemble, but do not link
```
-c选项表示编译、汇编指定的源文件（也就是编译源文件），但是不进行链接。使用-c选项可以将每一个源文件编译成对应的目标文件
目标文件是一种中间文件或者临时文件，如果不设置该选项，gcc 一般不会保留目标文件，可执行文件生成完成后就自动删除了。
目前我们的源文件仅仅就一个，hello.c。所以生成的目标文件也仅仅就一个hello.o，如果一个项目非常庞大，在这一步就会生成无数的.o文件
```
- link
通过汇编阶段我们生成了n个源文件对应的n个.o目标文件，接下来让想让这n个程序真正组合起来，然后通过统一的入口main()函数执行。   
那么我们就需要将这n个.o文件链接起来

```
gcc -o hello hello.o
```
通过最后的link步骤，我们就得到了计算机可执行文件hello
让我们来运行可执行文件hello看一下输出结果：
```
[root@jordy ~]# ./hello
hello world
```

## 1.3 掌握编译器的工作原理、特性的意义何在？   
上面我们已经分步骤的完整透析学习了编译器的伟大之处，它可以将我们人类写的源代码 最终 转换为计算机可以执行的可执行文件，   
从而完成了从初心到梦想的跨越。这样看来，假如我们能更深入的了解编译器的工作过程、原理以及已经编译过程中编译器的优化行为，  
这样岂不能更好的帮助我们写出规范的、高效的源程序？何乐而不为？恩，的确如此，假如掌握了编译器的原理和特性，我们的确从以下    
多方面入手，写出更好的代码来。
- 尽力优化我们的程序指令，使其更加符合编译器的特性，从而是程序的运行效率达到相对最优。   
- 帮助我们快速定位在编译过程中出现的各种莫名其妙问题的原因，进而快速解决它们。   
- 帮助我们更深入的了解如何可以让我们的指令和数据变的更加安全。       


## 1.4 计算机硬件的组成  
计算机是人类在20世纪最伟大的发明之一。试着了解下它的任何一个零部件的工作原理（如处理器、内外部存储器、显示器等等），都会让你觉得不可思议，实在是伟大的优点过头了。     
### 1.4.1总线   
这里主要明白32和64位的来源：   
我们首先来学习下面的一段描述：    
贯穿整个系统的是一组电子管道，称做总线，它携带信息字节并负责在各个部件间传递。               
通常总线被设计成传送定长的字节块，也就是字（word）。字中的字节数（即字长）是一个基本的系统参数，在各个系统中的情况都不尽相同。      
现在的大多数机器字长有的是4个字节（32位），有的是8个字节（64位）。为了讨论的方便，假设字长为4个字节，并且总线每次只传送1个字。     
  

