# mysql学习之基础篇-1-安装、sql基础


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

### DDL 定义数据库对象(数据库，表，字段) 

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

- **数值类型**

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

- **字符串类型**

  | 类型       | 描述                         | 大小                  |
  | ---------- | ---------------------------- | --------------------- |
  | CHAR       | 定长字符串(需要指定长度)     | 0-255 bytes           |
  | VARCHAR    | 变长字符串(需要指定长度)     | 0-65535 bytes         |
  | TINYBLOB   | 不超过255个字符的二进制数据  | 0-255 bytes           |
  | TINYTEXT   | 短文本字符串                 | 0-255 bytes           |
  | BLOB       | 二进制形式的长文本数据       | 0-65535 bytes         |
  | TEXT       | 长文本数据                   | 0-65535 bytes         |
  | MEDIUMBLOB | 二进制形式的中等长度文本数据 | 0-16 777 215 bytes    |
  | MEDIUMTEXT | 中等长度文本数据             | 0-16 777 215 bytes    |
  | LONGBLOB   | 二进制形式的极大文本数据     | 0-4 294 967 295 bytes |
  | LONGTEXT   | 极大文本数据                 | 0-4 294 967 295 bytes |

  {{< admonition abstract "char和varchar" true >}}
   **char 与 varchar 都可以描述字符串，char是定长字符串，指定长度多长，就占用多少个字符，和字段值的长度无关 。**
  {{< /admonition >}}

  {{< admonition info "char和varchar" true >}}
  **而varchar是变长字符串，指定的长度为最大占用长度 。相对来说，char的性**
  **能会更高些。**
  {{< /admonition >}}

    ```sql
    -- 如： 
    -- 1). 用户名 username ------> 长度不定, 最长不会超过50
     username varchar(50)
  
    -- 2). 性别 gender ---------> 存储值, 不是男,就是女
     gender char(1)
  
    -- 3). 手机号 phone --------> 固定长度为11
     phone char(11)
    ```

- **日期时间类型**

  | 类型      | 大小 | 描述                     | 格式                | 范围                                       |
  | --------- | ---- | ------------------------ | ------------------- | ------------------------------------------ |
  | DATE      | 3    | 日期值                   | YYYY-MM-DD          | 1000-01-01 至 9999-12-31                   |
  | TIME      | 3    | 时间值或持续时间         | HH:MM:SS            | -838:59:59 至 838:59:59                    |
  | YEAR      | 1    | 年份值                   | YYYY                | 1901 至 2155                               |
  | DATETIME  | 8    | 混合日期和时间值         | YYYY-MM-DD HH:MM:SS | 1000-01-01 00:00:00 至9999-12-31 23:59:59  |
  | TIMESTAMP | 4    | 混合日期和时间值，时间戳 | YYYY-MM-DD HH:MM:SS | 1970-01-01 00:00:01 至 2038-01-19 03:14:07 |

  ```sql
  -- 如: 
  -- 1). 生日字段 birthday
     birthday date
  
  -- 2). 创建时间 createtime
     createtime  datetime
  ```

##### 表操作-案例

{{< admonition question "设计一张员工信息表，要求如下：" true >}}

1. **编号（纯数字）**
2. **员工工号 (字符串类型，长度不超过10位)**
3. **员工姓名（字符串类型，长度不超过10位）**
4. **性别（男/女，存储一个汉字）**
5. **年龄（正常人年龄，不可能存储负数）**
6. **身份证号（二代身份证号均为18位，身份证中有X这样的字符）**
7. **入职时间（取值年月日即可**

{{</admonition>}}

**对应的建表语句如下:**

```sql
create table emp(
    id int comment '编号',
    workno varchar(10) comment '工号',
    name varchar(10) comment '姓名',
    gender char(1) comment '性别',
    age tinyint unsigned comment '年龄',
    idcard char(18) comment '身份证号',
    entrydate date comment '入职时间'
) comment '员工表';
```

##### 表操作-修改

- **添加字段**

  ```sql
  ALTER TABLE 表名 ADD  字段名  类型 (长度)  [ COMMENT 注释 ]  [ 约束 ];
  ```

  **案例: 为emp表增加一个新的字段”昵称”为nickname，类型为varchar(20)**

  ```sql
  ALTER TABLE emp ADD nickname varchar(20)  COMMENT '昵称';
  ```

