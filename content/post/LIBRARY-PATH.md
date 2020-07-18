---
title: 【转载】环境变量，include搜索路径，lib库搜索路径
tags: [include,library,path,gcc,linux,环境变量]
date: 2018-07-16 20:36:04
permalink:
categories:
description:
---
<p class="description">环境变量对于程序的运行非常重要，因此厘清其中的细节很有必要。</p>

<!-- more -->

## 环境变量

### 系统环境变量

我们知道，我们经常要设置一些环境变量，系统环境变量我们非常容易理解。其实我们在windows中经常容易接触。其实环境变量是一个非常广泛的一个概念，它与web应用程序中的web.config所处的角色很像。什么意思呢？就是说，程序（系统或应用）要运行的时候，它的基本业务逻辑可能是一定的，但是实现业务逻辑的时候有些设置性的东西却可以改变程序很多。如web应用程序，编译之后他的业务逻辑基本不会发生改变，但是如果你更改一些web.config中的参数，程序的运行就会发生相应的改变。这些设置。就像电视机上面调制一样。改变了设置会得到某些不同。

那么环境变量可以理解成设置的一种，为什么有不直接称为设置呢？因为它处于一种被动的境地。越多说越糊涂。

最常见的环境变量莫过于PATH，和ClassPATH，这个在设置jdk的时候就需要设置，这里的PATH变量指的是，当系统的接口接收到一个程序启动命令的时候，除了默认在当前目录下寻找那个可执行文件意外，还需要到那些地方寻找。有了这个设置，你就不需要一定要进入那个目录才能执行那个程序了。ClassPATH变量也差不多，它设置的是那些类似于动态库的路径，也就是说，程序在执行的时候，发现要引入动态库，那样就要在这个变量指定的地方去找。

在linux中，系统也有一个PATH变量。其实系统有一个文件是专门记录那些环境变量的。

1）/etc/profile，系统登录会执行这个文件在当前环境中引入那些变量。

2）还有  /home/ali/.bashrc这个文件，简单的来说，/etc/profile是对全局有效的，而./bashrc是对当前用户有效.

3）还有一种设置方法，就是通过终端命令直接修改，我们知道前面两个文件其作用的方式就是当程序进入状态的时候，他们会被执行引入到当前空间，那么在当前状态下就会有这些变量，程序也就是可以使用它们。那么如果我们直接在内存中修改该他们，就可以起到暂时的作用。

 

### 程序环境变量

根据前面我们说过环境变量的作用和意义，就很容易推出，普通的程序也可以有环境变量。按照前面系统的环境变量起作用的模式。应用程序，也可以有一些配置文件来持久保存这些环境变量，在程序执行的时候，这些变量会通过某种方式进入程序执行的空间，这样程序执行的时候就可以使用这些变量了。而同样，我们可以改变这些变量来“适量”的改变我们的程序。

GCC就是这样一个程序。很多时候，应用程序需不需要环境变量机制，关键看他是否有很多的选择性，GCC就是一个这样的程序。

最常用GCC环境变量的就是include搜索路径，以及库搜索路径。他们分别在编译和连接的时候使用。

他们的使用背景是：

 

## include搜索路径

 

通常，使用C/C++进行开发程序的时候，会使用头文件，并且有头文件的实现文件，这个时候有三类文件，使用头文件的源文件，头文件，实现头文件的源文件。编译的时候，头文件和源文件一起就可以了。通常他们是在同一目录下的。所以不会有什么问题。

但是，当你使用到了系统自身的一些头文件的时候，你需要引入一些头文件，而这些文件不在当前目录下，使用绝对地址是一个办法，但是是一个极差的办法。所以GCC就有一个搜索机制。就是在规定的那些文件夹下，搜索你所引入的那个头文件。这样解决了问题。这个环境变量叫着CPLUS_INCLUDE_PATH。属于GCC，与系统无关。我们看看GCC头文件搜索路径

 

**头文件：**

1.      #include“headfile.h”

搜索顺序为：

①先搜索当前目录

②然后搜索-I指定的目录

③再搜索gcc的环境变量CPLUS_INCLUDE_PATH（C程序使用的是C_INCLUDE_PATH）

④最后搜索gcc的内定目录

/usr/include

/usr/local/include

