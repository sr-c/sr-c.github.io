---
title: 使用PBJelly与blasr进行补洞
tags: [PBJelly,blasr,PacBio]
date: 2019-07-02 14:12:47
permalink:
categories:
description:
---
<p class="description">使用PBJelly进行gap filling</p>
<!-- more -->

PBJelly在2012年发表于PLoS ONE, 其设计目的是使用PacBio RS Ⅱ 数据来提升assembly的连续性。目前版本停滞在2015年9月1日的`15.8.24`，但仍然有`19 downloads/week`的下载热度。

## 安装篇

### legacy-blasr

PBJelly的安装[说明](https://sourceforge.net/p/pb-jelly/wiki/Home/)表示依赖1.3.1版本的`blasr`，但此版本在2012年，现已难以[安装](https://github.com/OSGConnect/modulefiles/blob/master/recipes/blasr-1.3.1)。

几番尝试后，我们选择使用PacBio提供的[pitchfork](https://github.com/PacificBiosciences/pitchfork)来编译该过时的软件。

```bash
cd /path/to/software
git clone https://github.com/PacificBiosciences/pitchfork.git
cd pitchfork
git checkout legacy_blasr
echo PREFIX=/opt/mybuild > settings.mk
make init
make blasr
```

此过程会下载依赖的各种软件，`python3`环境中会报错，需要切换到`python2.7`环境。

此外，`hdf5-1.8.16`的下载路径[失效](https://github.com/PacificBiosciences/pitchfork/issues/289)，自行下载[hdf5-1.8.16.tar.gz](https://hdfgroup.org/ftp/HDF5/releases/hdf5-1.8/hdf5-1.8.16/src/hdf5-1.8.16.tar.gz)至`pitchfork/ports/thirdparty/hdf5`中，重新`make blasr`可解决。

编译完成后，实际的`blasr`版本为`5.0.994e5fc`，将其导入环境变量

```bash
source /opt/mybuild/setup-env.sh
```

### networkx v1.1

说明文档要求`1.1`版本，实测`conda`安装的`1.1.1`版本也可成功运行。

```bash
conda install networkx==1.1.1
```

> p.s.  在extraction步骤，存在警告
>
> 2019-07-04 20:40:10,451 [INFO] Opening GML Files
> 2019-07-04 20:40:10,452 [WARNING] It is unknown if networkx version 1.11 will work.
> 2019-07-04 20:40:10,452 [WARNING] If you get an error here, please report it!!!

### PBJelly

```bash
wget https://sourceforge.net/projects/pb-jelly/files/PBSuite_15.8.24.tgz
tar zxf PBSuite_15.8.24.tgz
cd PBSuite_15.8.24
## edit SWEETPATH in setup.sh
vi setup.sh
source setup.sh
```

## 测试篇

按照示例[教程](https://github.com/alvaralmstedt/Tutorials/wiki/Gap-closing-with-PBJelly)进行测试

```bash
cd /path/to/PBSuite/docs/jellyExample
## edit the paths in the <reference> , <outputDir> and the baseDir attribute in the inputs tag to the full path in Protocol.xml
vi Protocol.xml

#Jelly.py <stage> Protocol.xml
Jelly.py setup <jellyprotocol.xml>
Jelly.py mapping <jellyprotocol.xml>
Jelly.py support <jellyprotocol.xml>
Jelly.py extraction <jellyprotocol.xml>
Jelly.py assembly <jellyprotocol.xml> -x “--nproc=4”
Jelly.py output <jellyprotocol.xml>

grep -Ho N jelly.out.fasta | uniq -c
```

运行成功，会将输入的3条contig连接为1条，N的数目由1494降至0

## 检查输入

```bash
sumarizeAssembly.py <reference.fasta>
readSummary.py <Protocol.xml>
```

PBJelly要求的reference输入格式只能是fasta格式，且后缀只能是`.fasta`；reads的输入格式可以为`fastq`或`fasta`，但输入fasta时要求有相应的质量文件

### Debug

```bash
Jelly.py setup <jellyprotocol.xml>

##切换到screen中运行
screen -S PBJelly
Jelly.py mapping <jellyprotocol.xml>

##比对步骤很快结束，查验mapping文件夹内的mapping/xxx.fastq.err
cat mapping/xxx.fastq.err
2019-07-02 20:03:34,546 [INFO] Running /path/to/software/PBSuite_15.8.24/bin/m4pie.py /path/to/workdir/04.genome_assembling/gap-filling/PBJelly/yourAssembly/mapping/AssemblyXXX.fastq.m4 /path/to/workdir/04.genome_assembling/gap-filling/PBJelly/yourAssembly/data/reads/AssemblyXXX.fastq /path/to/workdir/04.genome_assembling/gap-filling/PBJelly/yourAssembly/data/reference/scaffolds_FINAL.fasta --nproc 4 -i
Traceback (most recent call last):
  File "/path/to/software/PBSuite_15.8.24/bin/m4pie.py", line 209, in <module>
    run(sys.argv[1:])
  File "/path/to/software/PBSuite_15.8.24/bin/m4pie.py", line 171, in run
    aligns = M4File(args.m4)
  File "/path/to/software/PBSuite_15.8.24/pbsuite/utils/FileHandlers.py", line 484, in __init__
    file = open(file,'r')
IOError: [Errno 2] No such file or directory: '/path/to/workdir/04.genome_assembling/gap-filling/PBJelly/yourAssembly/mapping/AssemblyXXX.fastq.m4
```

搜索`PBSuite_15.8.24/bin/m4pie.py no such file`，能发现很多类似情况的报错

> https://sourceforge.net/p/pb-jelly/discussion/pbjtiks/thread/0c780910/?limit=25
>
> https://www.biostars.org/p/198519/
>
> https://sourceforge.net/p/pb-jelly/discussion/pbjtiks/thread/e351eed7/#a608

原因不一，包括新旧版本`blasr`在参数输入中，使用`-`与`--`的不同。

在`pbsuite/utils/CommandRunner.py`的第18行的`subprocess.Popen()`函数中[添加](https://sourceforge.net/p/pb-jelly/discussion/pbjtiks/thread/0c780910/#47df)`executable="/bin/bash"`

但此次报错的可能原因在于`screen`后的环境配置与原环境不同，使`blasr`不能正常运行。

#### 判断依据

在screen屏幕中，`blasr`不可执行

```bash
blasr --help
blasr: error while loading shared libraries: libpbihdf.so: cannot open shared object file: No such file or directory
```

在原环境中执行`Jelly.py mapping <jellyprotocol.xml>`, `blasr`即可正常运行。

```bash
## 传递debug参数，帮助排查问题
Jelly.py mapping <jellyprotocol.xml> -x "--debug"
```

#### 小tips

`Ctrl + C`手动终止`mapping`任务时，需注意，`blasr`不会相应终止，需要在任务管理器中终止。

## 总结

运行PBJelly之前，需要依次激活下列环境

```bash
## pitchfork编译的legacy-blasr环境
source ~/opt/pitchfock/setup-env.sh
## 安装有networkx1.1的python2.7环境
source activate SALSA
## PBSuite环境
source ~/software/PBSuite_15.8.24/setup.sh
```



## 参考来源

http://onsnetwork.org/kubu4/2017/10/30/software-installation-pb-jelly-suite-and-blasr-on-emu/

https://genefish.wordpress.com/2017/04/06/pbjelly-pt-2/

https://github.com/alvaralmstedt/Tutorials/wiki/Gap-closing-with-PBJelly

<hr />