- **修改数据类型**

  ```sql
  ALTER TABLE 表名 MODIFY  字段名  新数据类型 (长度);
  ```

- **修改字段名和字段类型**

  ```sql
  ALTER TABLE 表名 CHANGE  旧字段名  新字段名  类型 (长度)  [ COMMENT 注释 ]  [ 约束 ];
  ```

  **案例： 将emp表的nickname字段修改为username，类型为varchar(30)**

  ```sql
  ALTER TABLE emp CHANGE  nickname  username varchar(30)  COMMENT '昵称';
  ```

- **删除字段**

  ```sql
  ALTER TABLE 表名 DROP  字段名;
  ```

  **案例: 将emp表的字段username删除**

  ```sql
  ALTER TABLE emp DROP  username;
  ```

- **修改表名**

  ```sql
  ALTER TABLE 表名 RENAME TO  新表名;
  ```

  **案例: 将emp表的表名修改为 employee**

  ```sql
  ALTER TABLE emp RENAME TO employee;
  ```

##### 表操作-删除

- **删除表**

  ```sql
  DROP  TABLE [ IF  EXISTS ]  表名;
  ```

  > 可选项 IF EXISTS 代表，只有表名存在时才会删除该表，表名不存在，则不执行删除操作(如果不
  > 加该参数项，删除一张不存在的表，执行将会报错)。

- **删除指定表, 并重新创建表（删除数据，保留表结构）**

  ```sql
  TRUNCATE  TABLE 表名;
  ```

  > 注意: 在删除表的时候，表中的全部数据也都会被删除。

### DML 对数据库中表的数据记录进行增、删、改操作

DML英文全称是Data Manipulation Language(数据操作语言)，用来对数据库中表的数据记录进
行增、删、改操作。

- **添加数据（INSERT）**
- **修改数据（UPDATE)**
- **删除数据（DELETE）**

#### 添加数据

- **给指定字段添加数据**

  ```sql
  INSERT INTO 表名 (字段名1, 字段名2, ...)  VALUES (值1, 值2, ...);
  
  -- 案例 ：给employee表所有的字段添加数据
  insert into employee(id,workno,name,gender,age,idcard,entrydate) 
  values(1,'1','Itcast','男',10,'123456789012345678','2000-01-01');
  ```

- 给全部字段添加数据

  ```sql
  NSERT INTO 表名 VALUES (值1, 值2, ...);
  
  -- 案例：插入数据到employee表，具体的SQL如下：
  insert into employee values(2,'2','张无忌','男',18,'123456789012345670','2005-01-01');
  ```

- 批量添加数据

  ```sql
  INSERT INTO 表名 (字段名1, 字段名2, ...)  VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
  
  INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...) ;
  
  -- 案例：批量插入数据到employee表，具体的SQL如下
  insert into employee values(3,'3','韦一笑','男',38,'123456789012345670','2005-01-01'),(4,'4','赵敏','女',18,'123456789012345670','2005-01-01');
  ```

  > 注意事项:
  >
  > - 插入数据时，指定的字段顺序需要与值的顺序是一一对应的。
  > - 字符串和日期型数据应该包含在引号中。
  > - 插入的数据大小，应该在字段的规定范围内。

#### 修改数据

**修改数据的具体语法为:**

```sql
UPDATE   表名   SET   字段名1 = 值1 , 字段名2 = 值2 , .... [ WHERE  条件 ] ;

-- 案例
-- A. 修改id为1的数据，将name修改为itheima
update employee set name = 'itheima' where id = 1;

-- B. 修改id为1的数据, 将name修改为小昭, gender修改为 女
update employee set name = '小昭' , gender = '女' where id = 1;

-- C. 将所有的员工入职日期修改为 2008-01-01
update employee set entrydate = '2008-01-01';
```

> 注意事项:
>     **修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据**

#### 删除数据

删除数据的具体语法为:

```sql
DELETE  FROM  表名  [ WHERE  条件 ] ;

-- 案例
-- A. 删除gender为女的员工
delete from employee where gender = '女';

-- B. 删除所有员工
delete from employee;
```

