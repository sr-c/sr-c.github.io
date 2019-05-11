---
title: SALSA安装
tags: [SALSA,debug,Hi-C]
date: 2019-02-28 22:16:51
permalink:
categories:
description:
---
<p class="description">SALSA的编译安装</p>

<!-- more -->

## 安装

```bash
git clone https://github.com/machinegun/SALSA.git

conda create -n SALSA python=2.7
source activate SALSA
conda install boost networkx==1.11

cd SALSA
make
```

## debug

```bash
g++ -O3 -Wall -Wextra -std=c++11 -o correct_links correct_links.cpp
correct_links.cpp:12:10: fatal error: boost/config.hpp: No such file or directory
 #include <boost/config.hpp>
          ^~~~~~~~~~~~~~~~~~
compilation terminated.
```

这表明，编译过程中未配置好之前的boost环境，于是我们查看发现原来已经安装了boost，因此，我们卸载conda环境中的boost直接使用原环境中的boost进行编译，但是仍然得到报错

```bash
/usr/include/boost/graph/graph_concepts.hpp:92:18: error: using invalid field 'boost::concepts::IncidenceGraph<G>::p'
             e = *p.first;
                  ^
make: *** [correct_links] Error 1
```

经过查询似乎是原来环境中的boost版本过低导致，因此，还是需要手动指定conda环境中的boost

```bash
export CPLUS_INCLUDE_PATH=~/miniconda3/envs/SALSA/include:$CPLUS_INCLUDE_PATH
export BOOST_INCLUDE=~/miniconda3/envs/SALSA/include
```

之后可完成编译

```bash
> source ~/.bashrc
> make clean
> make
g++ -O3 -Wall -Wextra -std=c++11 -o correct_links correct_links.cpp
correct_links.cpp: In function 'std::vector<std::__cxx11::basic_string<char> > split(const string&, size_t)':
correct_links.cpp:46:19: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]
   for(int i = 0;i < s.length();i+=count)
                 ~~^~~~~~~~~~~~
correct_links.cpp: In function 'int main(int, char**)':
correct_links.cpp:237:25: warning: comparison between signed and unsigned integer expressions [-Wsign-compare]
         for(int i = 0;i < orientations.size();i++)
                       ~~^~~~~~~~~~~~~~~~~~~~~
```

### 查看环境中boost的版本

```bash
cat /usr/include/boost/version.hpp | grep "BOOST_LIB_VERSION"
//  BOOST_LIB_VERSION must be defined to be the same as BOOST_VERSION
#define BOOST_LIB_VERSION "1_41"
cat ~/miniconda3/envs/SALSA/include/boost/version.hpp | grep "BOOST_LIB_VERSION"
//  BOOST_LIB_VERSION must be defined to be the same as BOOST_VERSION
#define BOOST_LIB_VERSION "1_69"
```

## 参考来源

https://blog.csdn.net/lijingpengchina/article/details/9100775

https://blog.csdn.net/dongchongyang/article/details/72718687

https://blog.csdn.net/Doubao93/article/details/80176537

https://askubuntu.com/questions/147474/how-can-i-find-boost-version

<hr />