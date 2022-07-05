---
title: "mysql学习之基础篇-2-约束、多表查询、事物"
date: 2022-07-03T20:50:39+08:00
draft: false
categories: ["mysql"]
tags: ["mysql","基础"]
---

## 约束

### 概述

概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

目的：保证数据库中数据的正确、有效性和完整性。

分类:

| 约束                     | 描述                                                     | 关键字      |
| ------------------------ | -------------------------------------------------------- | ----------- |
| 非空约束                 | 限制该字段的数据不能为null                               | NOT NULL    |
| 唯一约束                 | 保证该字段的所有数据都是唯一、不重复的                   | UNIQUE      |
| 主键约束                 | 主键是一行数据的唯一标识，要求非空且唯一                 | PRIMARY KEY |
| 默认约束                 | 保存数据时，如果未指定该字段的值，则采用默认值           | DEFAULT     |
| 检查约束(8.0.16版本之后) | 保证字段值满足某一个条件                                 | CHECK       |
| 外键约束                 | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性 | FOREIGN KEY |

>**注意：约束是作用于表中字段上的，可以在创建表/修改表的时候添加约束**

### 建表约束案例

{{<admonition question "根据需求，完成表结构的创建。需求如下：" true>}}

| 字段名 | 字段含义   | 字段类型    | 约束条件                  | 约束关键字                  |
| ------ | ---------- | ----------- | ------------------------- | --------------------------- |
| id     | ID唯一标识 | int         | 主键，并且自动增长        | PRIMARY KEY, AUTO_INCREMENT |
| name   | 姓名       | varchar(10) | 不为空，并且唯一          | NOT NULL , UNIQUE           |
| age    | 年龄       | int         | 大于0，并且小于等于120    | CHECK                       |
| status | 状态       | char(1)     | 如果没有指定该值，默认为1 | DEFAULT                     |
| gender | 性别       | char(1)     | 无                        |                             |

{{</admonition>}}

**对应的建表语句为:**

```sql
CREATE TABLE tb_user(
    id int AUTO_INCREMENT PRIMARY KEY  COMMENT  'ID唯一标识',
    name varchar(10) NOT NULL UNIQUE  COMMENT  '姓名' ,
    age int check (age > 0 && age <= 120)  COMMENT  '年龄' ,
    status char(1) default  '1'  COMMENT  '状态',
    gender char(1)  COMMENT  '性别'
);
```

> 在为字段添加约束时，我们只需要在字段之后加上约束的关键字即可，需要关注其语法

### 外键约束

**外键：用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性**

#### 语法

- **添加外键**

  ```sql
  -- 建表时添加
  CREATE TABLE 表名(
      字段名    数据类型,
      ...
      [CONSTRAINT]   [外键名称]  FOREIGN  KEY (外键字段名)   REFERENCES   主表 (主表列名) 
  );
  
  -- 修改表结构时添加
  ALTER   TABLE  表名   ADD  CONSTRAINT   外键名称   FOREIGN   KEY (外键字段名)
  REFERENCES  主表 (主表列名) ;
  ```

  **案例: 为emp表的dept_id字段添加外键约束,关联dept表的主键id**

  ```sql
  alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references 
  dept(id);
  ```

- **删除外键**

  ```sql
  ALTER   TABLE  表名   DROP  FOREIGN  KEY  外键名称;
  ```

  **案例：删除emp表的外键fk_emp_dept_id**

  ```sql
  alter table emp drop foreign key fk_emp_dept_id;
  ```

#### 删除/更新行为

添加了外键之后，再删除父表数据时产生的约束行为，我们就称为删除/更新行为。具体的删除/更新行
为有以下几种：

| 行为        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| NO ACTION   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 RESTRICT 一致) 默认行为 |
| RESTRICT    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有则不允许删除/更新。 (与 NO ACTION 一致) 默认行为 |
| CASCADE     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除/更新外键在子表中的记录 |
| SET NULL    | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有则设置子表中该外键值为null（这就要求该外键允许取null） |
| SET DEFAULT | 父表有变更时，子表将外键列设置成一个默认的值 (Innodb不支持)  |

具体语法为：