> 注意事项: 
>
> - DELETE 语句的条件可以有，也可以没有，如果没有条件，则会删除整张表的所有数
>   据
> - DELETE 语句不能删除某一个字段的值(可以使用UPDATE，将该字段值置为NULL即
>   可)
> - 当进行删除全部数据操作时，datagrip会提示我们，询问是否确认删除，我们直接点击
>   Execute即可

### DQL 查询数据库中表的记录

DQL英文全称是Data Query Language(数据查询语言)，数据查询语言，用来查询数据库中表的记
录。查询关键字: SELECT

#### 基本语法

**DQL 查询语句，语法结构如下：**

```sql
SELECT
    字段列表
FROM
    表名列表
WHERE
    条件列表
GROUP  BY
    分组字段列表
HAVING
    分组后条件列表
ORDER BY
    排序字段列表
LIMIT
    分页参数
```

#### 基础查询

- 查询多个字段 `SELECT   字段1, 字段2, 字段3 ...  FROM   表名 ;`

- 查询所有 `SELECT  *  FROM   表名 ;`

  >注意 : * 号代表查询所有字段，在实际开发中尽量少用（不直观、影响效率）。

- 字段设置别名

  ```sql
  SELECT   字段1  [ AS  别名1 ] , 字段2  [ AS  别名2 ]   ...  FROM   表名;
  
  SELECT   字段1  [ 别名1 ] , 字段2  [ 别名2 ]   ...  FROM   表名;
  ```

- 去除重复记录

  ```sql
  SELECT  DISTINCT  字段列表  FROM   表名;
  ```

#### 条件查询

- 语法 

  ```sql
  SELECT  字段列表  FROM   表名   WHERE   条件列表 ;
  ```

- **条件**

  **常用的比较运算符如下**

  | 比较运算符              | 功能                                         |
  | ----------------------- | -------------------------------------------- |
  | \>                      | 大于                                         |
  | \>=                     | 大于等于                                     |
  | <                       | 小于                                         |
  | <=                      | 小于等于                                     |
  | =                       | 等于                                         |
  | **<> 或 !=**            | **不等于**                                   |
  | **BETWEEN ... AND ...** | **在某个范围之内(含最小、最大值)**           |
  | **IN(...)**             | **在in之后的列表中的值，多选一**             |
  | **LIKE 占位符**         | **模糊匹配(_匹配单个字符, %匹配任意个字符)** |
  | **IS NULL**             | **是NULL**                                   |

  **常用的逻辑运算符如下:**

  | 逻辑运算符 | 功能                        |
  | ---------- | --------------------------- |
  | AND 或 &&  | 并且 (多个条件同时成立)     |
  | OR 或      | 或者 (多个条件任意一个成立) |
  | NOT 或 !   | 非 , 不是                   |

#### 聚合查询

- 常见的聚合函数

  | 函数  | 功能     |
  | ----- | -------- |
  | count | 统计数量 |
  | max   | 最大值   |
  | min   | 最小值   |
  | avg   | 平均值   |
  | sum   | 求和     |

- 语法 `ELECT  聚合函数(字段列表)  FROM   表名 ;`

  > 注意 : NULL值是不参与所有聚合函数运算的。

#### 分组查询

- 语法

  ```sql
  SELECT  字段列表  FROM  表名  [ WHERE  条件 ]  GROUP   BY  分组字段名  [ HAVING  分组后过滤条件 ];
  
  -- 案例
  -- A. 根据性别分组 , 统计男性员工 和 女性员工的数量
  select gender, count(*) from emp group by gender ;
  
  -- B. 根据性别分组 , 统计男性员工 和 女性员工的平均年龄
  select gender, avg(age) from emp group by gender ;
  
  -- C. 查询年龄小于45的员工 , 并根据工作地址分组 , 获取员工数量大于等于3的工作地址
  select workaddress, count(*) address_count from emp where age < 45 group by 
  workaddress having address_count >= 3;
  
  -- D. 统计各个工作地址上班的男性及女性员工的数量
  select workaddress, gender, count(*) '数量' from emp group by gender , workaddress 
  ```

