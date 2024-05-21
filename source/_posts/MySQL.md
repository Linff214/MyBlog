---
title: MySQL
date: 2024-05-15 15:48:58
tags: 数据库
---

# 1.数据库与数据库管理系统

## 1.1数据库的相关概念

### 1.1.1为什么要使用数据库

* <font color='red'>持久化(persistence)：</font>把数据保存到可掉电式存储设备中以供之后使用。大多数情况下，特别是企业级应用，数据持久化意味着将内存中的数据保存到硬盘上加以”固化”，而持久化的实现过程大多通过各种关系数据库来完成。
* 持久化的主要作用是将内存中的数据存储在关系型数据库中，当然也可以存储在磁盘文件、XML数据文件中。

### 1.1.2数据库的相关概念

| <font color='red'>DB：数据库（Database）</font>              |
| ------------------------------------------------------------ |
| 即存储数据的“仓库”，其本质是一个文件系统。它保存了一系列有组织的数据。 |
| <font color='red'>**DBMS：数据库管理系统（Database Management System）**</font> |
| 是一种操纵和管理数据库的大型软件，用于建立、使用和维护数据库，对数据库进行统一管理和控制。用户通过数据库管理系统访问数据库中表内的数据。 |
| <font color='red'>**SQL：结构化查询语言（Structured Query Language）**</font> |
| 专门用来与数据库通信的语言。                                 |

![image-20240517163044491](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517163044491.png)

#### RDBMS与非RDBMS

* **<font color='red'>关系型数据库模型</font>**是把复杂的数据结构归结为简单的二元关系 （即二维表格形式）。

  ![image-20240517170210059](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517170210059.png)

* 关系型数据库以 行(row) 和 列(column) 的形式存储数据，以便于用户理解。这一系列的行和列被称为表(table)，一组表组成了一个库(database)。
* 表与表之间的数据记录有关系(relationship)。现实世界中的各种实体以及实体之间的各种联系均用关系模型 来表示。关系型数据库，就是建立在关系模型基础上的数据库。
* SQL 就是关系型数据库的查询语言。

![image-20240517171711136](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517171711136.png)

* 非关系型数据库，可看成传统关系型数据库的功能 阉割版本 ，基于键值对存储数据，不需要经过SQL层的解析， 性能非常高 。同时，通过减少不常用的功能，进一步提高性能。  

* #### 非关系型数据库的类型

  * <font color='red'>**键值型数据库**</font>

    键值型数据库通过 **Key-Value** 键值的方式来存储数据，其中 Key 和 Value 可以是简单的对象，也可以是复杂的对象。Key 作为唯一的标识符，优点是查找速度快，在这方面明显优于关系型数据库，缺点是无法像关系型数据库一样使用条件过滤（比如 WHERE），如果你不知道去哪里找数据，就要遍历所有的键，这就会消耗大量的计算。

    键值型数据库典型的使用场景是作为**内存缓存** 。 <font color='red'>**Redis** </font>是最流行的键值型数据库

  * <font color='red'>**文档型数据库**</font>

    此类数据库可存放并获取文档，可以是XML、JSON等格式。在数据库中文档作为处理信息的基本单位，一个文档就相当于一条记录。<font color='purple'>**文档数据库所存放的文档，就相当于键值数据库所存放的“值”**</font>。MongoDB是最流行的文档型数据库。此外，还有CouchDB

    等。

  * <font color='red'>**搜索引擎数据库**</font>

    <font color='purple'>**虽然关系型数据库采用了索引提升检索效率，但是针对全文索引效率却较低**</font>。搜索引擎数据库是应用在搜索引擎领域的数据存储形式，由于搜索引擎会爬取大量的数据，并以特定的格式进行存储，这样在检索的时候才能保证性能最优。核心原理是“倒排索引”。

    典型产品：Solr、Elasticsearch、Splunk 等。 

  * <font color='red'>**列式数据库**</font>

    列式数据库是相对于行式存储的数据库（即关系型数据库），Oracle、MySQL、SQL Server 等数据库都是采用的行式存储（Row-based），而列式数据库是将数据按照列存储到数据库中，**<font color='purple'>这样做的好处是可以大量降低系统的I/O，适合于分布式文件系统，不足在于功能相对有限。典型产品：HBase等。</font>**

    ![image-20240517192209745](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517192209745.png)

    * 要读来自于IO的数据，即直接从硬盘加载到内存中的，跟这个数据本身就在内存中相比，前者速度要慢得多得多。所以说有过多的冗余字段采用这种行式存储的方式容易字段过多，占用内存当然就多了，也就意味着我们加载更多的冗余字段进来了，就意味着有可能会出现过多的IO，那么这时候性能肯定就不会高。