```sql
ALTER TABLE  表名  ADD CONSTRAINT  外键名称  FOREIGN KEY  (外键字段)   REFERENCES   
主表名 (主表字段名)   ON UPDATE CASCADE ON DELETE CASCADE;
```

## 多表查询

### 多表关系

项目开发中，在进行数据库表结构设计时，会根据业务需求及业务模块之间的关系，分析并设计表结
构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：

- **一对多(多对一)**

  - 案例: 部门 与 员工的关系
  - 关系: 一个部门对应多个员工，一个员工对应一个部门
  - 实现: 在多的一方建立外键，指向一的一方的主键

- **多对多**

  - 案例: 学生 与 课程的关系

  - 关系: 一个学生可以选修多门课程，一门课程也可以供多个学生选择

  - 实现: 建立第三张中间表，中间表至少包含两个外键，分别关联两方主键

- **一对一**

  - 案例: 用户 与 用户详情的关系

  - **关系: 一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率**

  - 实现: 在任意一方加入外键，关联另外一方的主键，并且设置外键为唯一的(UNIQUE)

### 连接查询

- **内连接：相当于查询A、B交集部分数据**
- **外连接：**
  - **左外连接：查询左表所有数据，以及两张表交集部分数据**
  - **右外连接：查询右表所有数据，以及两张表交集部分数据**
  - **自连接：当前表与自身的连接查询，自连接必须使用表别名**

### 内连接

**内连接的语法分为两种: 隐式内连接、显式内连接**

- **隐式内连接**

  ```sql
  SELECT  字段列表   FROM   表1 , 表2   WHERE   条件 ... ;
  ```

- **显式内连接**

  ```sql
  SELECT  字段列表   FROM   表1  [ INNER ]  JOIN 表2  ON  连接条件 ... ;
  ```

{{<admonition tip "案例A: 隐式内连接实现" false>}}

```sql
-- A. 查询每一个员工的姓名 , 及关联的部门的名称 (隐式内连接实现)
-- 表结构: emp , dept
-- 连接条件: emp.dept_id = dept.id

select emp.name , dept.name from emp , dept where emp.dept_id = dept.id ;

-- 为每一张表起别名,简化SQL编写
select e.name,d.name from emp e , dept d where e.dept_id = d.id;
```

{{</admonition >}}

{{<admonition tip "案例B: 显式内连接实现" false>}}

```sql
-- B. 查询每一个员工的姓名 , 及关联的部门的名称 (显式内连接实现) 
-- INNER JOIN ... ON ...
-- 表结构: emp , dept
-- 连接条件: emp.dept_id = dept.id

select e.name, d.name from emp e inner join dept d  on e.dept_id = d.id;

-- 为每一张表起别名,简化SQL编写
select e.name, d.name from emp e join dept d  on e.dept_id = d.id;
```

{{</admonition >}}

> 注意事项: 一旦为表起了别名，就不能再使用表名来指定对应的字段了，此时只能够使用别名来指定字段。

### 外连接

**外连接分为两种，分别是：左外连接 和 右外连接**

- **左外连接**

  ```sql
  SELECT  字段列表   FROM   表1  LEFT  [ OUTER ]  JOIN 表2  ON  条件 ... ;
  ```

  > 左外连接相当于查询表1(左表)的所有数据，当然也包含表1和表2交集部分的数据

- **右外连接**

  ```sql
  SELECT  字段列表   FROM   表1  RIGHT  [ OUTER ]  JOIN 表2  ON  条件 ... ;
  ```

  > 右外连接相当于查询表2(右表)的所有数据，当然也包含表1和表2交集部分的数据

{{<admonition tip "案例A: 左外连接" false>}}

```sql
-- A. 查询emp表的所有数据, 和对应的部门信息 
-- 由于需求中提到，要查询emp的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询。
-- 表结构: emp, dept
-- 连接条件: emp.dept_id = dept.id

select e.*, d.name from emp e left outer join dept d on e.dept_id = d.id;

select e.*, d.name from emp e left join dept d on e.dept_id = d.id;
```

{{</admonition >}}

{{<admonition tip "案例B: 右外连接" false>}}