- **where与having区别**

  - **执行时机不同：where是分组之前进行过滤，不满足where条件，不参与分组；而having是分组**
    **之后对结果进行过滤**
  - **判断条件不同：where不能对聚合函数进行判断，而having可以。**

  > 注意事项:
  >   • **分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义。**
  >   **• 执行顺序: where > 聚合函数 > having 。**
  >   • **支持多字段分组, 具体语法为 : group by columnA,columnB**

#### 排序查询

- 语法

  ```sql
  SELECT  字段列表  FROM   表名  ORDER  BY  字段1  排序方式1 , 字段2  排序方式2 
  ```

- **排序方式**

  - **ASC : 升序(默认值)**
  - **DESC: 降序**

  > 注意事项：
  >   • 如果是升序, 可以不指定排序方式ASC ;
  >   • 如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序 ;

- 案例

  ```sql
  -- 根据年龄对公司的员工进行升序排序 , 年龄相同 , 再按照入职时间进行降序排序
  select * from emp order by age asc , entrydate desc;
  ```

#### 分页查询

- 语法

  ```sql
  SELECT  字段列表  FROM   表名  LIMIT  起始索引, 查询记录数 ;
  
  -- 案例
  -- 查询第1页员工数据, 每页展示10条记录
  select * from emp limit 0,10;
  
  -- 查询第2页员工数据, 每页展示10条记录 --------> (页码-1)*页展示记录数
  select * from emp limit 10,10;
  ```

  > 注意事项:
  >   • 起始索引从0开始，起始索引 = （查询页码 - 1）* 每页显示记录数。
  >   • 分页查询是数据库的方言，不同的数据库有不同的实现，MySQL中是LIMIT。
  >   • 如果查询的是第一页数据，起始索引可以省略，直接简写为 limit 10。

#### 案例

```sql
-- 查询年龄为20,21,22,23岁的员工信息。
select * from emp where gender = '女' and age in(20,21,22,23);

-- 查询性别为 男 ，并且年龄在 20-40 岁(含)以内的姓名为三个字的员工
select * from emp where gender = '男' and ( age between 20 and 40 ) and name like 
'___';

-- 统计员工表中, 年龄小于60岁的 , 男性员工和女性员工的人数
select gender, count(*) from emp where age < 60 group by gender;

-- 查询所有年龄小于等于35岁员工的姓名和年龄，并对查询结果按年龄升序排序，如果年龄相同按
-- 入职时间降序排序
select name , age from emp where age <= 35 order by age asc , entrydate desc;

-- 查询性别为男，且年龄在20-40 岁(含)以内的前5个员工信息，对查询的结果按年龄升序排序，
-- 年龄相同按入职时间升序排序
select * from emp where gender = '男' and age between 20 and 40 order by age asc , 
entrydate asc limit 5 ;
```

#### 执行顺序

```sql
SELECT            执行顺序
    字段列表          4
FROM
    表名列表          1
WHERE
    条件列表          2
GROUP  BY
    分组字段列表       
HAVING               3
    分组后条件列表
ORDER BY
    排序字段列表       5 
LIMIT
    分页参数          6
```

故而顺序为： 

```sql
FROM
    表名列表 
WHERE
    条件列表  
GROUP  BY
    分组字段列表       
HAVING              
    分组后条件列表
SELECT       
    字段列表
ORDER BY
    排序字段列表       
LIMIT
    分页参数          
```

### DCL 管理数据库用户、控制数据库的访问权限

DCL英文全称是Data Control Language(数据控制语言)，用来管理数据库用户、控制数据库的访
问权限。

#### 管理用户

- 查询用户 `select * from mysql.user;`

  >其中 Host代表当前用户访问的主机, 如果为localhost, 仅代表只能够在当前本机访问，是不可以
  >远程访问的。 User代表的是访问该数据库的用户名。在MySQL中需要通过Host和User来唯一标识一个用户
  
- 创建用户

  ```sql
  CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';
  ```

- 修改用户密码

  ```sql
  ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码' ;
  ```

- 删除用户

  ```sql
  DROP USER '用户名'@'主机名' ;
  ```

  > 注意事项:
  >
  > - 在MySQL中需要通过用户名@主机名的方式，来唯一标识一个用户
  > - 主机名可以使用 % 通配
  > - 这类SQL开发人员操作的比较少，主要是DBA（ Database Administrator 数据库
  >   管理员）使用

#### 权限控制


