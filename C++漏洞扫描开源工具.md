# C/C++开源漏洞扫描工具列表

| 工具名称                                                     | 主要用途                                                     | 备注                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| BLAST（Berkeley Lazy Abstraction Software verification Tool） | BLAST解决的任务是需要检查软件是否满足其相关接口的行为要求。  | 主要针对接口的检验。**不适用。**                             |
| **Cppcheck**                                                 | Cppcheck是一种用于C和C ++ 编程语言的静态代码分析工具。 它是一个多功能工具，可以检查非标准代码。 | 可用，基于命令行，可批量运行测试代码、生成txt格式的报告。**适用** |
| cpplint                                                      | Cpplint是一个python脚本，Google使用它作为自己的C++代码规范检查工具。 | Cpplint只是一个代码风格检测工具，其并不对代码逻辑、语法错误等进行检查。**不适用。** |
| **Clang static analyzer**                                    | Clang静态分析器是Clang项目的一部分，可用于C/C++和Objective-C的静态代码检测。 | 缺陷：报告格式是html网页，不能生成txt报告。**可用性低**      |
| Coccinelle                                                   | Coccinelle是一个文本模式转换的正则式匹配的工具。             | Linux内核的测试/开发工具。**不适用。**                       |
| Frama-c                                                      | Frama-C 是一个用来分析 C 代码的工具，它收集了很多静态统计技术，如代码切片和依赖分析等。 | 主要针对源代码的结构分析，用于收集代码的特征，好像没有漏洞扫描功能。**不适用。** |
| Sparse                                                       | sparse是由Linux之父开发，主要的功能就是静态的检查代码中可能出现的错误，从而减少Linux内核的隐患。 | 针对Linux内核测试。**不适用。**                              |
| **Splint**                                                   | Splint 是一个对 C 源程序（不支持C++）的安全漏洞和代码错误进行静态检查（statically checking）的工具。 | 缺陷：不支持C++。该工具支持的C语言版本非常低，经常遇到“语法错误”，例如需要使用的变量，需要再函数的开头进行集中声明，然后再在后面进行使用。不符合语法规则就直接报错终止，无法检测代码中存在的缺陷。**可用性低** |



# 工具1：Clang Static Analyzer

## 工具简介

Clang静态分析器是一种源代码分析工具，可以查找C，C ++和Objective-C程序中的错误。

目前，它可以作为一个运行[独立的工具](https://clang-analyzer.llvm.org/scan-build.html)或在[Xcode](https://clang-analyzer.llvm.org/xcode.html)上运行。独立工具是从命令行调用的，旨在与代码库的构建一起运行。该分析仪是100％开源的，并且是[Clang](https://clang.llvm.org/)项目的一部分。与Clang的其余部分一样，分析器被实现为C ++库，可供其他工具和应用程序使用。

## 使用方法

使用环境：Ubuntu系统

下载安装：

``` 
sudo apt-get install clang
# 查看版本，检查是否安装成功
clang --version
```

如果要使用Clang Static Analyzer，也就是漏洞扫描，需要使用scan-build和scan-view，所以还需要下载clang-tools，安装好之后就可以通过命令行的方式运行Clang Static Analyzer。

```
sudo apt-get install clang-tools
```

接下来使用工具，先创建一个C代码，如下：

```c
#include<stdio.h> 
#include<stdlib.h> 
int main()  
{  
  int *mem;  
  mem=malloc(sizeof(int));  
  if(mem) return 1;  
  *mem=0xdeadbeaf;  
  free(mem);  
  return 0;  
} 
```

到达mamleak.c路径下，执行 `$ scan-build clang -c memleak.c`命令,得到类似结果：

```
cici@cici-virtual-machine:~/Desktop/ttt$ scan-build clang -c memleak.c
scan-build: Using '/usr/lib/llvm-6.0/bin/clang' for static analysis
memleak.c:7:18: warning: Potential leak of memory pointed to by 'mem'
  if(mem) return 1;  
                 ^
memleak.c:8:7: warning: Dereference of null pointer (loaded from variable 'mem')
  *mem=0xdeadbeaf;  
   ~~~^
2 warnings generated.
scan-build: 2 bugs found.
scan-build: Run 'scan-view /tmp/scan-build-2019-11-13-190926-4198-1' to examine bug reports.
```

最后根据提示可以打开网页版的分析结果，即输入`scan-view /tmp/scan-build-2019-11-13-190926-4198-1`即可得到报告，报告是网页版的形式，打开后如下图：

![image.png](https://i.loli.net/2019/11/13/USnv3BGxyFWdJeT.png)

打开详细报告之后的效果是这样的：

![image.png](https://i.loli.net/2019/11/13/kHFl5qGgEht9yzK.png)

常用命令：

- 单个文件：`scan-build clang -c xxx.c`
- 文件夹：`scan-build clang -c path/*.c`

**工具缺陷**：不能将报告导出到txt文件中，只能在浏览器中查看。



# 工具2：cppcheck

使用方法：

使用命令`sudo apt-get install cppcheck`下载安装

常用命令：

- 单个文件：`cppcheck xxx.cpp`
- 文件夹：`cppcheck path`
- 保存到文件中：`cppcheck xx.cpp 2> err.txt`
- 多线程：`cppcheck -j 4 path`



# 工具3：Splint

现有的Lint程序主要有两个版本:

①PC-Lint,由GimpelSoftware提供的支持C/C++的商用程序。官方地址:http://www.gimpel.com/html/pcl.htm

②Splint(原来的LCLint)是一个有GNU免费授权的Lint程序,只支持c,而不支持c++.官方地址:http://www.splint.org/

由于PC-lint是商业软件,虽说功能强大,但是容易获取。这里主要介绍GNU的splint工具。

Splint是一个动态检查C语言程序安全弱点和编写错误的一个程序,会进行多种错误检查:未使用变量,类型不一致,使用未定义的变量,无法执行的代码,忽略返回值,执行路径没有返回,无限循环等错误。



使用方法：

Ubuntu：`sudo apt-get install splint`

Windows：[下载链接](https://github.com/maoserr/splint_win32/downloads)

常用命令：

- 单个文件：`splint x.c`
- 文件夹：`splint path/*.c > err.txt`
- 保存到文件中：`splint x.c > err.txt`



**缺陷**：该工具支持的C语言版本非常低，经常遇到“语法错误”，例如需要使用的变量，需要再函数的开头进行集中声明，然后再在后面进行使用。不符合语法规则就直接报错终止，无法检测代码中存在的缺陷。

例如：

![image.png](https://i.loli.net/2019/11/14/crybXO8xlT6Iu5s.png)