```sql
-- B. 查询dept表的所有数据, 和对应的员工信息(右外连接)
-- 由于需求中提到，要查询dept表的所有数据，所以是不能内连接查询的，需要考虑使用外连接查询
-- 表结构: emp, dept
-- 连接条件: emp.dept_id = dept.id

select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id;

select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id
```

{{</admonition >}}

> 注意事项：**左外连接和右外连接是可以相互替换的，只需要调整在连接查询时SQL中，表结构的先后顺**
> **序就可以了。而我们在日常开发使用时，更偏向于左外连接。**

### 自连接

**自连接查询，顾名思义，就是自己连接自己，也就是把一张表连接查询多次。**

- **语法**

  ```sql
  SELECT  字段列表   FROM   表A   别名A   JOIN  表A    别名B   ON  条件 ... ;
  ```

  > 而对于自连接查询，可以是内连接查询，也可以是外连接查询

{{<admonition tip "案例: 自连接" false>}}

```sql
-- A. 查询员工 及其 所属领导的名字
-- 表结构: emp

select a.name , b.name from emp a , emp b where a.managerid = b.id;

-- B. 查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来
-- 表结构: emp a , emp b

select a.name '员工', b.name '领导' from emp a left join emp b on a.managerid = 
b.id;
```

{{</admonition >}}

> 注意事项:  **在自连接查询中，必须要为表起别名，要不然我们不清楚所指定的条件、返回的字段，到底**
> **是哪一张表的字段**

### 联合查询

**对于union查询，就是把多次查询的结果合并起来，形成一个新的查询结果集**

```sql
SELECT  字段列表   FROM   表A  ...  
UNION [ ALL ]
SELECT  字段列表  FROM   表B  ....
```

- **对于联合查询的多张表的列数必须保持一致，字段类型也需要保持一致**
- **union all 会将全部的数据直接合并在一起，union 会对合并之后的数据去重**

{{<admonition tip "案例: 联合查询" false>}}

```sql
-- A. 将薪资低于 5000 的员工 , 和 年龄大于 50 岁的员工全部查询出来.
-- 当前对于这个需求，我们可以直接使用多条件查询，使用逻辑运算符 or 连接即可。 那这里呢，我们
-- 也可以通过union/union all来联合查询

select * from emp where salary < 5000
union all
select * from emp where age > 50;

-- 去重
select * from emp where salary < 5000
union 
select * from emp where age > 50;
```

{{</admonition >}}

>**注意： 如果多条查询语句查询出来的结果，字段数量不一致，在进行union/union all联合查询时，将会报错**

### 子查询

**SQL语句中嵌套SELECT语句，称为嵌套查询，又称子查询**

例如：`SELECT  *  FROM   t1   WHERE  column1 =  ( SELECT  column1  FROM  t2 );`

- **标量子查询（子查询结果为单个值）**

  **子查询返回的结果是单个值（数字、字符串、日期等），最简单的形式，这种子查询称为标量子查询。**
  **常用的操作符：=  <>  >   >=   <  <=**

  ```sql
  -- 查询 "销售部" 的所有员工信息
  select * from emp where dept_id = (select id from dept where name = '销售部');
  ```

- **列子查询(子查询结果为一列)**

  **子查询返回的结果是一列（可以是多行），这种子查询称为列子查询。**

  **常用的操作符：IN 、NOT IN 、 ANY 、SOME 、 ALL**

  | 操作符 | 描述                                   |
  | ------ | -------------------------------------- |
  | IN     | 在指定的集合范围之内，多选一           |
  | NOT IN | 不在指定的集合范围之内                 |
  | ANY    | 子查询返回列表中，有任意一个满足即可   |
  | SOME   | 与ANY等同，使用SOME的地方都可以使用ANY |
  | ALL    | 子查询返回列表的所有值都必须满足       |

  ```sql
  -- 查询 "销售部" 和 "市场部" 的所有员工信息
  select * from emp where dept_id in (select id from dept where name = '销售部' or 
  name = '市场部');
  ```