* **图形数据库**  

NoSQL 对 SQL 做出了很好的补充，比如实际开发中，有很多业务需求，其实并不需要完整的关系型数据库功能，非关系型数据库的功能就足够使用了。这种情况下，使用 性能更高 、 成本更低 的非关系型数据库当然是更明智的选择。比如：日志收集、排行榜、定时器等。  

## 1.2 关系型数据库设计规则

* 关系型数据库的典型数据结构就是`数据表`，这些数据表的组成都是结构化的（Structured)。
* 将数据放到表中，表再放到库中。
* 一个数据库中可以有多个表，每个表都有一个名字，用来标识自己。表名具有唯一性。
* 表具有一些特性，这些特性定义了数据在表中如何存储，类似Java和Python中“类”的设计。

#### 1.2.1 记录、字段

* E-R (entity-relationship，实体-联系）模型中有三个主要概念是:`实体集、属性、联系集`。

* 一个实体集（class）对应于数据库中的一个表(table)，一个实体(instance)则对应于数据库表中的一行(row)，也称为一条记录(record)。一个属性(attribute)对应于数据库表中的一列(column)，也称为一个字段(field)。

  `库可以看作data中的文件夹，表可以看作文件夹中的文件`

```
ORM思想，即对象关系映射，Object Relational Mapping
数据库中的一个表 <---> Java或Python中的一个类
表中的一条数据 <---> 类中的一个对象（或实体）
表中的一个列 <----> 类中的一个字段、属性(field)
```

![image-20240517203410449](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517203410449.png)

#### 1.2.2表的关联关系 

* 表与表之间的数据记录有关系(relationship)。现实世界中的各种实体以及实体之间的各种联系均用关系模型来表示。
* 四种：一对一关联、一对多关联、多对多关联、自我引用

**一对一关联（one-to-one）**

* 在实际的开发中应用不多，因为一对一可以创建成一张表。
* 举例：设计学生表 ：学号、姓名、手机号码、班级、系别、身份证号码、家庭住址、籍贯、紧急联系人、...
  * 拆为两个表：两个表的记录是一一对应关系。
  * 基础信息表 (常用信息）：学号、姓名、手机号码、班级、系别
  * 档案信息表 （不常用信息）：学号、身份证号码、家庭住址、籍贯、紧急联系人、...
* 两种建表原则：
  * <font color='green'>外键唯一：</font>主表的主键和从表的外键（唯一），形成主外键关系，外键唯一。
  * <font color='green'>外键是主键：</font>主表的主键和从表的主键，形成主外键关系。

**一对多关系（one-to-many****）**

常见实例场景:`客户表和订单表 ，分类表和商品表 ，部门表和员工表 。`

举例：员工表：编号、姓名、...、所属部门

部门表：编号、名称、简介

<font color='orange'>**一对多建表原则**</font>：在从表(多方)创建一个字段，字段作为外键指向主表(一方)的主键![image-20240517220612575](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517220612575.png)

**多对多**

要表示多对多关系，必须创建第三个表，该表通常称为 联接表 ，它将多对多关系划分为两个一对多关系。将这两个表的主键都插入到第三个表中。  

![image-20240517220928821](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517220928821.png)

**自我引用(Self reference)**  

![image-20240517221824736](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240517221824736.png)

#  2.基本的SELECT语句

## 2.1 SQL的分类

```sql
DDL:数据定义语言。CREATE \ ALTER \ DROP \ RENAME \ TRUNCATE
DML:数据操作语言。INSERT \ DELETE \ UPDATE \ SELECT （重中之重）
DCL:数据控制语言。COMMIT \ ROLLBACK \ SAVEPOINT \ GRANT \ REVOKE
```

## 2.2SQL语言的规则和规范

  2.1 SQL的规则 ----必须要遵守
- SQL 可以写在一行或者多行。为了提高可读性，各子句分行写，必要时使用缩进
- 每条命令以 ; 或 \g 或 \G 结束
- 关键字不能被缩写也不能分行
- 关于标点符号
  - 必须保证所有的()、单引号、双引号是成对结束的
  - 必须使用英文状态下的半角输入方式
  - 字符串型和日期时间类型的数据可以使用单引号（' '）表示
  - 列的别名，尽量使用双引号（" "），而且不建议省略as

2.2 SQL的规范  ----建议遵守
- MySQL 在 Windows 环境下是大小写不敏感的
- MySQL 在 Linux 环境下是大小写敏感的
  - 数据库名、表名、表的别名、变量名是严格区分大小写的
  - 关键字、函数名、列名(或字段名)、列的别名(字段的别名) 是忽略大小写的。
- 推荐采用统一的书写规范：
  - 数据库名、表名、表别名、字段名、字段别名等都小写
  - SQL 关键字、函数名、绑定变量等都大写

## 2.3MySQL的三种注释的方式

```sql
/*
USE dbtest2;

-- 这是一个查询语句
SELECT * FROM emp;

INSERT INTO emp 
VALUES(1002,'Tom'); #字符串、日期时间类型的变量需要使用一对''表示

INSERT INTO emp 
VALUES(1003,'Jerry');

SELECT * FROM emp\G

SHOW CREATE TABLE emp\g

/*
```

## 2.4 导入现有的数据表、表的数据。

方式1：source 文件的全路径名
`举例：source d:\atguigudb.sql;`

方式2：基于具体的图形化界面的工具可以导入数据
比如：`SQLyog中 选择 “工具” -- “执行sql脚本” -- 选中xxx.sql即可。`

#5. 最基本的SELECT语句： SELECT 字段1,字段2,... FROM 表名 
SELECT 1 + 1,3 * 2;

SELECT 1 + 1,3 * 2
FROM DUAL; #dual：伪表

## 2.5 表中的所有的字段（或列）
SELECT * FROM employees;

SELECT employee_id,last_name,salary
FROM employees;

## 2.6 列的别名

`as:全称：alias(别名),可以省略`

`列的别名可以使用一对""引起来，不要使用''。`

SELECT employee_id emp_id,last_name AS lname,department_id "部门id",salary * 12 AS "annual sal"
FROM employees;

## 2.7 去除重复行
````sql
#查询员工表中一共有哪些部门id呢？
#错误的:没有去重的情况
SELECT department_id
FROM employees;
#正确的：去重的情况
SELECT DISTINCT department_id
FROM employees;
```

#错误的：
SELECT salary,DISTINCT department_id
FROM employees;

#仅仅是没有报错，但是没有实际意义。
SELECT DISTINCT department_id,salary
FROM employees;
````



## 2.8.空值参与运算

```sql
1. 空值：null

2. null不等同于0，''，'null'
SELECT * FROM employees;

#3. 空值参与运算：结果一定也为空。
SELECT employee_id,salary "月工资",salary * (1 + commission_pct) * 12 "年工资",commission_pct
FROM employees;

#实际问题的解决方案：引入IFNULL
SELECT employee_id,salary "月工资",salary * (1 + IFNULL(commission_pct,0)) * 12 "年工资",commission_pct

FROM `employees`;
```



## 2.9. 着重号 ``

```sql 
SELECT * FROM `order`;
```



## 2.10 查询常数

SELECT '尚硅谷',123,employee_id,last_name
FROM employees;

## 2.11.显示表结构

```sql
DESCRIBE employees; #显示了表中字段的详细信息

DESC employees;

DESC departments;
```



## 2.12.过滤数据

```sql
#练习：查询90号部门的员工信息
SELECT * 
FROM employees
#过滤条件WHERE,声明在FROM结构的后面
WHERE department_id = 90; 

#练习：查询last_name为'King'的员工信息
SELECT * 
FROM EMPLOYEES
WHERE LAST_NAME = 'King'; 
```

# 3.运算符
## 1.算术运算符： +  -  *  /  div  % mod

```sql
SELECT 100, 100 + 0, 100 - 0, 100 + 50, 100 + 50 * 30, 100 + 35.5, 100 - 35.5 
FROM DUAL;

#在SQL中，+没有连接的作用，就表示加法运算。此时，会将字符串转换为数值（隐式转换）

SELECT 100 + '1'  # 在Java语言中，结果是：1001。 
FROM DUAL;

SELECT 100 + 'a' #此时将'a'看做0处理
FROM DUAL;

SELECT 100 + NULL  # null值参与运算，结果为null
FROM DUAL;

SELECT 100, 100 * 1, 100 * 1.0, 100 / 1.0, 100 / 2, #一般情况下除法是除不尽的，所有SQL默认除法的结果是小
100 + 2 * 5 / 2,100 / 3, 100 DIV 0  # 分母如果为0，则结果为null
FROM DUAL;

#取模运算： % (mod) 结果的符号与被模数一致，被模数%模数

SELECT 12 % 3,12 % 5, 12 MOD -5,-12 % 5,-12 % -5
FROM DUAL;

#练习：查询员工id为偶数的员工信息
SELECT employee_id,last_name,salary
FROM employees
WHERE employee_id % 2 = 0;
```



## 2. 比较运算符

```sql
#2.1 =  <=>  <> !=  <  <=  >  >= 

= 的使用

SELECT 1 = 2,1 != 2,1 = '1',1 = 'a',0 = 'a' #字符串存在隐式转换。如果转换数值不成功，则看做0
FROM DUAL;

SELECT 'a' = 'a','ab' = 'ab','a' = 'b' #两边都是字符串的话，则按照ANSI的比较规则进行比较。
FROM DUAL;

SELECT 1 = NULL,NULL = NULL # 只要有null参与判断，结果就为null
FROM DUAL;

SELECT last_name,salary,commission_pct
FROM employees
#where salary = 6000;
WHERE commission_pct = NULL;  #此时执行，不会有任何的结果

<=>或!= ：安全等于。 记忆技巧：为NULL而生。

SELECT 1 <=> 2,1 <=> '1',1 <=> 'a',0 <=> 'a'
FROM DUAL;

SELECT 1 <=> NULL, NULL <=> NULL
FROM DUAL;

#练习：查询表中commission_pct为null的数据有哪些
SELECT last_name,salary,commission_pct
FROM employees
WHERE commission_pct <=> NULL;

SELECT 3 <> 2,'4' <> NULL, '' != NULL,NULL != NULL
FROM DUAL;
```



## 3

#① **IS NULL \ IS NOT NULL \ ISNULL**
<font color='green'>#练习：查询表中commission_pct为null的数据有哪些</font>
`SELECT last_name,salary,commission_pct`
`FROM employees`
`WHERE commission_pct IS NULL;`
<font color='green'>#或</font>
`SELECT last_name,salary,commission_pct`
`FROM employees`
`WHERE ISNULL(commission_pct);`

<font color='green'>#练习：查询表中commission_pct不为null的数据有哪些</font>

```sql
SELECT last_name,salary,commission_pct
FROM employees
WHERE commission_pct IS NOT NULL;
```

```SQL
#或
SELECT last_name,salary,commission_pct
FROM employees
WHERE NOT commission_pct <=> NULL;
```

**#② LEAST() \ GREATEST** 最小最大

```SQL
SELECT LEAST('g','b','t','m'),GREATEST('g','b','t','m')
FROM DUAL;

SELECT LEAST(first_name,last_name),LEAST(LENGTH(first_name),LENGTH(last_name))
FROM employees;
```

**#③ BETWEEN （条件下界1） AND （条件上界2）  （查询条件1和条件2范围内的数据，包含边界）**
<font color='green'>#查询工资在6000 到 8000的员工信息</font>

```SQL
SELECT employee_id,last_name,salary
FROM employees
#where salary between 6000 and 8000;
WHERE salary >= 6000 && salary <= 8000;
```

<font color='green'>#交换6000 和 8000之后，查询不到数据</font>

```SQL
SELECT employee_id,last_name,salary
FROM employees
WHERE salary BETWEEN 8000 AND 6000;
```

<font color='green'>#查询工资不在6000 到 8000的员工信息</font>

```SQL
SELECT employee_id,last_name,salary
FROM employees
WHERE salary NOT BETWEEN 6000 AND 8000;
#where salary < 6000 or salary > 8000;
```

**#④ in (set)\ not in (set)**离散查找

<font color='green'>#练习：查询部门为10,20,30部门的员工信息</font>

```SQL
SELECT last_name,salary,department_id
FROM employees
#where department_id = 10 or department_id = 20 or department_id = 30;
WHERE department_id IN (10,20,30);
```

<font color='green'>#练习：查询工资不是6000,7000,8000的员工信息</font>

```SQL
SELECT last_name,salary,department_id
FROM employees
WHERE salary NOT IN (6000,7000,8000);
```

**<font color='green'>#⑤ LIKE :模糊查询</font>**

**<font color='green'>% : 代表不确定个数的字符 （0个，1个，或多个）</font>**

<font color='green'>#练习：查询last_name中包含字符'a'的员工信息</font>

```SQL
SELECT last_name
FROM employees
WHERE last_name LIKE '%a%';
```

<font color='green'>#练习：查询last_name中以字符'a'开头的员工信息</font>

```SQL
SELECT last_name
FROM employees
WHERE last_name LIKE 'a%';
```

<font color='green'>#练习：查询last_name中包含字符'a'且包含字符'e'的员工信息</font>

```SQL
#写法1：
SELECT last_name
FROM employees
WHERE last_name LIKE '%a%' AND last_name LIKE '%e%';
#写法2：
SELECT last_name
FROM employees
WHERE last_name LIKE '%a%e%' OR last_name LIKE '%e%a%';
```

**<font color='green'>_ ：代表一个不确定的字符</font>**

```sql
#练习：查询第3个字符是'a'的员工信息
SELECT last_name
FROM employees
WHERE last_name LIKE '__a%';

#练习：查询第2个字符是_且第3个字符是'a'的员工信息
#需要使用转义字符: \ 
SELECT last_name
FROM employees
WHERE last_name LIKE '_\_a%';

#或者  (了解)ESCAPE '$'表示$为转义
SELECT last_name
FROM employees
WHERE last_name LIKE '_$_a%' ESCAPE '$';
```

**#⑥ REGEXP \ RLIKE :正则表达式**

```sql
SELECT 'shkstart' REGEXP '^shk', 'shkstart' REGEXP 't$', 'shkstart' REGEXP 'hk'
FROM DUAL;

SELECT 'atguigu' REGEXP 'gu.gu','atguigu' REGEXP '[ab]'
FROM DUAL;
```

## 3. 逻辑运算符： OR ||  AND && NOT ! XOR

**or  and**  

```sql
SELECT last_name,salary,department_id
FROM employees
#where department_id = 10 or department_id = 20;
#where department_id = 10 and department_id = 20;
WHERE department_id = 50 AND salary > 6000;
```

**not** 

```sql
SELECT last_name,salary,department_id
FROM employees
#where salary not between 6000 and 8000;
#where commission_pct is not null;
WHERE NOT commission_pct <=> NULL;
```

**XOR :追求的"异"**

```sql
SELECT last_name,salary,department_id
FROM employees
WHERE department_id = 50 XOR salary > 6000;
```

<font color='red'>#注意：AND的优先级高于OR</font>

## **4. 位运算符： & |  ^  ~  >>   <<**

```sql
SELECT 12 & 5, 12 | 5,12 ^ 5 
FROM DUAL;
 
SELECT 10 & ~1 FROM DUAL;

#在一定范围内满足：每向左移动1位，相当于乘以2；每向右移动一位，相当于除以2。
SELECT 4 << 1 , 8 >> 1
FROM DUAL;
```

# 4.排序与分页

## 1. 排序

<font color='purple'>如果没有使用排序操作，默认情况下查询返回的数据是按照添加数据的顺序显示的。</font>

`SELECT * FROM employees;`

<font color='green'>1.1 基本使用</font>

**使用 `ORDER BY `对查询到的数据进行排序操作**。

**升序**：ASC (ascend)

**降序**：DESC (descend)

<font color='green'>练习：按照salary从高到低的顺序显示员工信息</font>

```sql
SELECT employee_id,last_name,salary
FROM employees
ORDER BY salary DESC;
```

<font color='green'>练习：按照salary从低到高的顺序显示员工信息</font>

```sql
SELECT employee_id,last_name,salary
FROM employees
ORDER BY salary ASC;

SELECT employee_id,last_name,salary
FROM employees
ORDER BY salary; 
```

<font color='green'>如果在ORDER BY 后没有显式指名排序的方式的话，则默认按照升序排列。</font>

<font color='green'>#2. 我们可以使用列的别名，进行排序</font>

```sql
SELECT employee_id,salary,salary * 12 annual_sal
FROM employees
ORDER BY annual_sal;
```

<font color='green'>#列的别名只能在 ORDER BY 中使用，不能在WHERE中使用。
#如下操作报错！</font>

```sql
SELECT employee_id,salary,salary * 12 annual_sal
FROM employees
WHERE annual_sal > 81600;
```

<font color='green'>#3. 强调格式：WHERE 需要声明在FROM后，ORDER BY之前。</font>

```sql
SELECT employee_id,salary
FROM employees
WHERE department_id IN (50,60,70)
ORDER BY department_id DESC;
```

<font color='green'>#4. 二级排序</font>

<font color='blue'>#练习：显示员工信息，按照department_id的降序排列，salary的升序排列</font>

```sql
SELECT employee_id,salary,department_id
FROM employees
ORDER BY department_id DESC,salary ASC;
```

<font color='green'>#2. 分页</font>
<font color='green'>#2.1 mysql使用limit实现数据的分页显示</font>

```sql
#需求1：每页显示20条记录，此时显示第1页

SELECT employee_id,last_name
FROM employees
LIMIT 0,20;

#需求2：每页显示20条记录，此时显示第2页

SELECT employee_id,last_name
FROM employees
LIMIT 20,20;

#需求3：每页显示20条记录，此时显示第3页 

SELECT employee_id,last_name
FROM employees
LIMIT 40,20;
#需求：每页显示pageSize条记录，此时显示第pageNo页：
#公式：LIMIT (pageNo-1) * pageSize,pageSize;
```

<font color='blue'>#2.2 WHERE ... ORDER BY ...LIMIT 声明顺序如下：</font>

```sql
LIMIT的格式： 严格来说：LIMIT 位置偏移量,条目数

结构"LIMIT 0,条目数" 等价于 "LIMIT 条目数"

SELECT employee_id,last_name,salary
FROM employees
WHERE salary > 6000
ORDER BY salary DESC
#limit 0,10;
LIMIT 10;
```

<font color='blue'>#练习：表里有107条数据，我们只想要显示第 32、33 条数据怎么办呢？</font>

```sql
SELECT employee_id,last_name
FROM employees
LIMIT 31,2;
```

<font color='green'>#2.3 MySQL8.0新特性：LIMIT ... OFFSET ...</font>

<font color='blue'>#练习：表里有107条数据，我们只想要显示第 32、33 条数据怎么办呢？</font>

```sql
SELECT employee_id,last_name
FROM employees
LIMIT 2 OFFSET 31;
```

<font color='blue'>#练习：查询员工表中工资最高的员工信息</font>

```sql
SELECT employee_id,last_name,salary
FROM employees
ORDER BY salary DESC
#limit 0,1
LIMIT 1;
```

<font color='blue'>#2.4 LIMIT 可以使用在MySQL、PGSQL、MariaDB、SQLite 等数据库中使用，表示分页。</font>

不能使用在SQL Server、DB2、Oracle！
