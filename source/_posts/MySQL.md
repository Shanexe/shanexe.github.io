---
title: MySQL
date: 2018-11-16 21:55:11
tags: [mysql]
categories: 开源应用
---

# 关系数据库基础

<!-- more -->

### 关系模型

[关系模型](https://zh.wikipedia.org/wiki/关系模型)是实践中很少会去注意的理论，为图灵奖获得者、数据库先驱，[Edgar Frank Codd](https://zh.wikipedia.org/wiki/埃德加·科德)提出，是关系型数据库的基础。不过随着数据库技术的完善，现在的开发人员不需要精通数据库理论也能很好地开发业务了。

### 数据库范式

数据库范式是为了数据库设计的规范化而提出的一些概念。从第一范式到第六范式要求越严格，约束性越高。通俗来讲，即范式越小，数据冗余越大（数据重复），可能表现为存储变大等缺点；而范式越大，查询效率越慢（一次查询要跨多个表），可能表现为join操作变多（join是关系数据库最耗时的操作，也是最大的瓶颈之一）。因此最好的实践是，结合硬件资源、人力资源，将数据库表设计成适合业务需求的范式，通常认为第三范式是最合适的。

# MySQL索引

### 索引原理

- B+树

- InnoDB与MyISAM索引区别

  ![](https://raw.githubusercontent.com/Shaneue/pictures/master/index_mysql.png)