- **行子查询(子查询结果为一行)**

  **子查询返回的结果是一行（可以是多列），这种子查询称为行子查询**

  **常用的操作符：= 、<> 、IN 、NOT IN**

  ```sql
  -- 查询与 "张无忌" 的薪资及直属领导相同的员工信息 ;
  -- 这个需求同样可以拆解为两步进行:
  -- 1. 查询 "张无忌" 的薪资及直属领导
  select salary, managerid from emp where name = '张无忌';
  -- 2. 查询与 "张无忌" 的薪资及直属领导相同的员工信息 
  select * from emp where (salary,managerid) = (select salary, managerid from emp 
  where name = '张无忌');
  ```

- **表子查询(子查询结果为多行多列)**

  **子查询返回的结果是多行多列，这种子查询称为表子查询**

  **常用的操作符：IN**

  ```sql
  -- 案例1 
  -- 查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息
  -- 分解为两步执行
  -- 1. 查询 "鹿杖客" , "宋远桥" 的职位和薪资
  select job, salary from emp where name = '鹿杖客' or name = '宋远桥';
  -- 2. 查询与 "鹿杖客" , "宋远桥" 的职位和薪资相同的员工信息
  select * from emp where (job,salary) in ( select job, salary from emp where name = 
  '鹿杖客' or name = '宋远桥' );
  
  -- 案例2
  -- 查询入职日期是 "2006-01-01" 之后的员工信息 , 及其部门信息
  -- 1. 入职日期是 "2006-01-01" 之后的员工信息
  select * from emp where entrydate > '2006-01-01';
  -- 2. 查询这部分员工, 对应的部门信息
  select e.*, d.* from (select * from emp where entrydate > '2006-01-01') e left 
  join dept d on e.dept_id = d.id ;
  ```

### 多表查询案例

**这里主要涉及到的表就三张：emp员工表、dept部门表、salgrade薪资等级表**

**表结构代码如下：**

```sql
create table test2.dept
(
    id   int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '部门名称'
)comment '部门表';

create table test2.emp
(
    id        int auto_increment comment 'ID'  primary key,
    name      varchar(50) not null comment '姓名',
    age       int         null comment '年龄',
    job       varchar(20) null comment '职位',
    salary    int         null comment '薪资',
    entrydate date        null comment '入职时间',
    managerid int         null comment '直属领导ID',
    dept_id   int         null comment '部门ID',
    constraint fk_emp_dept_id foreign key (dept_id) references test2.dept (id)
)comment '员工表';

create table test2.salgrade
(
    grade int null,
    losal int null,
    hisal int null
)comment '薪资等级表';

```

**案例**

```sql
-- 1. 查询员工的姓名、年龄、职位、部门信息 （隐式内连接）
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id

select e.name, e.age, e.job, d.name
from emp e,
     dept d
where e.dept_id = d.id;



-- 2. 查询年龄小于30岁的员工的姓名、年龄、职位、部门信息（显式内连接）
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id
select e.name, e.age, e.job, d.name
from emp e
         inner join dept d on e.dept_id = d.id
where e.age < 30;


-- 3. 查询拥有员工的部门ID、部门名称
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id
select distinct d.id, d.name
from emp e,
     dept d
where e.dept_id = d.id;

-- 4. 查询所有年龄大于40岁的员工, 及其归属的部门名称; 如果员工没有分配部门, 也需要展示出来
-- 表: emp , dept
-- 连接条件: emp.dept_id = dept.id
-- 外连接
select e.*, d.name
from emp e
         left join dept d on e.dept_id = d.id
where e.age > 40;


-- 5. 查询所有员工的工资等级
-- 表: emp , salgrade
-- 连接条件 : emp.salary >= salgrade.losal and emp.salary <= salgrade.hisal
select e.name, e.salary, s.grade
from emp e,
     salgrade s
where e.salary between s.losal and s.hisal;

-- 6. 查询 "研发部" 所有员工的信息及 工资等级
-- 表: emp , salgrade , dept
-- 连接条件 : emp.salary between salgrade.losal and salgrade.hisal , emp.dept_id = dept.id
-- 查询条件 : dept.name = '研发部'
select e.name, s.grade, d.name
from emp e,
     salgrade s,
     dept d
where e.salary between s.losal and s.hisal
  and e.dept_id = d.id
  and d.name = "研发部";


-- 7. 查询 "研发部" 员工的平均工资
-- 表: emp , dept
-- 连接条件 :  emp.dept_id = dept.id
select avg(e.salary)
from emp e
         inner join dept d on e.dept_id = d.id
where d.name = "研发部";

-- 8. 查询工资比 "灭绝" 高的员工信息。
-- a. 查询 "灭绝" 的薪资
select salary
from emp
where name = "灭绝";

-- b. 查询比她工资高的员工数据
select *
from emp
where salary > (select salary from emp where name = "灭绝");


-- 9. 查询比平均薪资高的员工信息
-- a. 查询员工的平均薪资
select avg(salary)
from emp;

-- b. 查询比平均薪资高的员工信息
select *
from emp
where salary > (select avg(salary) from emp);


-- 10. 查询低于本部门平均工资的员工信息

-- a. 查询指定部门平均薪资  1
select avg(e.salary), d.name
from emp e
         inner join dept d on e.dept_id = d.id
where d.id = 1;

select avg(e.salary), d.name
from emp e
         inner join dept d on e.dept_id = d.id
group by d.id;

-- b. 查询低于本部门平均工资的员工信息
select *
from emp e1
where e1.salary < (select avg(e.salary)
                   from emp e
                   where e.dept_id = e1.dept_id);

-- 11. 查询所有的部门信息, 并统计部门的员工人数
select d.*,count(*) "员工人数" from emp e ,dept d where e.dept_id=d.id group by d.id;

# select d.*,count(*) "员工人数" from emp e left join dept d on e.dept_id=d.id group by d.id;

```

