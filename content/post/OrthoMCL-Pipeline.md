---
title: 自动化运行OrthoMCL
tags: [OrthoMCL]
date: 2018-09-18 17:11:46
permalink:
categories:
description:
---
<p class="description">使用OrthoMCL Pipeline 来自动化运行OrthoMCL，简化操作步骤。</p>

<!-- more -->

## 安装

```bash
git clone https://github.com/apetkau/orthomcl-pipeline.git
```

### Perl模块

通过`cpanm`安装

```bash
cpanm BioPerl DBD::mysql DBI Parallel::ForkManager YAML::Tiny Set::Scalar Text::Table Exception::Class Test::Most Test::Warn Test::Exception Test::Deep Moose SVG Algorithm::Combinatorics
```

### 依赖软件

- [OrthoMCL](http://orthomcl.org/common/downloads/software/v2.0/) 或 [OrthoMCL Custom](https://github.com/apetkau/orthomclsoftware-custom) （能够自定义序列识别符的修改版本）
- [BLAST](http://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs&DOC_TYPE=Download) (blastall, formatdb) 注意不是NCBI-blast+, 推荐使用`2.2.26`版本
- [MCL](http://www.micans.org/mcl/index.html)

依赖环境解决后能够顺利运行设置脚本

```bash
$ perl scripts/orthomcl-pipeline-setup.pl
Checking for Software dependencies...
Checking for OthoMCL ... OK
Checking for formatdb ... OK
Checking for blastall ... OK
Checking for mcl ... OK
Wrote new configuration to orthomcl-pipeline/scripts/../etc/orthomcl-pipeline.conf
Wrote executable file to orthomcl-pipeline/scripts/../bin/orthomcl-pipeline
Please add directory orthomcl-pipeline/scripts/../bin to PATH
```

### 数据库设置

#### 创建orthomcl用户

```bash
$ mysql -u root -p 
Enter password: 
mysql> GRANT SELECT, INSERT, UPDATE, DELETE, CREATE, CREATE VIEW, INDEX, DROP on . to orthomcl;  #创建用户并授权 
mysql> set password for orthomcl@localhost = password('orthomcl');  #设置用户密码 
mysql> quit;
```

#### 创建数据库并生成配置脚本

```bash
$ perl scripts/orthomcl-setup-database.pl --user orthomcl --password orthomcl --host localhost --database orthomcl --outfile orthomcl.conf
Connecting to mysql and creating database **orthmcldb** on host orthodb with user orthomcl ...OK
database orthmcl created ...OK
Config file **orthomcl.conf** created.
```
若已有orthomcl数据库，则可添加`--no-create-database`参数，不新建数据库。
## 测试数据

```bash
$ perl t/test_pipeline.pl -m orthomcl.conf -s fork -t /tmp
Test using scheduler fork

TESTING NON-COMPLIANT INPUT
TESTING FULL PIPELINE RUN 3
README:
Tests case of one gene (in 1.fasta and 2.fasta) not present in other files.
ok 1 - Expected matched returned groups file
...
```

## 运行

```bash
$ ./bin/orthomcl-pipeline
Error: no input-dir defined
Usage: orthomcl-pipeline -i [input dir] -o [output dir] -m [orthmcl config] [Options]
...
```

## 参考来源

https://github.com/apetkau/orthomcl-pipeline/blob/master/INSTALL.md

https://www.jianshu.com/p/449a51fa3d18

<hr />