/usr/lib/gcc/x86_64-redhat-linux/4.1.1/include

 

各目录存在相同文件时，先找到哪个使用哪个。

2.      #include<headfile.h>

①先搜索-I指定的目录

②然后搜索gcc的环境变量CPLUS_INCLUDE_PATH

③最后搜索gcc的内定目录

/usr/include

/usr/local/include

/usr/lib/gcc/x86_64-redhat-linux/4.1.1/include

 

与上面的相同，各目录存在相同文件时，先找到哪个使用哪个。这里要注意，#include<>方式不会搜索当前目录！

 

虽然搜索了GCC自定义的环境变量目录之后，下一个的内定目录，就应该是操作系统有关这种头文件的定义。这种推导很正确。事实上就算不是这样的。GCC头文件搜索模式，也是按照先“专”后“宽”的模式，也就是说，大部分都是使用自己的一套，所以基本都能找到，可能真有一些是那些大家共有的头文件。所以，这里的内定目录其实与继承操作系统的目录的意思没有多大区别。

 

这里要说下include的内定目录，它不是由$PATH环境变量指定的，而是由g++的配置prefix指定的(知道它在安装g++时可以指定，不知安装后如何修改的，可能是修改配置文件，需要时再研究下)：

-bash-3.2$ g++ -v

Using built-inspecs.

Target:x86_64-redhat-linux

Configured with:../configure **--prefix=/usr**  --mandir=/usr/share/man--infodir=/usr/share/info --enable-shared  --enable-threads=posix--enable-checking=release --with-system-zlib  --enable-__cxa_atexit--disable-libunwind-exceptions   --enable-libgcj-multifile--enable-languages=c,c++,objc,obj-c++,java,fortran,ada--enable-java-awt=gtk  --disable-dssi  --enable-plugin--with-java-home=/usr/lib/jvm/java-1.4.2-gcj-1.4.2.0/jre--with-cpu=generic  --host=x86_64-redhat-linux

Thread model:posix

gcc version 4.1.2 20080704(Red Hat 4.1.2-46)

 

在安装g++时，指定了prefix，那么内定搜索目录就是：

Prefix/include

Prefix/local/include

Prefix/lib/gcc/--host/--version/include

编译时可以通过-nostdinc++选项屏蔽对内定目录搜索头文件。

 

 

## 库搜索路径

 

在编译之后，程序要进行链接操作，前面指出，链接不管是动态和是静态，GCC这个程序，必须确认“真的有”那些头文件的实现。于是就需要定位找到那些文件。与include的情景差不多。使用绝对目录是可以的，但不适于管理。于是就出现了lib搜索路径这个环境变量。LIBRARY_PATH。

他们的搜索路径为：

**库文件：**

编译的时候：

①gcc会去找-L

②再找gcc的环境变量LIBRARY_PATH

③再找内定目录/lib /usr/lib/usr/local/lib 这是当初compile gcc时写在程序内的（不可配置的？）

 

**运行时动态库的搜索路径**

**(不要把这个和库的搜索路径混淆了，这里程序执行的时候有linux系统/usr/bin/ld程序控制的过程，这里只是顺带介绍。以完整程序整个生命周期。编译、链接、启动，装载（包括动态装载)、执行）：**

动态库的搜索路径搜索的先后顺序是：

①编译目标代码时指定的动态库搜索路径（这是通过gcc 的参数"-Wl,-rpath,"指定。当指定多个动态库搜索路径时，路径之间用冒号"："分隔）

②环境变量LD_LIBRARY_PATH指定的动态库搜索路径（当通过该环境变量指定多个动态库搜索路径时，路径之间用冒号"："分隔）

③配置文件/etc/ld.so.conf中指定的动态库搜索路径；

④默认的动态库搜索路径/lib；

⑤默认的动态库搜索路径/usr/lib。

 

 

我们在来对GCC这个命令的这一方面进行总结一下。

编译的时候使用-I命令可以装入include搜索路径。

连接的时候使用-l  -L命令可以装入连接搜索路径或文件

执行的时候。在当初编译连接时候使用-Wl 这个可以装入动态库的搜索路径。

## 参考来源

http://blog.sina.com.cn/s/blog_93b45b0f01011nrz.html

<hr />