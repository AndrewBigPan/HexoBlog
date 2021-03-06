---
title: C/C++中文的编码和字符串处理
tags:
  - C++
categories:
  - C++
photos:
 - https://i.loli.net/2018/10/12/5bc03f0951d75.png
---

windows和linux平台下用C/C++处理中文字符的知识点记录。
<!--more-->

```CPP
// windows
  char c1;        // 表示单字符，占用一个字节
  wchar_t wc1;    // 表示宽字符，占用两个字节

// Linux
  char c2;        // 占用一个字节
  wchar_t wc2;    // 占用四个字节
```
&emsp;&emsp;windows平台下对于用字符串保存中文的问题，GBK和UTF8都是用char来表示，只是为了表示一个中文字符需要用到多个char。而对于UNICODE（其实应该说是UFT16），每一个字符都需要一个两个字节，也就是用wchar_t表示。<br>
&emsp;&emsp;[UNICODE][UNICODE]只是一个字符集，规定了不同的字符对应于一个唯一的整数，平时所说的使用UNICODE编码其实说的是UFT16编码（顾名思义就是用16位来表示一个字符）。<br>
&emsp;&emsp;[UTF8][UTF8]、[UTF16][UTF16]和[UTF32][UTF32]则是基于UNICODE字符集的三种编码方式。不同之处是：对于一个字符所对应的整数，应该怎样用二进制位表示出来。对于UTF16和UTF32，不管字符对应的数字是多少，都用恒定的多字节表示，所以可以很方便的表示一个字符，但需要注意字节序问题。比较麻烦的是UFT8，对于不同的字符，可能会用到一个字节，两个字节到最多六个字节。这么做的好处是节省了空间。<br>
&emsp;&emsp;在实际使用字符串保存时，因为UTF16不管什么字符，都用2个字节表示，所以可能会出现某一个字节全零的情况。例如字符‘A’编码是0x41，用UTF16表示就是0x0041。对于char表示的字符串，以0x00表示结尾，所以没有办法正确的存储此类数据，此时只能用wchar_t来保存。<br>
&emsp;&emsp;UTF8编码方式如下：用1~6个字节存储一个字符，当第一个字节的首位为0时，表示这个字符只用一个字节表示（刚好与ASCII码一一对应），当用多字节表示一个字符时，首字节以连续的多个1和一个0开始，表示用多个字节。例如用3个字节是，首字节为1110xxxx，后面各字节均以10开始。<br>

| UNICODE原码(16进制) | UTF8(2进制)                |
| ------------------- | -------------------------- |
| 0000-007F           | 0xxxxxxx                   |
| 0080-07FF           | 110xxxxx 10xxxxxx          |
| 0800-FFFF           | 1110xxxx 10xxxxxx 10xxxxxx |
| ......              | ......                     |

&emsp;&emsp;如上所示，UTF8中的‘x’就是实际表示字符编码的位。表示的最大值就是全1的情况，最小值就是少一个字节的情况下最大值加1，因为少一个字节已经可以存的下的字符，不会用多一个字节来保存。由上可知，UTF8可以直接用char类型的字符串来表示，只是用对应的解释方式来解释就可以正确显示了。<br>
&emsp;&emsp;另外一种就是[GBK][GBK]等编码方式。这一类编码方式和UNICODE没有任何关系，是另一种字符集和编码方式的规定。使用方法可以类比于UTF8，在编码小于128时，就是ASCII，而中文的编码均大于128，用超过一个字节来表示。在平时编写的windows程序中，可以理解为如果使用了UNICODE宏，就是在用wchar_t来表示中文，使用UTF16编码，如果没有UNICODE宏，那么就是在用GBK，以char来表示中文。<br>
&emsp;&emsp;windows平台下的TCHAR类型就是通过宏对char和wchar_t的封装。可根据当前平台情况选择对应的类型。_T修饰的字符串常量同理，根据是否定义的UNICODE宏，分别表示""或L""。<br>
&emsp;&emsp;Linux平台的不同在于，wchar_t用4个字节表示，也就是UCS-4，而windows用两个字节，UCS-2。<br>

参考：
[http://www.cnblogs.com/KevinYang/archive/2010/06/18/1760597.html](http://www.cnblogs.com/KevinYang/archive/2010/06/18/1760597.html)
[http://blog.csdn.net/shellching/article/details/5316442](http://blog.csdn.net/shellching/article/details/5316442)

[UNICODE]: https://en.wikipedia.org/wiki/Unicode
[UTF8]: https://en.wikipedia.org/wiki/UTF-8
[UTF16]: https://en.wikipedia.org/wiki/UTF-16
[UTF32]: https://en.wikipedia.org/wiki/UTF-32
[GBK]: https://en.wikipedia.org/wiki/GBK
