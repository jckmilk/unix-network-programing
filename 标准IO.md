# 第五章 标准I/O 库

目的：方便用户使用而不必关心底层实现

## 5.1流和FILE对象

对于标准I/O库，它们围绕流进行。当标准I/O库打开或创建一个文件时，我们已使一个流与一个文件相关联。

流的定向决定了所读、写的字符是单字节还是多字节。当一个流最初被创建时，它没有定向。如若在未定向的流上使用一个多字节I/O函数(<wchar.h>),则将该流定向为宽定向。若在未定向的流上使用一个单字节I/O函数,则将该流定向为字节定向。

freopen函数清楚一个流的定向，fwide函数用于设置流的定向。
```
    #include<stdio.h>
    #include<wchar.h>
    
    int fwide(FILE *fp, int mode);
 ```
    
返回值：流宽向 返回正值，字节定向，返回负值，未定向 返回0；
**
fwide函数 并不改变已定向的流**

当打开一个流时，fopen返回一个指向FILE对象的指针。该对象通常是一个结构，包含了标准I/O库为管理该流所需要的所有信息，包括实际文件的描述符，指向用于该流缓冲区的指针、缓冲区长度、当前在缓冲区的字符数以及出错标志。

为了引用一个流，需要将file指针作为参数传递给每个I/O函数。


##5.2 标准输入、标准输出和标准错误

## 5.3缓冲
标准I/O库提供缓冲的目的是尽可能减少read和write的调用次数。它也对每个I/O流自动地进行缓冲管理。

标准I/O提供以下3中类型的缓冲。
1. 全缓冲。在填满标准I/O缓冲区后才开始实际I/O操作。**对于驻留在磁盘上的文件通常使用全缓存。在一个流上执行第一个I/O操作时，相关函数调用malloc获取需要使用的缓冲区**。
flush说明缓冲区的写操作，可以自动洗或者调用fflush重洗。
2. 行重洗。遇到换行符 执行I/O操作。这样 允许一个输入一个字符(fputc)，只有写满一行在进行实际I/O操作。当流涉及终端时(标准输入和标准输出），通常使用行缓冲。

很多系统默认以下类型的缓冲：
- 标准错误是不带缓冲的，立即显示
- 指向终端设备的是行缓冲；否则是全缓冲的。

下面两个函数改变缓冲类型。

    #include<stdio.h>
    
    void setbuf(FILE *restrict fp， char *restrict buf);
    
    int setvbuf(FILE *restrict fp， char *restrict buf， int mode， size_t size）；
    成功 ，返回0；若出错，返回非0.
    

buf指定一个长度为BUFFSIZE的缓冲区，通常在此之后该流是全缓冲的。 为了关闭缓冲 设置为NULL。

setVbuf精确说明，缓冲区的类型

- IOFBF  全缓冲
- IOLBF  行缓冲
- IONBF  不带缓冲

不带缓冲 忽略buf和size参数。
如果带缓冲 而buf为NULL，则标准I/O库将自动分配适当长度的缓冲区。适当长度指的是常量BUFSIZ所指定的值。


    #include<stdio.h>
    int fflush(FILE* fp);
    成功返回0；出错 返回EOF。


## 5.4打开流

下列三个函数打开一个标准I/O流。

    #include<stdio.h>
    FILE *fopen(const char *restrict pathname, const char *restrict type);
    
    FILE *freopen(cosnt char* restrict pathneme, const char *restrict type, 	FILE* restrict fp);
    FILE *fdopen(int fd, const char* type):
    
    若成功，返回文件指针；若出错，返回NULL;

1. fopen打开路径名为pathname的一个指定文件
2. freopen在一个指定的流上打开一个指定的文件，如果该流已经打开，则先关闭该流。若流已经定向，则使用freopen清楚该定向。此函数一般用于将一个指定文件大开卫预定义的流:标准输入、标准输出、标准错误。
3. fdopen函数取一个已有的描述符，并使一个标准的I/O流与该描述符结合。此函数通常用于创建管道和socket。这类文件不能使用fopen函数打开。

type参数指定了该I/O流的读写方式


## 5.5读和写流

输入函数

以下3个函数可用于一次读一个字符

    #include<stdio.h>
    int getc(FILE *fp);
    int fgetc(FILE *fp);
    int getchar(void);
    若成功返回下一个字符，若到达文件尾端或出错，返回EOF。

getc可以实现为宏，fgetc不能实现为宏。

将unsigned char 转换为int 类型。 因为EOF 是 -1。

调用 ferror或feof区分出错情况。

每个流在FILE对象中维护了两个标志：
- 出错标志
- 文件结束标志

调用 clearerr清楚这两个标志
ugetc 将字符压送回

输出函数

    #include<stdio.h>
    
    int putc(int c, FILE *fp);
    int fputc(int c, FILE  *fp);
    int putchar(int c);

## 5.7每次一行IO


