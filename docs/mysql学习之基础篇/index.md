# Mysql学习之基础篇


## Mysql概述

### mysql安装与卸载

- [MySQL安装文档-Windows版 - 明里的博客 (demo122.github.io)](../mysql安装文档-windows/)
- [MySQL卸载文档-Windows版 - 明里的博客 (demo122.github.io)](../mysql卸载文档-windows/)

> 如果安装出现`MySQL error 1042: Unable to connect to any of the specified MySQL hosts`，[解决方法](https://blog.csdn.net/weixin_44940258/article/details/107461986)

- [sql基础](../mysql/#sql基础)

## SQL语句

sql全称 Structured  Query Language，结构化查询语言。操作关系型数据库的编程语言，定义了
一套操作关系型数据库统一标准。

### SQL分类

| 分类 | 全称                       | 说明                                                   |
| :--: | -------------------------- | :----------------------------------------------------- |
| DDL  | Data Definition Language   | 数据定义语言，用来定义数据库对象(数据库，表，字段)     |
| DML  | Data Manipulation Language | 数据操作语言，用来对数据库表中的数据进行增删改         |
| DQL  | Data Query Language        | 数据查询语言，用来查询数据库中表的记录                 |
| DCL  | Data Control Language      | 数据控制语言，用来创建数据库用户、控制数据库的访问权限 |

### DDL

Data  Definition Language，数据定义语言，用来定义数据库对象(数据库，表，字段) 。

#### 数据库操作 

- 查询所有数据库

  ```sql
  show databases ;
  ```

- 查询当前数据库

  ```sql
  select database() ;
  ```

- 创建数据库

  ```sql
  create database [ if not exists ] 数据库名 [ default charset 字符集 ] [ collate 排序规则 ] ;
  ```

  例如： 

  ```sql
  -- 创建一个itcast数据库, 使用数据库默认的字符集。
  create database itcast;
  
  -- 在同一个数据库服务器中，不能创建两个名称相同的数据库，否则将会报错
  -- 可以通过if not exists 参数来解决这个问题，数据库不存在, 则创建该数据库，如果存在，则不创建
  create database if not extists itcast;
  
  
  -- 创建一个itheima数据库，并且指定字符集
  create database itheima default charset utf8mb4
  ```

- 删除数据库

  ```sql
  drop database [ if exists ] 数据库名 ;
  ```

- 切换数据库

  ```sql
  use 数据库名;
  ```

#### 表操作

##### 表操作-查询创建

- 查询当前数据库所有表

  ```sql
  show tables;
  ```

- 查看指定表结构

  ```sql
  desc 表名;
  ```

- 查询指定表的建表语句

  ```sql
  show create table 表名;
  ```

- **创建表结构**

  ```sql
  CREATE TABLE  表名(
      字段1  字段1类型 [ COMMENT  字段1注释 ],
      字段2  字段2类型 [COMMENT  字段2注释 ],
      字段3  字段3类型 [COMMENT  字段3注释 ],
      ......
      字段n  字段n类型 [COMMENT  字段n注释 ] 
  ) [ COMMENT  表注释 ] ;
  -- 注意: [...] 内为可选参数，最后一个字段后面没有逗号
  ```

  ***示例***：

  ```sql
  create table tb_user(
      id int comment '编号',
      name varchar(50) comment '姓名',
      age int comment '年龄',
      gender varchar(1) comment '性别'
  ) comment '用户表';
  ```

##### 表操作-数据类型

在上述的建表语句中，我们在指定字段的数据类型时，用到了int ，varchar，那么在MySQL中除了
以上的数据类型，还有哪些常见的数据类型呢？ 接下来,我们就来详细介绍一下MySQL的数据类型。
MySQL中的数据类型有很多，主要分为三类：数值类型、字符串类型、日期时间类型。

- 数值类型

  | 类型        |  大小  | 描述               | 无符号(unsigned)范围       | 有符号（signed)范围 |
  | ----------- | :----: | ------------------ | -------------------------- | ------------------- |
  | TINYINT     | 1byte  | 小整数值           | (0，255)                   | (-128，127)         |
  | SMALLINT    | 2bytes | 大整数值           | (-32768，32767)            | (0，65535)          |
  | MEDIUMINT   | 3bytes | 大整数值           | (-8388608，8388607)        | (0，16777215)       |
  | INT/INTEGER | 4bytes | 大整数值           | (-2147483648，2147483647)  | (0，4294967295)     |
  | BIGINT      | 8bytes | 极大整数值         | (-2^63，2^63-1)            | (0，2^64-1)         |
  | FLOAT       | 4bytes | 单精度浮点数值     |                            |                     |
  | DOUBLE      | 8bytes | 双精度浮点数值     |                            |                     |
  | DECIMAL     |        | 小数值(精确定点数) | 依赖于M(精度)和D(标度)的值 |                     |

  ```sql
  # 如: 
  # 1). 年龄字段 -- 不会出现负数, 而且人的年龄不会太大
      age tinyint unsigned
      
  # 2). 分数 -- 总分100分, 最多出现一位小数
      score double(4,1)
  ```

- 字符串类型

  | 类型       | 大小                  | 描述                         |
  | ---------- | --------------------- | ---------------------------- |
  | CHAR       | 0-255 bytes           | 定长字符串(需要指定长度)     |
  | VARCHAR    | 0-65535 bytes         | 变长字符串(需要指定长度)     |
  | TINYBLOB   | 0-255 bytes           | 不超过255个字符的二进制数据  |
  | TINYTEXT   | 0-255 bytes           | 短文本字符串                 |
  | BLOB       | 0-65535 bytes         | 二进制形式的长文本数据       |
  | TEXT       | 0-65535 bytes         | 长文本数据                   |
  | MEDIUMBLOB | 0-16 777 215 bytes    | 二进制形式的中等长度文本数据 |
  | MEDIUMTEXT | 0-16 777 215 bytes    | 中等长度文本数据             |
  | LONGBLOB   | 0-4 294 967 295 bytes | 二进制形式的极大文本数据     |
  | LONGTEXT   | 0-4 294 967 295 bytes | 极大文本数据                 |

  {{< admonition type=tip title="This is a tip" open=false >}}
 **char 与 varchar 都可以描述字符串，char是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关 。**
  {{< /admonition >}}


**char 与 varchar 都可以描述字符串，char是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关 。**

  - **而varchar是变长字符串，指定的长度为最大占用长度 。相对来说，char的性**
    **能会更高些。**

##### 表操作-案例

##### 表操作-修改

##### 表操作-删除

## 约束

## 多表查询

## 事物