## 事务

**事务 是一组操作的集合，它是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系**
**统提交或撤销操作请求，即这些操作要么同时成功，要么同时失败**

### 控制事务一

- **查看/设置事务提交方式**

  ```sql
  SELECT  @@autocommit ;
  SET   @@autocommit = 0 ;
  ```

- **提交事务**

  ```sql
  COMMIT;
  ```

- **回滚事务**

  ```sql
  ROLLBACK;
  ```

  >**注意：上述的这种方式，我们是修改了事务的自动提交行为, 把默认的自动提交修改为了手动提**
  >**交, 此时我们执行的DML语句都不会提交, 需要手动的执行commit进行提交。**

### 控制事务二

- **开启事务**

  ```sql
  START  TRANSACTION   或  BEGIN ;
  ```

- **提交事务**

  ```sql
  COMMIT;
  ```

- **回滚事务**

  ```sql
  ROLLBACK;
  ```

### 事务四大特性

- **原子性（Atomicity）：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。**
- **一致性（Consistency）：事务完成时，必须使所有的数据都保持一致状态。**
- **隔离性（Isolation）：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。**
- **持久性（Durability）：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。**

### 并发事务问题

- **赃读：一个事务读到另外一个事务还没有提交的数据。**

  **比如事务B读取到了事务A未提交的数据。**

- **不可重复读：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读**

  **事务A两次读取同一条记录，但是读取到的数据却是不一样的。**

- **幻读：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据**
  **已经存在，好像出现了 "幻影"。**

### 事务隔离级别

为了解决并发事务所引发的问题，在数据库中引入了事务隔离级别。主要有以下几种：

| 隔离级别              | 脏读 | 不可重复读 | 幻读 |
| --------------------- | ---- | ---------- | ---- |
| Read uncommitted      | √    | √          | √    |
| Read committed        | ×    | √          | √    |
| Repeatable Read(默认) | ×    | ×          | √    |
| Serializable          | ×    | ×          | ×    |

- 查看事务隔离级别

  ```sql
  SELECT @@TRANSACTION_ISOLATION;
  ```

- 设置事务隔离级别

  ```sql
  SET  [ SESSION | GLOBAL ]  TRANSACTION  ISOLATION  LEVEL  { READ UNCOMMITTED | 
  READ COMMITTED | REPEATABLE READ | SERIALIZABLE }
  ```

  -

  ```sql
  select @@transaction_isolation;
  
  set session transaction isolation level read committed ;
  set session transaction isolation level repeatable read ;
  ```

> **注意：事务隔离级别越高，数据越安全，但是性能越低。**
