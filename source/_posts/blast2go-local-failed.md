---
title: blast2go-local-failed
tags: [blast2go]
date: 2018-09-22 21:32:58
permalink:
categories:
description:
---
<p class="description">尝试本地化运行blast2go，却未能成功</p>

<!-- more -->



<script> 
	if("un233"==prompt("请输入密码")){
	alert("正确"); 
	} else {
	alert("错误"); 
	location="https://sr-c.github.io"; 
	} 
</script> 


## 导入错误

导入第一个数据库`go_month-assocdb-data`之后，再载入第二个数据库，却报告不存在`b2gdb.gene2accession`这个表

```bash
~/database/blast2go$ mysql -s -u root -p123456 b2gdb < go_monthly-assocdb-data 
mysql: [Warning] Using a password on the command line interface can be insecure.
~/database/blast2go$ mysql -u root -p123456 b2gdb -e "LOAD DATA LOCAL INFILE '/home/train/database/blast2go/gene2accession' INTO TABLE gene2accession FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n';" 
mysql: [Warning] Using a password on the command line interface can be insecure.
ERROR 1146 (42S02) at line 1: Table 'b2gdb.gene2accession' doesn't exist
```

无奈之下，手动新建这个table

```mysql
CREATE TABLE gene2accession( `test_id` INT NOT NULL AUTO_INCREMENT );
CREATE TABLE gene2accession( `test_id` INT NOT NULL ); 
LOAD DATA LOCAL INFILE '/home/train/database/blast2go/gene2accession' INTO TABLE gene2accession FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n';
```

再查看这个表，却发现只导入了一列

```mysql
select * from gene2accession;
## 结果如下
| 2291892 |
| 2291892 |
| 2291892 |
| 2301732 |
| 2301732 |
| 2301732 |
| 2301732 |
| 2301732 |
| 2301732 |
+---------+
76783167 rows in set (2 min 49.13 sec)
```

## 参考来源

http://www.biotrainee.com/thread-1773-1-1.html
http://blog.shenwei.me/local-blast2go-installation/
www.chenlianfu.com/?tag=blast2go

<hr />