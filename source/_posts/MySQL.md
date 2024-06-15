---
title: MySQL
date: 2024-05-15 15:48:58
tags: 数据库
categories: 工作/项目
---

# 数据库与数据库管理系统

## 数据库的相关概念

### 为什么要使用数据库

* <font color='red'>持久化(persistence)：</font>把数据保存到可掉电式存储设备中以供之后使用。大多数情况下，特别是企业级应用，数据持久化意味着将内存中的数据保存到硬盘上加以”固化”，而持久化的实现过程大多通过各种关系数据库来完成。
* 持久化的主要作用是将内存中的数据存储在关系型数据库中，当然也可以存储在磁盘文件、XML数据文件中。

### 数据库的相关概念



| <font color='red'>DB数据库（Database）</font>                |
| :----------------------------------------------------------- |
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

## 关系型数据库设计规则

* 关系型数据库的典型数据结构就是`数据表`，这些数据表的组成都是结构化的（Structured)。
* 将数据放到表中，表再放到库中。
* 一个数据库中可以有多个表，每个表都有一个名字，用来标识自己。表名具有唯一性。
* 表具有一些特性，这些特性定义了数据在表中如何存储，类似Java和Python中“类”的设计。

#### 记录、字段

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

#### 表的关联关系 

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

#  基本的SELECT语句

## SQL的分类

```sql
DDL:数据定义语言。CREATE \ ALTER \ DROP \ RENAME \ TRUNCATE
DML:数据操作语言。INSERT \ DELETE \ UPDATE \ SELECT （重中之重）
DCL:数据控制语言。COMMIT \ ROLLBACK \ SAVEPOINT \ GRANT \ REVOKE
```

## SQL语言的规则和规范

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

## MySQL的三种注释的方式

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

## 导入现有的数据表、表的数据。

方式1：source 文件的全路径名
`举例：source d:\atguigudb.sql;`

方式2：基于具体的图形化界面的工具可以导入数据
比如：`SQLyog中 选择 “工具” -- “执行sql脚本” -- 选中xxx.sql即可。`

#5. 最基本的SELECT语句： SELECT 字段1,字段2,... FROM 表名 
SELECT 1 + 1,3 * 2;

SELECT 1 + 1,3 * 2
FROM DUAL; #dual：伪表

## 表中的所有的字段（或列）
SELECT * FROM employees;

SELECT employee_id,last_name,salary
FROM employees;

## 列的别名

`as:全称：alias(别名),可以省略`

`列的别名可以使用一对""引起来，不要使用''。`

SELECT employee_id emp_id,last_name AS lname,department_id "部门id",salary * 12 AS "annual sal"
FROM employees;

## 去除重复行
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



## 空值参与运算

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



## 着重号 ``

```sql 
SELECT * FROM `order`;
```



## 查询常数

SELECT '尚硅谷',123,employee_id,last_name
FROM employees;

## 显示表结构

```sql
DESCRIBE employees; #显示了表中字段的详细信息

DESC employees;

DESC departments;
```



## 过滤数据

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

# 运算符
## 算术运算符： +  -  *  /  div  % mod

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



## 比较运算符

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

① **IS NULL \ IS NOT NULL \ ISNULL**
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

**② LEAST() \ GREATEST** 最小最大

```SQL
SELECT LEAST('g','b','t','m'),GREATEST('g','b','t','m')
FROM DUAL;

SELECT LEAST(first_name,last_name),LEAST(LENGTH(first_name),LENGTH(last_name))
FROM employees;
```

**③ BETWEEN （条件下界1） AND （条件上界2）  （查询条件1和条件2范围内的数据，包含边界）**
<font color='green'>查询工资在6000 到 8000的员工信息</font>

```SQL
SELECT employee_id,last_name,salary
FROM employees
#where salary between 6000 and 8000;
WHERE salary >= 6000 && salary <= 8000;
```

<font color='green'>交换6000 和 8000之后，查询不到数据</font>

```SQL
SELECT employee_id,last_name,salary
FROM employees
WHERE salary BETWEEN 8000 AND 6000;
```

<font color='green'>查询工资不在6000 到 8000的员工信息</font>

```SQL
SELECT employee_id,last_name,salary
FROM employees
WHERE salary NOT BETWEEN 6000 AND 8000;
#where salary < 6000 or salary > 8000;
```

**#④ in (set)\ not in (set)**离散查找

<font color='green'>练习：查询部门为10,20,30部门的员工信息</font>

```SQL
SELECT last_name,salary,department_id
FROM employees
#where department_id = 10 or department_id = 20 or department_id = 30;
WHERE department_id IN (10,20,30);
```

<font color='green'>练习：查询工资不是6000,7000,8000的员工信息</font>

```SQL
SELECT last_name,salary,department_id
FROM employees
WHERE salary NOT IN (6000,7000,8000);
```

**<font color='green'>⑤ LIKE :模糊查询</font>**

**<font color='green'>% : 代表不确定个数的字符 （0个，1个，或多个）</font>**

<font color='green'>练习：查询last_name中包含字符'a'的员工信息</font>

```SQL
SELECT last_name
FROM employees
WHERE last_name LIKE '%a%';
```

<font color='green'>练习：查询last_name中以字符'a'开头的员工信息</font>

```SQL
SELECT last_name
FROM employees
WHERE last_name LIKE 'a%';
```

<font color='green'>练习：查询last_name中包含字符'a'且包含字符'e'的员工信息</font>

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

**⑥ REGEXP \ RLIKE :正则表达式**

```sql
SELECT 'shkstart' REGEXP '^shk', 'shkstart' REGEXP 't$', 'shkstart' REGEXP 'hk'
FROM DUAL;

SELECT 'atguigu' REGEXP 'gu.gu','atguigu' REGEXP '[ab]'
FROM DUAL;
```

## 逻辑运算符： OR ||  AND && NOT ! XOR

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

<font color='red'>注意：AND的优先级高于OR</font>

## 位运算符： & |  ^  ~  >>   <<

```sql
SELECT 12 & 5, 12 | 5,12 ^ 5 
FROM DUAL;
 
SELECT 10 & ~1 FROM DUAL;

#在一定范围内满足：每向左移动1位，相当于乘以2；每向右移动一位，相当于除以2。
SELECT 4 << 1 , 8 >> 1
FROM DUAL;
```

# 排序与分页

## 排序

<font color='purple'>如果没有使用排序操作，默认情况下查询返回的数据是按照添加数据的顺序显示的。</font>

`SELECT * FROM employees;`

### <font color='green'>基本使用</font>

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

<font color='red'>如果在ORDER BY 后没有显式指名排序的方式的话，则**默认按照升序排列**。</font>

1.<font color='green'>我们可以使用列的别名，进行排序</font>

```sql
SELECT employee_id,salary,salary * 12 annual_sal
FROM employees
ORDER BY annual_sal;
```

2.<font color='green'><font color='red'>列的别名</font>只能在 ORDER BY 中使用，<font color='red'>不能在WHERE中使用</font>。
#如下操作报错！</font>

```sql
SELECT employee_id,salary,salary * 12 annual_sal
FROM employees
WHERE annual_sal > 81600;
```

<font color='green'> 3.强调格式：WHERE 需要声明在FROM后，ORDER BY之前。</font>

```sql
第三步
SELECT employee_id,salary
第一步
FROM employees
WHERE department_id IN (50,60,70)
第二步
ORDER BY department_id DESC;
```

### <font color='green'>二级排序</font>

<font color='blue'>练习：显示员工信息，按照department_id的降序排列，salary的升序排列</font>

💗可以使用不在SELECT

💗列表中的列排序。

💗在对多列进行排序的时候，首先排序的第一列必须有相同的列值，才会对第二列进行排序。如果第一列数据中所有值都是唯一的，将不再对第二列进行排序。

```sql
SELECT employee_id,salary,department_id
FROM employees
ORDER BY department_id DESC,salary ASC;
```

## <font color='green'>分页</font>

<font color='green'>2.1 mysql使用limit实现数据的分页显示</font>

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

```

**需求：每页显示pageSize条记录，此时显示第pageNo页：**
**<font color='blue'><font color='green'>公式：LIMIT (pageNo-1) * pageSize,pageSize;</font></font>**

<font color='blue'>2.2 WHERE ... ORDER BY ...LIMIT 声明顺序如下：</font>

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

<font color='blue'>练习：表里有107条数据，我们只想要显示第 32、33 条数据怎么办呢？</font>

```sql
SELECT employee_id,last_name
FROM employees
LIMIT 31,2;
```

**<font color='green'>2.3 MySQL8.0新特性：LIMIT ... OFFSET ...</font>**

<font color='blue'>练习：表里有107条数据，我们只想要显示第 32、33 条数据怎么办呢？</font>

```sql
SELECT employee_id,last_name
FROM employees
LIMIT 2 OFFSET 31;
```

<font color='blue'>练习：查询员工表中工资最高的员工信息</font>

```sql
SELECT employee_id,last_name,salary
FROM employees
ORDER BY salary DESC
#limit 0,1
LIMIT 1;
```

<font color='blue'>2.4 LIMIT 可以使用在MySQL、PGSQL、MariaDB、SQLite 等数据库中使用，表示分页。</font>

不能使用在SQL Server、DB2、Oracle！

# 多表查询

```sql
/*
SELECT ...,....,....
FROM ....
WHERE .... AND / OR / NOT....
ORDER BY .... (ASC/DESC),....,...
LIMIT ...,...

*/
```

🚩**熟悉常见的几个表**
```sql
DESC employees;

DESC departments;

DESC locations;
```

🚩**查询员工名为'Abel'的人在哪个城市工作？**
```sql
SELECT * 
FROM employees
WHERE last_name = 'Abel';

SELECT *
FROM departments
WHERE department_id = 80;


SELECT *
FROM locations 
WHERE location_id = 2500;
```

🚩**出现笛卡尔积的错误**

<font color='blue'>笛卡尔乘积是一个数学运算。假设我有两个集合 X 和 Y，那么 X 和 Y 的笛卡尔积就是 X 和 Y 的所有可能组合，也就是第一个对象来自于 X，第二个对象来自于 Y 的所有可能。组合的个数即为两个集合中元素个数的乘积数。  </font>

```sql
⛔错误的原因：缺少了多表的连接条件

错误的实现方式：每个员工都与每个部门匹配了一遍。
SELECT employee_id,department_name
FROM employees,departments;  #查询出2889条记录

#错误的方式
SELECT employee_id,department_name
FROM employees CROSS JOIN departments;#查询出2889条记录


SELECT *
FROM employees;  #107条记录

SELECT 2889 / 107
FROM DUAL;

SELECT *
FROM departments; # 27条记录
```

🚩多表查询的正确方式：需要有连接条件

```sql
SELECT employee_id,department_name
FROM employees,departments
#两个表的连接条件
WHERE employees.`department_id` = departments.department_id;
```

🚩如果查询语句中出现了多个表中都存在的字段，则**必须指明此字段所在的表。**
```sql
SELECT employees.employee_id,departments.department_name,employees.department_id
FROM employees,departments
WHERE employees.`department_id` = departments.department_id;
```

**#建议：从sql优化的角度，建议多表查询时，每个字段前都指明其所在的表。**

🚩可以给表起别名，在SELECT和WHERE中使用表的别名。
```sql
SELECT emp.employee_id,dept.department_name,emp.department_id
FROM employees emp,departments dept
WHERE emp.`department_id` = dept.department_id;

❗❗#如果给表起了别名，一旦在SELECT或WHERE中使用表名的话，则必须使用表的别名，而不能再使用表的原名。
#如下的操作是错误的：
SELECT emp.employee_id,departments.department_name,emp.department_id
FROM employees emp,departments dept
WHERE emp.`department_id` = departments.department_id;
```

🚩结论：如果有n个表实现多表的查询，则需要至少<font color='blue'><font color='blue'>n-1</font></font>个连接条件
```sql
#练习：查询员工的employee_id,last_name,department_name,city
SELECT e.employee_id,e.last_name,d.department_name,l.city,e.department_id,l.location_id
FROM employees e,departments d,locations l
WHERE e.`department_id` = d.`department_id`
AND d.`location_id` = l.`location_id`;

/*
演绎式：提出问题1 ---> 解决问题1 ----> 提出问题2 ---> 解决问题2 ....

归纳式：总--分
```


🚩 多表查询的分类
```sql
/*

角度1：等值连接  vs  非等值连接

角度2：自连接  vs  非自连接

角度3：内连接  vs  外连接

*/
```

## 等值连接  vs  非等值连接

🚩非等值连接的例子：
```sql
SELECT *
FROM job_grades;

SELECT e.last_name,e.salary,j.grade_level
FROM employees e,job_grades j
#where e.`salary` between j.`lowest_sal` and j.`highest_sal`;
WHERE e.`salary` >= j.`lowest_sal` AND e.`salary` <= j.`highest_sal`;
```

### 🚩 自连接  vs  非自连接

```sql
SELECT * FROM employees;

自连接的例子：
#练习：查询员工id,员工姓名及其管理者的id和姓名

SELECT emp.employee_id,emp.last_name,mgr.employee_id,mgr.last_name
FROM employees emp ,employees mgr
WHERE emp.`manager_id` = mgr.`employee_id`;
```

### 🚩 内连接  vs  外连接

**内连接：**<font color='blue'>合并具有同一列的两个以上的表的行, 结果集中不包含一个表与另一个表不匹配的行</font>

SELECT employee_id,department_name
FROM employees e,departments d
WHERE e.`department_id` = d.department_id;  # 只有106条记录

**外连接：**<font color='blue'>合并具有同一列的两个以上的表的行, 结果集中除了包含一个表与另一个表匹配的行之外，</font><font color='blue'>还查询到了左表 或 右表中不匹配的行。</font>

**外连接的分类**：<font color='blue'>左外连接、右外连接、满外连接</font>

**左外连接：**<font color='blue'>两个表在连接过程中除了返回满足连接条件的行以外还返回左表中不满足条件的行，这种连接称为左外连接。</font>

**右外连接：**<font color='blue'>两个表在连接过程中除了返回满足连接条件的行以外还返回右表中不满足条件的行，这种连接称为右外连接。</font>

```sql
#练习：查询所有的员工的last_name,department_name信息 

SELECT employee_id,department_name
FROM employees e,departments d
WHERE e.`department_id` = d.department_id;   # 需要使用左外连接
```

**SQL92语法实现内连接：见上，略**
**SQL92语法实现外连接：使用 +  ----------MySQL不支持SQL92语法中外连接的写法！**
**不支持：**

```sql
SELECT employee_id,department_name
FROM employees e,departments d
WHERE e.`department_id` = d.department_id(+);
```

**SQL99语法中使用 JOIN ...ON 的方式实现多表的查询。这种方式也能解决外连接的问题。MySQL是支持此种方式的。**

## **SQL99语法如何实现多表的查询。**

#### **SQL99语法实现<font color='red'>内连接</font>：**

```sql
SELECT last_name,department_name
FROM employees e INNER JOIN departments d
ON e.`department_id` = d.`department_id`;
多表连接
SELECT last_name,department_name,city
FROM employees e JOIN departments d
ON e.`department_id` = d.`department_id`
JOIN locations l
ON d.`location_id` = l.`location_id`;
```

#### **SQL99语法实现<font color='red'>外连接</font>：**

```sql
练习：查询所有的员工的last_name,department_name信息 

🚩左外连接：

SELECT last_name,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id` = d.`department_id`;

🚩右外连接：
SELECT last_name,department_name
FROM employees e RIGHT OUTER JOIN departments d
ON e.`department_id` = d.`department_id`;

🚩满外连接：mysql不支持FULL OUTER JOIN
SELECT last_name,department_name
FROM employees e FULL OUTER JOIN departments d
ON e.`department_id` = d.`department_id`;

```

## 🚩7种JOIN的实现：

![image-20240523102341139](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240523102341139.png)

#### UNION的使用

合并查询结果 利用UNION关键字，可以给出多条SELECT语句，并将它们的结果组合成单个结果集。合并时，两个表对应的列数和数据类型必须相同，并且相互对应。各个SELECT语句之间使用UNION或UNION ALL关键字分隔。

语法格式：

```sql
SELECT column,... FROM table1
UNION [ALL]
SELECT column,... FROM table2
```

**UNION操作符**  

`UNION `操作符返回两个查询的结果集的并集，去除重复记录。

**UNION ALL操作符**

`UNION ALL`操作符返回两个查询的结果集的并集。对于两个结果集的重复部分，不去重  

举例：查询部门编号>90或邮箱包含a的员工信息  

```sql
#方式1
SELECT * FROM employees WHERE email LIKE '%a%' OR department_id>90

#方式2
SELECT * FROM employees WHERE email LIKE '%a%'
UNION
SELECT * FROM employees WHERE department_id>90;

举例：查询中国用户中男性的信息以及美国用户中年男性的用户信息
SELECT id,cname FROM t_chinamale WHERE csex='男'
UNION ALL
SELECT id,tname FROM t_usmale WHERE tGender='male';
```

**🚩 UNION  和 UNION ALL的使用**

UNION：会执行去重操作

UNION ALL:不会执行去重操作

**结论：**如果明确知道合并数据后的结果数据不存在重复数据，或者不需要去除重复的数据，
则尽量使用UNION ALL语句，以提高数据查询的效率。

```sql
🥇中图：内连接

SELECT employee_id,department_name
FROM employees e JOIN departments d
ON e.`department_id` = d.`department_id`;

🥇左上图：左外连接

SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id` = d.`department_id`;

🥇右上图：右外连接

SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id` = d.`department_id`;

🥇左中图：

SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE d.`department_id` IS NULL;

🥇右中图：

SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE e.`department_id` IS NULL;

🥇左下图：满外连接

🥇方式1：左上图 UNION ALL 右中图

SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id` = d.`department_id`
UNION ALL
SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE e.`department_id` IS NULL;

🥇方式2：左中图 UNION ALL 右上图

SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE d.`department_id` IS NULL
UNION ALL
SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id` = d.`department_id`;

🥇右下图：左中图  UNION ALL 右中图

SELECT employee_id,department_name
FROM employees e LEFT JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE d.`department_id` IS NULL
UNION ALL
SELECT employee_id,department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE e.`department_id` IS NULL;
```



🚩 SQL99语法的新特性1:自然连接**NATURAL JOIN**

![image-20240523133008040](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240523133008040.png)

```sql
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
ON e.`department_id` = d.`department_id`
AND e.`manager_id` = d.`manager_id`;#连接条件有两个

NATURAL JOIN : 它会帮你自动查询两张连接表中`所有相同的字段`，然后进行`等值连接`。

SELECT employee_id,last_name,department_name
FROM employees e NATURAL JOIN departments d;
```

🚩SQL99语法的新特性2:**USING**
```sql
SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
ON e.department_id = d.department_id;

SELECT employee_id,last_name,department_name
FROM employees e JOIN departments d
USING (department_id);
```

🚩拓展：
```sql
SELECT last_name,job_title,department_name 
FROM employees INNER JOIN departments INNER JOIN jobs 
ON employees.department_id = departments.department_id 
AND employees.job_id = jobs.job_id;
```

## 小结

表连接的约束条件可以有三种方式：`WHERE, ON, USING`

* `WHERE`：适用于所有关联查询

* `ON` ：只能和JOIN一起使用，只能写关联条件。虽然关联条件可以并到`WHERE`中和其他条件一起写，但分开写可读性更好。

* `USING`：只能和JOIN一起使用，而且要求两个关联字段在关联表中名称一致，而且只能表示关联字段值相等

# 单行函数

## 基本函数

| 函数                |                                                              |
| ------------------- | ------------------------------------------------------------ |
| ABS(x)              | 返回x的绝对值                                                |
| SIGN(X)             | 返回X的符号。正数返回1，负数返回-1，0返回0                   |
| PI()                | 返回圆周率的值                                               |
| CEIL(x)，CEILING(x) | 返回大于或等于某个值的最小整数                               |
| FLOOR(x)            | 返回小于或等于某个值的最大整数                               |
| LEAST(e1,e2,e3…)    | 返回列表中的最小值                                           |
| GREATEST(e1,e2,e3…) | 返回列表中的最大值                                           |
| MOD(x,y)            | 返回X除以Y后的余数                                           |
| RAND()              | 返回0~1的随机值                                              |
| RAND(x)             | 返回0~1的随机值，其中x的值用作种子值，相同的X值会产生相同的随机数 |
| ROUND(x)            | 返回一个对x的值进行四舍五入后，最接近于X的整数               |
| ROUND(x,y)          | 返回一个对x的值进行四舍五入后最接近X的值，并保留到小数点后面Y位 |
| TRUNCATE(x,y)       | 返回数字x截断为y位小数的结果                                 |
| SQRT(x)             | 返回x的平方根。当X的值为负数时，返回NULL                     |

## 数值函数

### 基本的操作

```sql
SELECT ABS(-123),ABS(32),SIGN(-23),SIGN(43),PI(),CEIL(32.32),CEILING(-43.23),FLOOR(32.32),
FLOOR(-43.23),MOD(12,5),12 MOD 5,12 % 5
FROM DUAL;
```

#### 取随机数

```sql
SELECT RAND(),RAND(),RAND(10),RAND(10),RAND(-1),RAND(-1)
FROM DUAL;
```

#### 四舍五入，截断操作

```sql
SELECT ROUND(123.556),ROUND(123.456,0),ROUND(123.456,1),ROUND(123.456,2),
ROUND(123.456,-1),ROUND(153.456,-2)
FROM DUAL;

SELECT TRUNCATE(123.456,0),TRUNCATE(123.496,1),TRUNCATE(129.45,-1)
FROM DUAL;
```

#### 单行函数可以嵌套

```sql
SELECT TRUNCATE(ROUND(123.456,2),0)
FROM DUAL;
```

#### 角度与弧度的互换

| 函数       | 用法                                  |
| ---------- | ------------------------------------- |
| RADIANS(x) | 将角度转化为弧度，其中，参数x为角度值 |
| DEGREES(x) | 将弧度转化为角度，其中，参数x为弧度值 |

```sql
SELECT RADIANS(30),RADIANS(45),RADIANS(60),RADIANS(90),
DEGREES(2*PI()),DEGREES(RADIANS(60))
FROM DUAL;
```

#### 三角函数

| 函数       | 用法                                                         |
| ---------- | ------------------------------------------------------------ |
| SIN(x)     | 返回x的正弦值，其中，参数x为弧度值                           |
| ASIN(x)    | 返回x的反正弦值，即获取正弦为x的值。如果x的值不在-1到1之间，则返回NULL |
| COS(x)     | 返回x的余弦值，其中，参数x为弧度值                           |
| ACOS(x)    | 返回x的反余弦值，即获取余弦为x的值。如果x的值不在-1到1之间，则返回NULL |
| TAN(x)     | 返回x的正切值，其中，参数x为弧度值                           |
| ATAN(x)    | 返回x的反正切值，即返回正切值为x的值                         |
| ATAN2(m,n) | 返回两个参数的反正切值                                       |
| COT(x)     | 返回x的余切值，其中，X为弧度值                               |

```sql
SELECT SIN(RADIANS(30)),DEGREES(ASIN(1)),TAN(RADIANS(45)),DEGREES(ATAN(1))
FROM DUAL;
```

#### 指数和对数

| 函数                 | 用法                                                 |
| -------------------- | ---------------------------------------------------- |
| POW(x,y)，POWER(X,Y) | 返回x的y次方                                         |
| EXP(X)               | 返回e的X次方，其中e是一个常数，2.718281828459045     |
| LN(X)，LOG(X)        | 返回以e为底的X的对数，当X <= 0 时，返回的结果为NULL  |
| LOG10(X)             | 返回以10为底的X的对数，当X <= 0 时，返回的结果为NULL |
| LOG2(X)              | 返回以2为底的X的对数，当X <= 0 时，返回NULL          |

```sql
SELECT POW(2,5),POWER(2,4),EXP(2)
FROM DUAL;

SELECT LN(EXP(2)),LOG(EXP(2)),LOG10(10),LOG2(4)
FROM DUAL;
```

#### 进制间的转换

| 函数          | 用法                     |
| ------------- | ------------------------ |
| BIN(x)        | 返回x的二进制编码        |
| HEX(x)        | 返回x的十六进制编码      |
| OCT(x)        | 返回x的八进制编码        |
| CONV(x,f1,f2) | 返回f1进制数变成f2进制数 |

```sql
SELECT BIN(10),HEX(10),OCT(10),CONV(10,10,8)
FROM DUAL;
```

## 字符串函数

| 函数                             |                                                              |
| -------------------------------- | ------------------------------------------------------------ |
| ASCII(S)                         | 返回字符串S中的**第一个**字符的ASCII码值                     |
| CHAR_LENGTH(s)                   | 返回字符串s的**字符数**。作用与CHARACTER_LENGTH(s)相同       |
| LENGTH(s)                        | 返回字符串s的**字节数**，和字符集有关                        |
| CONCAT(s1,s2,......,sn)          | 连接s1,s2,......,sn为一个字符串                              |
| CONCAT_WS(x,s1,s2,......,sn)     | 同CONCAT(s1,s2,...)函数，但是每个字符串之间要加上x           |
| REPLACE(str, a, b)               | 用字符串b替换字符串str中所有出现的字符串a                    |
| INSERT(str, idx, len,replacestr) | 将字符串str从第idx位置开始，len个字符长的子串替换为字符串replacestr |
| UPPER(s) 或 UCASE(s)             | 将字符串s的所有字母转成大写字母                              |
| LOWER(s) 或LCASE(s)              | 将字符串s的所有字母转成小写字母                              |
| LEFT(str,n)                      | 返回字符串str最左边的n个字符                                 |
| RIGHT(str,n)                     | 返回字符串str最右边的n个字符                                 |
| LPAD(str, len, pad)              | 用字符串pad对str最左边进行填充，直到str的长度为len个字符     |
| RPAD(str ,len, pad)              | 用字符串pad对str最右边进行填充，直到str的长度为len个字符     |
| LTRIM(s)                         | 去掉字符串s左侧的空格                                        |
| RTRIM(s)                         | 去掉字符串s右侧的空格                                        |
| TRIM(s)                          | 去掉字符串s开始与结尾的空格                                  |
| TRIM(s1 FROM s)                  | 去掉字符串s开始与结尾的s1                                    |
| TRIM(LEADING s1FROM s)           | 去掉字符串s开始处的s1                                        |
| TRIM(TRAILING s1FROM s)          | 去掉字符串s结尾处的s1                                        |
| REPEAT(str, n)                   | 返回str重复n次的结果                                         |
| SPACE(n)                         | 返回n个空格                                                  |
| STRCMP(s1,s2)                    | 比较字符串s1,s2的ASCII码值的大小，>0表示s1大， <0 表示s2大   |
| SUBSTR(s,index,len)              | 返回从字符串s的index位置其len个字符，作用与SUBSTRING(s,n,len)、MID(s,n,len)相同 |
| LOCATE(substr,str)               | 返回字符串substr在字符串str中首次出现的位置，作用于POSITION(substrIN str)、INSTR(str,substr)相同。未找到，返回0 |
| ELT(m,s1,s2,…,sn)                | 返回指定位置的字符串，如果m=1，则返回s1，如果m=2，则返回s2，如果m=n，则返回sn |
| FIELD(s,s1,s2,…,sn)              | 返回字符串s在字符串列表中第一次出现的位置                    |
| FIND_IN_SET(s1,s2)               | 返回字符串s1在字符串s2中出现的位置。其中，字符串s2是一个以逗号分隔的字符串 |
| REVERSE(s)                       | 返回s反转后的字符串                                          |
| NULLIF(value1,value2)            | 比较两个字符串，**如果value1与value2相等，则返回NULL，否则返回value1** |

```sql
SELECT ASCII('Abcdfsf'),CHAR_LENGTH('hello'),CHAR_LENGTH('我们'),
LENGTH('hello'),LENGTH('我们')
FROM DUAL;

#xxx worked for yyy

SELECT CONCAT(emp.last_name,' worked for ',mgr.last_name) "details"
FROM employees emp JOIN employees mgr
WHERE emp.`manager_id` = mgr.employee_id;

SELECT CONCAT_WS('-','hello','world','hello','beijing')
FROM DUAL;
```

#### 字符串的索引是从1开始的！

```sql
SELECT INSERT('helloworld',2,3,'aaaaa'),REPLACE('hello','lol','mmm')
FROM DUAL;

SELECT UPPER('HelLo'),LOWER('HelLo')
FROM DUAL;

SELECT last_name,salary
FROM employees
WHERE LOWER(last_name) = 'King';

SELECT LEFT('hello',2),RIGHT('hello',3),RIGHT('hello',13)
FROM DUAL;
```

**LPAD:实现右对齐效果**

**RPAD:实现左对齐效果**

```sql
SELECT employee_id,last_name,LPAD(salary,10,' ')
FROM employees;

SELECT CONCAT('---',LTRIM('    h  el  lo   '),'***'),
TRIM('oo' FROM 'ooheollo')
FROM DUAL;

SELECT REPEAT('hello',4),LENGTH(SPACE(5)),STRCMP('abc','abe')
FROM DUAL;


SELECT SUBSTR('hello',2,2),LOCATE('lll','hello')
FROM DUAL;

SELECT ELT(2,'a','b','c','d'),FIELD('mm','gg','jj','mm','dd','mm'),
FIND_IN_SET('mm','gg,mm,jj,dd,mm,gg')
FROM DUAL;

SELECT employee_id,NULLIF(LENGTH(first_name),LENGTH(last_name)) "compare"
FROM employees;
```

## 日期和时间函数

#### 获取日期、时间

| 函数                                                         | 用法                           |
| ------------------------------------------------------------ | ------------------------------ |
| CURDATE() ，CURRENT_DATE()                                   | 返回当前日期，只包含年、月、日 |
| CURTIME() ， CURRENT_TIME()                                  | 返回当前时间，只包含时、分、秒 |
| NOW() / SYSDATE() / CURRENT_TIMESTAMP() / LOCALTIME() /LOCALTIMESTAMP() | 返回当前系统日期和时间         |
| UTC_DATE()                                                   | 返回UTC（世界标准时间）日期    |
| UTC_TIME()                                                   | 返回UTC（世界标准时间）时间    |

```sql
SELECT CURDATE(),CURRENT_DATE(),CURTIME(),NOW(),SYSDATE(),
UTC_DATE(),UTC_TIME()
FROM DUAL;

SELECT CURDATE(),CURDATE() + 0,CURTIME() + 0,NOW() + 0
FROM DUAL;
```

#### 💖日期与时间戳的转换(把指定的日期转换成毫秒数)

| 函数                     | 用法                                                         |
| ------------------------ | ------------------------------------------------------------ |
| UNIX_TIMESTAMP()         | 以UNIX时间戳的形式返回当前时间。SELECT UNIX_TIMESTAMP() ->1634348884 |
| UNIX_TIMESTAMP(date)     | 将指定时间date以UNIX时间戳的形式返回。                       |
| FROM_UNIXTIME(timestamp) | 将UNIX时间戳的时间转换为普通格式的时间                       |

```sql
SELECT UNIX_TIMESTAMP(),UNIX_TIMESTAMP('2021-10-01 12:12:32'),
FROM_UNIXTIME(1635173853),FROM_UNIXTIME(1633061552)
FROM DUAL;
```

#### 获取月份、星期、星期数、天数等函数

| 函数                                    | 用法                                            |
| --------------------------------------- | ----------------------------------------------- |
| YEAR(date) / MONTH(date) / DAY(date)    | 返回具体的日期值                                |
| HOUR(time) / MINUTE(time) /SECOND(time) | 返回具体的时间值                                |
| MONTHNAME(date)                         | 返回月份：January，...                          |
| DAYNAME(date)                           | 返回星期几：MONDAY，TUESDAY.....SUNDAY          |
| WEEKDAY(date)                           | 返回周几，注意，周1是0，周2是1，。。。周日是6   |
| QUARTER(date)                           | 返回日期对应的季度，范围为1～4                  |
| WEEK(date) ， WEEKOFYEAR(date)          | 返回一年中的第几周                              |
| DAYOFYEAR(date)                         | 返回日期是一年中的第几天                        |
| DAYOFMONTH(date)                        | 返回日期位于所在月份的第几天                    |
| DAYOFWEEK(date)                         | 返回周几，注意：周日是1，周一是2，。。。周六是7 |

```sql
SELECT YEAR(CURDATE()),MONTH(CURDATE()),DAY(CURDATE()),
HOUR(CURTIME()),MINUTE(NOW()),SECOND(SYSDATE())
FROM DUAL;


SELECT MONTHNAME('2021-10-26'),DAYNAME('2021-10-26'),WEEKDAY('2021-10-26'),
QUARTER(CURDATE()),WEEK(CURDATE()),DAYOFYEAR(NOW()),
DAYOFMONTH(NOW()),DAYOFWEEK(NOW())
FROM DUAL;
```

#### 日期的操作函数

| 函数                    | 用法                                       |
| ----------------------- | ------------------------------------------ |
| EXTRACT(type FROM date) | 返回指定日期中特定的部分，type指定返回的值 |

![image-20240525153810170](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240525153810170.png)

```sql
SELECT EXTRACT(SECOND FROM NOW()),EXTRACT(DAY FROM NOW()),
EXTRACT(HOUR_MINUTE FROM NOW()),EXTRACT(QUARTER FROM '2021-05-12')
FROM DUAL;
```

#### 时间和秒钟转换的函数

| 函数                 | 用法                                                         |
| -------------------- | ------------------------------------------------------------ |
| TIME_TO_SEC(time)    | 将 time 转化为秒并返回结果值。转化的公式为： 小时*3600+分钟*60+秒 |
| SEC_TO_TIME(seconds) | 将 seconds 描述转化为包含小时、分钟和秒的时间                |

```sql
SELECT TIME_TO_SEC(CURTIME()),
SEC_TO_TIME(83355)
FROM DUAL;
```

#### 💖计算日期和时间的函数

| 函数                                                         | 用法                                           |
| ------------------------------------------------------------ | ---------------------------------------------- |
| DATE_ADD(datetime, INTERVAL expr type)，ADDDATE(date,INTERVAL expr type) | 返回与给定日期时间相差INTERVAL时间段的日期时间 |
| DATE_SUB(date,INTERVAL expr type)，SUBDATE(date,INTERVAL expr type) | 返回与date相差INTERVAL时间间隔的日期           |

| 函数                         | 用法                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| ADDTIME(time1,time2)         | 返回time1加上time2的时间。当time2为一个数字时，代表的是秒 ，可以为负数 |
| SUBTIME(time1,time2)         | 返回time1减去time2后的时间。当time2为一个数字时，代表的是 秒 ，可以为负数 |
| DATEDIFF(date1,date2)        | 返回date1 - date2的日期间隔天数                              |
| TIMEDIFF(time1, time2)       | 返回time1 - time2的时间间隔                                  |
| FROM_DAYS(N)                 | 返回从0000年1月1日起，N天以后的日期                          |
| TO_DAYS(date)                | 返回日期date距离0000年1月1日的天数                           |
| LAST_DAY(date)               | 返回date所在月份的最后一天的日期                             |
| MAKEDATE(year,n)             | 针对给定年份与所在年份中的天数返回一个日期                   |
| MAKETIME(hour,minute,second) | 将给定的小时、分钟和秒组合成时间并返回                       |
| PERIOD_ADD(time,n)           | 返回time加上n后的时间                                        |

```sql
SELECT NOW(),DATE_ADD(NOW(),INTERVAL 1 YEAR),
DATE_ADD(NOW(),INTERVAL -1 YEAR),
DATE_SUB(NOW(),INTERVAL 1 YEAR)
FROM DUAL;


SELECT DATE_ADD(NOW(), INTERVAL 1 DAY) AS col1,DATE_ADD('2021-10-21 23:32:12',INTERVAL 1 SECOND) AS col2,
ADDDATE('2021-10-21 23:32:12',INTERVAL 1 SECOND) AS col3,
DATE_ADD('2021-10-21 23:32:12',INTERVAL '1_1' MINUTE_SECOND) AS col4,
DATE_ADD(NOW(), INTERVAL -1 YEAR) AS col5, #可以是负数
DATE_ADD(NOW(), INTERVAL '1_1' YEAR_MONTH) AS col6 #需要单引号
FROM DUAL;


SELECT ADDTIME(NOW(),20),SUBTIME(NOW(),30),SUBTIME(NOW(),'1:1:3'),DATEDIFF(NOW(),'2021-10-01'),
TIMEDIFF(NOW(),'2021-10-25 22:10:10'),FROM_DAYS(366),TO_DAYS('0000-12-25'),
LAST_DAY(NOW()),MAKEDATE(YEAR(NOW()),32),MAKETIME(10,21,23),PERIOD_ADD(20200101010101,10)
FROM DUAL;
```

#### 💖日期的格式化与解析

| 函数                              | 用法                                       |
| --------------------------------- | ------------------------------------------ |
| DATE_FORMAT(date,fmt)             | 按照字符串fmt格式化日期date值为字符串      |
| TIME_FORMAT(time,fmt)             | 按照字符串fmt格式化时间time值              |
| GET_FORMAT(date_type,format_type) | 返回日期字符串的显示格式                   |
| STR_TO_DATE(str, fmt)             | 按照字符串fmt对str进行解析，解析为一个日期 |

上述 非GET_FORMAT 函数中fmt参数常用的格式符：  

| 符   | 说明                                                         | 格式符 | 说明                                                         |
| ---- | ------------------------------------------------------------ | ------ | ------------------------------------------------------------ |
| %Y   | 4位数字表示年份                                              | %y     | 表示两位数字表示年份                                         |
| %M   | 月名表示月份（January,....）                                 | %m     | 两位数字表示月份（01,02,03。。。）                           |
| %b   | 缩写的月名（Jan.，Feb.，....）                               | %c     | 数字表示月份（1,2,3,...）                                    |
| %D   | 英文后缀表示月中的天数（1st,2nd,3rd,...）                    | %d     | 两位数字表示月中的天数(01,02...)                             |
| %e   | 数字形式表示月中的天数（1,2,3,4,5.....）                     |        |                                                              |
| %H   | 两位数字表示小数，24小时制（01,02..）                        | %h和%I | 两位数字表示小时，12小时制（01,02..）                        |
| %k   | 数字形式的小时，24小时制(1,2,3)                              | %l     | 数字形式表示小时，12小时制（1,2,3,4....）                    |
| %i   | 两位数字表示分钟（00,01,02）                                 | %S和%s | 两位数字表示秒(00,01,02...)                                  |
| %W   | 一周中的星期名称（Sunday...）                                | %a     | 一周中的星期缩写（Sun.， Mon.,Tues.，..）                    |
| %w   | 以数字表示周中的天数(0=Sunday,1=Monday....)                  |        |                                                              |
| %j   | 以3位数字表示年中的天数(001,002...)                          | %U     | 以数字表示年中的第几周， （1,2,3。。）其中Sunday为周中第一天 |
| %u   | 以数字表示年中的第几周， （1,2,3。。）其中Monday为周中第一天 |        |                                                              |
| %T   | 24小时制                                                     | %r     | 12小时制                                                     |
| %p   | AM或PM                                                       | %%     | 表示%                                                        |

```sql
格式化：日期 ---> 字符串

解析：  字符串 ----> 日期

#此时我们谈的是日期的显式格式化和解析

#之前，我们接触过隐式的格式化或解析
SELECT *
FROM employees
WHERE hire_date = '1993-01-13';

#格式化：

SELECT DATE_FORMAT(CURDATE(),'%Y-%M-%D'),
DATE_FORMAT(NOW(),'%Y-%m-%d'),TIME_FORMAT(CURTIME(),'%h:%i:%S'),
DATE_FORMAT(NOW(),'%Y-%M-%D %h:%i:%S %W %w %T %r')
FROM DUAL;

#解析：格式化的逆过程
SELECT STR_TO_DATE('2021-October-25th 11:37:30 Monday 1','%Y-%M-%D %h:%i:%S %W %w')
FROM DUAL;

SELECT GET_FORMAT(DATE,'USA')
FROM DUAL;

SELECT DATE_FORMAT(CURDATE(),GET_FORMAT(DATE,'USA'))
FROM DUAL;
```

## 流程控制函数

| 函数                                                         | 用法                                               |
| ------------------------------------------------------------ | -------------------------------------------------- |
| IF(value,value1,value2)                                      | 如果value的值为TRUE，返回value1，否则返回value2    |
| IFNULL(value1, value2)                                       | 如果value1不为NULL，返回value1，否则返回value2     |
| CASE WHEN 条件1 THEN 结果1 WHEN 条件2 THEN 结果2.... [ELSE resultn] END | 相当于Java的if...else if...else...                 |
| CASE expr WHEN 常量值1 THEN 值1 WHEN 常量值1 THEN值1 .... [ELSE 值n] END | 相当于Java的switch...case...，expr是需要判断的字段 |

#### IF(VALUE,VALUE1,VALUE2)

```sql
SELECT last_name,salary,IF(salary >= 6000,'高工资','低工资') "details"
FROM employees;

SELECT last_name,commission_pct,IF(commission_pct IS NOT NULL,commission_pct,0) "details",
salary * 12 * (1 + IF(commission_pct IS NOT NULL,commission_pct,0)) "annual_sal"
FROM employees;

```

#### IFNULL(VALUE1,VALUE2):看做是IF(VALUE,VALUE1,VALUE2)的特殊情况

```sql
SELECT last_name,commission_pct,IFNULL(commission_pct,0) "details"
FROM employees;
```

#### CASE WHEN ... THEN ...WHEN ... THEN ... ELSE ... END

```sql
类似于java的if ... else if ... else if ... else

SELECT last_name,salary,CASE WHEN salary >= 15000 THEN '白骨精' 
			     WHEN salary >= 10000 THEN '潜力股'
			     WHEN salary >= 8000 THEN '小屌丝'
			     ELSE '草根' END "details",department_id
FROM employees;

SELECT last_name,salary,CASE WHEN salary >= 15000 THEN '白骨精' 
			     WHEN salary >= 10000 THEN '潜力股'
			     WHEN salary >= 8000 THEN '小屌丝'
			     END "details"
FROM employees;
```



#### CASE ... WHEN ... THEN ... WHEN ... THEN ... ELSE ... END

```sql
类似于java的swich ... case...

/*

练习1
查询部门号为 10,20, 30 的员工信息, 
若部门号为 10, 则打印其工资的 1.1 倍, 
20 号部门, 则打印其工资的 1.2 倍, 
30 号部门,打印其工资的 1.3 倍数,
其他部门,打印其工资的 1.4 倍数

*/
SELECT employee_id,last_name,department_id,salary,CASE department_id WHEN 10 THEN salary * 1.1
								     WHEN 20 THEN salary * 1.2
								     WHEN 30 THEN salary * 1.3
								     ELSE salary * 1.4 END "details"
FROM employees;

/*

练习2
查询部门号为 10,20, 30 的员工信息, 
若部门号为 10, 则打印其工资的 1.1 倍, 
20 号部门, 则打印其工资的 1.2 倍, 
30 号部门打印其工资的 1.3 倍数

*/
select 自带循环
SELECT employee_id,last_name,department_id,salary,CASE department_id WHEN 10 THEN salary * 1.1
								     WHEN 20 THEN salary * 1.2
								     WHEN 30 THEN salary * 1.3
								     END "details"
FROM employees
WHERE department_id IN (10,20,30);
```



## 加密与解密的函数

加密与解密函数主要用于对数据库中的数据进行加密和解密处理，以防止数据被他人窃取。这些函数在保证数据库安全时非常有用。

| PASSWORD(str)               | 返回字符串str的加密版本，41位长的字符串。加密结果 不可逆 ，常用于用户的密码加密 |
| --------------------------- | ------------------------------------------------------------ |
| MD5(str)                    | 返回字符串str的md5加密后的值，也是一种加密方式。若参数为NULL，则会返回NULL |
| SHA(str)                    | 从原明文密码str计算并返回加密后的密码字符串，当参数为NULL时，返回NULL。 SHA加密算法比MD5更加安全 。 |
| ENCODE(value,password_seed) | 返回使用password_seed作为加密密码加密value                   |
| DECODE(value,password_seed) | 返回使用password_seed作为加密密码解密value                   |

```sql
PASSWORD()在mysql8.0中弃用。

SELECT MD5('mysql'),SHA('mysql'),MD5(MD5('mysql'))
FROM DUAL;

#ENCODE()\DECODE() 在mysql8.0中弃用。
/*
SELECT ENCODE('atguigu','mysql'),DECODE(ENCODE('atguigu','mysql'),'mysql')
FROM DUAL;
*/
```



## MySQL信息函数

| VERSION()                                             | 返回当前MySQL的版本号                                    |
| ----------------------------------------------------- | -------------------------------------------------------- |
| CONNECTION_ID()                                       | 返回当前MySQL服务器的连接数                              |
| DATABASE()，SCHEMA()                                  | 返回MySQL命令行当前所在的数据库                          |
| USER()，CURRENT_USER()、SYSTEM_USER()，SESSION_USER() | 返回当前连接MySQL的用户名，返回结果格式为“主机名@用户名” |
| CHARSET(value)                                        | 返回字符串value自变量的字符集                            |
| COLLATION(value)                                      | 返回字符串value的比较规则                                |

```sql
SELECT VERSION(),CONNECTION_ID(),DATABASE(),SCHEMA(),
USER(),CURRENT_USER(),CHARSET('尚硅谷'),COLLATION('尚硅谷')
FROM DUAL;
```



## 其他函数

| 函数                          | 用法                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| FORMAT(value,n)               | 返回对数字value进行格式化后的结果数据。n表示 四舍五入 后保留到小数点后n位 |
| CONV(value,from,to)           | 将value的值进行不同进制之间的转换                            |
| INET_ATON(ipvalue)            | 将以点分隔的IP地址转化为一个数字                             |
| INET_NTOA(value)              | 将数字形式的IP地址转化为以点分隔的IP地址                     |
| BENCHMARK(n,expr)             | 将表达式expr重复执行n次。用于测试MySQL处理expr表达式所耗费的时间 |
| CONVERT(value USINGchar_code) | 将value所使用的字符编码修改为char_code                       |

```sql
#如果n的值小于或者等于0，则只保留整数部分
SELECT FORMAT(123.125,2),FORMAT(123.125,0),FORMAT(123.125,-2)
FROM DUAL;

SELECT CONV(16, 10, 2), CONV(8888,10,16), CONV(NULL, 10, 2)
FROM DUAL;
#以“192.168.1.100”为例，计算方式为192乘以256的3次方，加上168乘以256的2次方，加上1乘以256，再加上100。
SELECT INET_ATON('192.168.1.100'),INET_NTOA(3232235876)
FROM DUAL;

#BENCHMARK()用于测试表达式的执行效率
SELECT BENCHMARK(100000,MD5('mysql'))
FROM DUAL;

CONVERT():可以实现字符集的转换

SELECT CHARSET('atguigu'),CHARSET(CONVERT('atguigu' USING 'gbk'))
FROM DUAL;


```

# 聚合函数

## 常见的几个聚合函数

<font color='blue'>1.1 AVG / SUM ：只适用于<font color='red'>数值类型</font>的字段（或变量）</font>

```sql
SELECT AVG(salary),SUM(salary),AVG(salary) * 107
FROM employees;
#如下的操作没有意义
SELECT SUM(last_name),AVG(last_name),SUM(hire_date)
FROM employees;
```

<font color='blue'>1.2 MAX / MIN :适用于数值类型、字符串类型、日期时间类型的字段（或变量）</font>

```sql
SELECT MAX(salary),MIN(salary)
FROM employees;

SELECT MAX(last_name),MIN(last_name),MAX(hire_date),MIN(hire_date)
FROM employees;
```

<font color='blue'>1.3 COUNT：</font>

<font color='green'>① 作用：计算指定字段在查询结构中出现的个数（不包含NULL值的）</font>

```sql
SELECT COUNT(employee_id),COUNT(salary),COUNT(2 * salary),COUNT(1),COUNT(2),COUNT(*)
FROM employees ;

SELECT *
FROM employees;
```

<font color='orange'>#如果计算表中有多少条记录，如何实现？
</font>

<font color='orange'>#方式1：COUNT(*)</font>

<font color='orange'>#方式2：COUNT(1)
</font>

<font color='orange'>#方式3：COUNT(具体字段) : 不一定对！</font>

<font color='green'>② 注意：计算指定字段出现的个数时，是不计算NULL值的。</font>

```sql
SELECT COUNT(commission_pct)
FROM employees;

SELECT commission_pct
FROM employees
WHERE commission_pct IS NOT NULL;
```

<font color='green'>③ 公式：AVG = SUM / COUNT</font>

```sql
SELECT AVG(salary),SUM(salary)/COUNT(salary),
AVG(commission_pct),SUM(commission_pct)/COUNT(commission_pct),
SUM(commission_pct) / 107
FROM employees;

#需求：查询公司中平均奖金率
#错误的！
SELECT AVG(commission_pct)
FROM employees;

#正确的：
SELECT SUM(commission_pct) / COUNT(IFNULL(commission_pct,0)),
AVG(IFNULL(commission_pct,0))
FROM employees;

如何需要统计表中的记录数，使用COUNT(*)、COUNT(1)、COUNT(具体字段) 哪个效率更高呢？

如果使用的是MyISAM 存储引擎，则三者效率相同，都是O(1)

如果使用的是InnoDB 存储引擎，则三者效率：COUNT(*) = COUNT(1)> COUNT(字段)


#其他：方差、标准差、中位数 
```

## GROUP BY 的使用

```sql
#需求：查询各个部门的平均工资，最高工资
SELECT department_id,AVG(salary),SUM(salary)
FROM employees
GROUP BY department_id

#需求：查询各个job_id的平均工资
SELECT job_id,AVG(salary)
FROM employees
GROUP BY job_id;

#需求：查询各个department_id,job_id的平均工资
#方式1：
SELECT department_id,job_id,AVG(salary)
FROM employees
GROUP BY  department_id,job_id;
#方式2：
SELECT job_id,department_id,AVG(salary)
FROM employees
GROUP BY job_id,department_id;


#错误的！
SELECT department_id,job_id,AVG(salary)
FROM employees
GROUP BY department_id;

#结论1：SELECT中出现的非组函数的字段必须声明在GROUP BY 中。

反之，GROUP BY中声明的字段可以不出现在SELECT中。

#结论2：GROUP BY 声明在FROM后面、WHERE后面，ORDER BY 前面、LIMIT前面

#结论3：MySQL中GROUP BY中使用WITH ROLLUP可以得到总平均，见下图


SELECT department_id,AVG(salary)
FROM employees
GROUP BY department_id WITH ROLLUP;

#需求：查询各个部门的平均工资，按照平均工资升序排列
SELECT department_id,AVG(salary) avg_sal
FROM employees
GROUP BY department_id
ORDER BY avg_sal ASC;

#说明：当使用ROLLUP时，不能同时使用ORDER BY子句进行结果排序，即ROLLUP和ORDER BY是互相排斥的。
#错误的：
SELECT department_id,AVG(salary) avg_sal
FROM employees
GROUP BY department_id WITH ROLLUP
ORDER BY avg_sal ASC;
```

![image-20240602191358392](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240602191358392.png)

## HAVING的使用 (作用：用来过滤数据的)

```sql
#练习：查询各个部门中最高工资比10000高的部门信息
#错误的写法：
SELECT department_id,MAX(salary)
FROM employees
WHERE MAX(salary) > 10000
GROUP BY department_id;


#要求1：如果过滤条件中使用了聚合函数，则必须使用HAVING来替换WHERE。否则，报错。
#要求2：HAVING 必须声明在 GROUP BY 的后面。

#正确的写法：
SELECT department_id,MAX(salary)
FROM employees
GROUP BY department_id
HAVING MAX(salary) > 10000;

#要求3：开发中，我们使用HAVING的前提是SQL中使用了GROUP BY。


#练习：查询部门id为10,20,30,40这4个部门中最高工资比10000高的部门信息
#方式1：推荐，执行效率高于方式2.
SELECT department_id,MAX(salary)
FROM employees
WHERE department_id IN (10,20,30,40)
GROUP BY department_id
HAVING MAX(salary) > 10000;

#方式2：
SELECT department_id,MAX(salary)
FROM employees
GROUP BY department_id
HAVING MAX(salary) > 10000 AND department_id IN (10,20,30,40);

#结论：当过滤条件中有聚合函数时，则此过滤条件必须声明在HAVING中。

当过滤条件中没有聚合函数时，则此过滤条件声明在WHERE中或HAVING中都可以。但是，建议大家声明在WHERE中。

/*
  WHERE 与 HAVING 的对比

1. 从适用范围上来讲，HAVING的适用范围更广。 
2. 如果过滤条件中没有聚合函数：这种情况下，WHERE的执行效率要高于HAVING
   */
```



## SQL底层执行原理

```sql
#4.1 SELECT 语句的完整结构
/*

#sql92语法：
SELECT ....,....,....(存在聚合函数)
FROM ...,....,....
WHERE 多表的连接条件 AND 不包含聚合函数的过滤条件
GROUP BY ...,....
HAVING 包含聚合函数的过滤条件
ORDER BY ....,...(ASC / DESC )
LIMIT ...,....


#sql99语法：
SELECT ....,....,....(存在聚合函数)
FROM ... (LEFT / RIGHT)JOIN ....ON 多表的连接条件 
(LEFT / RIGHT)JOIN ... ON ....
WHERE 不包含聚合函数的过滤条件
GROUP BY ...,....
HAVING 包含聚合函数的过滤条件
ORDER BY ....,...(ASC / DESC )
LIMIT ...,....


*/

```

### SQL语句的执行过程：

**#FROM ...,...-> ON -> (LEFT/RIGNT  JOIN) -> WHERE -> GROUP BY -> HAVING -> SELECT -> DISTINCT ->** **ORDER BY -> LIMIT**

# 子查询

## **由一个具体的需求，引入子查询**

```sql
#需求：谁的工资比Abel的高？
#方式1：
SELECT salary
FROM employees
WHERE last_name = 'Abel';

SELECT last_name,salary
FROM employees
WHERE salary > 11000;

#方式2：自连接
SELECT e2.last_name,e2.salary
FROM employees e1,employees e2
WHERE e2.`salary` > e1.`salary` #多表的连接条件
AND e1.last_name = 'Abel';

#方式3：子查询
SELECT last_name,salary
FROM employees
WHERE salary > (
		SELECT salary
		FROM employees
		WHERE last_name = 'Abel'
		);
```

## 称谓的规范：外查询（或主查询）、内查询（或子查询）

```sql
/*

- 子查询（内查询）在主查询之前一次执行完成。
- 子查询的结果被主查询（外查询）使用 。
- 注意事项
  - 子查询要包含在括号内
  - 将子查询放在比较条件的右侧
  - 单行操作符对应单行子查询，多行操作符对应多行子查询

*/

#不推荐：
SELECT last_name,salary
FROM employees
WHERE  (
	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'
		) < salary;
		
/*
```

## 子查询的分类

```sql
角度1：从内查询返回的结果的条目数
单行子查询  vs  多行子查询

角度2：内查询是否被执行多次
	相关子查询  vs  不相关子查询
	
 比如：相关子查询的需求：查询工资大于本部门平均工资的员工信息。
       不相关子查询的需求：查询工资大于本公司平均工资的员工信息。

*/

#子查询的编写技巧（或步骤）：① 从里往外写  ② 从外往里写
```

## **单行子查询**

### 单行操作符： =  !=  >   >=  <  <= 

```sql
#题目：查询工资大于149号员工工资的员工的信息

SELECT employee_id,last_name,salary
FROM employees
WHERE salary > (
		SELECT salary
		FROM employees
		WHERE employee_id = 149
		);

#题目：返回job_id与141号员工相同，salary比143号员工多的员工姓名，job_id和工资

SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
		SELECT job_id
		FROM employees
		WHERE employee_id = 141
		)
AND salary > (
		SELECT salary
		FROM employees
		WHERE employee_id = 143
		);


#题目：返回公司工资最少的员工的last_name,job_id和salary

SELECT last_name,job_id,salary
FROM employees
WHERE salary = (
		SELECT MIN(salary)
		FROM employees
		);

#题目：查询与141号员工的manager_id和department_id相同的其他员工
#的employee_id，manager_id，department_id。
#方式1：
SELECT employee_id,manager_id,department_id
FROM employees
WHERE manager_id = (
		    SELECT manager_id
		    FROM employees
		    WHERE employee_id = 141
		   )
AND department_id = (
		    SELECT department_id
		    FROM employees
		    WHERE employee_id = 141
		   )
AND employee_id <> 141;//员工id不等于141

#方式2：了解
SELECT employee_id,manager_id,department_id
FROM employees
WHERE (manager_id,department_id) = (
				    SELECT manager_id,department_id
			            FROM employees
				    WHERE employee_id = 141
				   )
AND employee_id <> 141;

#题目：查询最低工资大于110号部门最低工资的部门id和其最低工资

SELECT department_id,MIN(salary)
FROM employees
WHERE department_id IS NOT NULL
GROUP BY department_id
HAVING MIN(salary) > (
			SELECT MIN(salary)
			FROM employees
			WHERE department_id = 110
		     );

#题目：显式员工的employee_id,last_name和location。
#其中，若员工department_id与location_id为1800的department_id相同，
#则location为’Canada’，其余则为’USA’。

SELECT employee_id,last_name,CASE department_id WHEN (SELECT department_id FROM departments WHERE location_id = 1800) THEN 'Canada'
						ELSE 'USA' END "location"
FROM employees;
```

### 子查询中的空值问题

```sql
SELECT last_name, job_id
FROM   employees
WHERE  job_id =
                (SELECT job_id
                 FROM   employees
                 WHERE  last_name = 'Haas');                 
```

### 非法使用子查询

```sql     
#错误：Subquery returns more than 1 row
SELECT employee_id, last_name
FROM   employees
WHERE  salary =
                (SELECT   MIN(salary)
                 FROM     employees
                 GROUP BY department_id);    
```

## 多行子查询

### 多行子查询的操作符： IN  ANY ALL SOME(同ANY)

| 操作符 | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| IN     | 等于列表中的任意一个                                         |
| ANY    | 需要和单行比较操作符一起使用，和子查询返回的某一/任一个值比较 |
| ALL    | 需要和单行比较操作符一起使用，和子查询返回的所有值比较       |
| SOME   | 实际上是ANY的别名，作用相同，一般常使用ANY                   |

### 举例：

#### IN:

```sql
SELECT employee_id, last_name
FROM   employees
WHERE  salary IN
                (SELECT   MIN(salary)
                 FROM     employees
                 GROUP BY department_id);                  
```

#### ANY / ALL:

```sql
#题目：返回其它job_id中比job_id为‘IT_PROG’部门任一工资低的员工的员工号、
#姓名、job_id 以及salary

SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE job_id <> 'IT_PROG'
AND salary < ANY (
		SELECT salary
		FROM employees
		WHERE job_id = 'IT_PROG'
		);

#题目：返回其它job_id中比job_id为‘IT_PROG’部门所有工资低的员工的员工号、
#姓名、job_id 以及salary
SELECT employee_id,last_name,job_id,salary
FROM employees
WHERE job_id <> 'IT_PROG'
AND salary < ALL (
		SELECT salary
		FROM employees
		WHERE job_id = 'IT_PROG'
		);
		
#题目：查询平均工资最低的部门id
#MySQL中聚合函数是不能嵌套使用的。
#方式1：
SELECT department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary) = (
			SELECT MIN(avg_sal)
			FROM(
				SELECT AVG(salary) avg_sal
				FROM employees
				GROUP BY department_id
				) t_dept_avg_sal
			);

#方式2：
SELECT department_id
FROM employees
GROUP BY department_id
HAVING AVG(salary) <= ALL(	
			SELECT AVG(salary) avg_sal
			FROM employees
			GROUP BY department_id
			) 
```

#### 5.3 空值问题

```sql
SELECT last_name
FROM employees
WHERE employee_id NOT IN (
			SELECT manager_id
			FROM employees
			);			
```

## 相关子查询

#回顾：查询员工中工资大于公司平均工资的员工的last_name,salary和其department_id

### 6.1 

```sql
SELECT last_name,salary,department_id
FROM employees
WHERE salary > (
		SELECT AVG(salary)
		FROM employees
		);
		
#题目：查询员工中工资大于本部门平均工资的员工的last_name,salary和其department_id
#方式1：使用相关子查询
SELECT last_name,salary,department_id
FROM employees e1
WHERE salary > (
		SELECT AVG(salary)
		FROM employees e2
		WHERE department_id = e1.`department_id`
		);

#方式2：在FROM中声明子查询
SELECT e.last_name,e.salary,e.department_id
FROM employees e,(
		SELECT department_id,AVG(salary) avg_sal
		FROM employees
		GROUP BY department_id) t_dept_avg_sal
WHERE e.department_id = t_dept_avg_sal.department_id
AND e.salary > t_dept_avg_sal.avg_sal


#题目：查询员工的id,salary,按照department_name 排序

SELECT employee_id,salary
FROM employees e
ORDER BY (
	 SELECT department_name
	 FROM departments d
	 WHERE e.`department_id` = d.`department_id`
	) ASC;

#结论：在SELECT中，除了GROUP BY 和 LIMIT之外，其他位置都可以声明子查询！
/*
SELECT ....,....,....(存在聚合函数)
FROM ... (LEFT / RIGHT)JOIN ....ON 多表的连接条件 
(LEFT / RIGHT)JOIN ... ON ....
WHERE 不包含聚合函数的过滤条件
GROUP BY ...,....
HAVING 包含聚合函数的过滤条件
ORDER BY ....,...(ASC / DESC )
LIMIT ...,....
*/

#题目：若employees表中employee_id与job_history表中employee_id相同的数目不小于2，
#输出这些相同id的员工的employee_id,last_name和其job_id

SELECT *
FROM job_history;

SELECT employee_id,last_name,job_id
FROM employees e
WHERE 2 <= (
	    SELECT COUNT(*)
	    FROM job_history j
	    WHERE e.`employee_id` = j.`employee_id`
		)
```

### 6.2 EXISTS 与 NOT EXISTS关键字

```sql
#题目：查询公司管理者的employee_id，last_name，job_id，department_id信息
#方式1：自连接
SELECT DISTINCT mgr.employee_id,mgr.last_name,mgr.job_id,mgr.department_id
FROM employees emp JOIN employees mgr
ON emp.manager_id = mgr.employee_id;

#方式2：子查询

SELECT employee_id,last_name,job_id,department_id
FROM employees
WHERE employee_id IN (
			SELECT DISTINCT manager_id
			FROM employees
			);

#方式3：使用EXISTS
SELECT employee_id,last_name,job_id,department_id
FROM employees e1
WHERE EXISTS (
	       SELECT *
	       FROM employees e2
	       WHERE e1.`employee_id` = e2.`manager_id`
	     );

#题目：查询departments表中，不存在于employees表中的部门的department_id和department_name

#方式1：
SELECT d.department_id,d.department_name
FROM employees e RIGHT JOIN departments d
ON e.`department_id` = d.`department_id`
WHERE e.`department_id` IS NULL;

#方式2：
SELECT department_id,department_name
FROM departments d
WHERE NOT EXISTS (
		SELECT *
		FROM employees e
		WHERE d.`department_id` = e.`department_id`
		);

SELECT COUNT(*)
FROM departments;
```

# 创建和管理表

`SELECT * `
`FROM order;`

### 创建和管理数据库

### #1.1 如何创建数据库

```sql
#方式1：
CREATE DATABASE mytest1;  # 创建的此数据库使用的是默认的字符集

#查看创建数据库的结构
SHOW CREATE DATABASE mytest1;

#方式2：显式了指名了要创建的数据库的字符集
CREATE DATABASE mytest2 CHARACTER SET 'gbk';

#
SHOW CREATE DATABASE mytest2;

#方式3（推荐）：如果要创建的数据库已经存在，则创建不成功，但不会报错。
CREATE DATABASE IF NOT EXISTS mytest2 CHARACTER SET 'utf8';

#如果要创建的数据库不存在，则创建成功
CREATE DATABASE IF NOT EXISTS mytest3 CHARACTER SET 'utf8';

SHOW DATABASES;
```

### #1.2 管理数据库

```sql
#查看当前连接中的数据库都有哪些
SHOW DATABASES;

#切换数据库
USE atguigudb;

#查看当前数据库中保存的数据表
SHOW TABLES;

#查看当前使用的数据库
SELECT DATABASE() FROM DUAL;

#查看指定数据库下保存的数据表
SHOW TABLES FROM mysql;
```

### #1.3 修改数据库

```sql
#更改数据库字符集
SHOW CREATE DATABASE mytest2;

ALTER DATABASE mytest2 CHARACTER SET 'utf8';
```

### #1.4 删除数据库

```sql
#方式1：如果要删除的数据库存在，则删除成功。如果不存在，则报错
DROP DATABASE mytest1;

SHOW DATABASES;

#方式2：推荐。 如果要删除的数据库存在，则删除成功。如果不存在，则默默结束，不会报错。
DROP DATABASE IF EXISTS mytest1;

DROP DATABASE IF EXISTS mytest2;
```

## #2. 如何创建数据表

```sql
USE atguigudb;

SHOW CREATE DATABASE atguigudb; #默认使用的是utf8

SHOW TABLES;
```

### #方式1："白手起家"的方式

```sql
CREATE TABLE IF NOT EXISTS myemp1(   #需要用户具备创建表的权限。
id INT,
emp_name VARCHAR(15), #使用VARCHAR来定义字符串，必须在使用VARCHAR时指明其长度。
hire_date DATE
);
#查看表结构
DESC myemp1;
#查看创建表的语句结构
SHOW CREATE TABLE myemp1; #如果创建表时没有指明使用的字符集，则默认使用表所在的数据库的字符集。
#查看表数据
SELECT * FROM myemp1;
```



### #方式2：基于现有的表，同时导入数据

```sql
#基于查询语句创建的表
CREATE TABLE myemp2
AS
SELECT employee_id,last_name,salary
FROM employees;

DESC myemp2;
DESC employees;

SELECT *
FROM myemp2;

#说明1：查询语句中字段的别名，可以作为新创建的表的字段的名称。
#说明2：此时的查询语句可以结构比较丰富，使用前面章节讲过的各种SELECT
CREATE TABLE myemp3
AS
SELECT e.employee_id emp_id,e.last_name lname,d.department_name
FROM employees e JOIN departments d
ON e.department_id = d.department_id;

SELECT *
FROM myemp3;

DESC myemp3;

#练习1：创建一个表employees_copy，实现对employees表的复制，包括表数据
CREATE TABLE employees_copy
AS
SELECT *
FROM employees;

SELECT * FROM employees_copy;

#练习2：创建一个表employees_blank，实现对employees表的复制，不包括表数据
CREATE TABLE employees_blank
AS
SELECT *
FROM employees
#where department_id > 10000;
WHERE 1 = 2; #山无陵，天地合，乃敢与君绝。


SELECT * FROM employees_blank;
```



## #3. 修改表  --> ALTER TABLE 

`DESC myemp1;`

### 3.1 添加字段（一个）
```sql
ALTER TABLE myemp1
ADD salary DOUBLE(10,2); #默认添加到表中的最后一个字段的位置

ALTER TABLE myemp1
ADD phone_number VARCHAR(20) FIRST;

ALTER TABLE myemp1
ADD email VARCHAR(45) AFTER emp_name;
```

### 3.2 修改一个字段：数据类型、长度、默认值（略）
```sql
ALTER TABLE myemp1
MODIFY emp_name VARCHAR(25) ;

ALTER TABLE myemp1
MODIFY emp_name VARCHAR(35) DEFAULT 'aaa';
```

### 3.3 重命名一个字段
```sql
ALTER TABLE myemp1
CHANGE salary monthly_salary DOUBLE(10,2);

ALTER TABLE myemp1
CHANGE email my_email VARCHAR(50);
```

### 3.4 删除一个字段
ALTER TABLE myemp1
DROP COLUMN my_email;

## #4. 重命名表

```sql
#方式1：
RENAME TABLE myemp1
TO myemp11;

DESC myemp11;

#方式2：
ALTER TABLE myemp2
RENAME TO myemp12;

DESC myemp12;
```

## #5. 删除表

```sql
#不光将表结构删除掉，同时表中的数据也删除掉，释放表空间
DROP TABLE IF EXISTS myemp2;

DROP TABLE IF EXISTS myemp12;
```

## #6. 清空表

```sql
#清空表，表示清空表中的所有数据，但是表结构保留。

SELECT * FROM employees_copy;

TRUNCATE TABLE employees_copy;

SELECT * FROM employees_copy;

DESC employees_copy;
```

## #7. DCL 中 COMMIT 和 ROLLBACK

```sql
COMMIT:提交数据。一旦执行COMMIT，则数据就被永久的保存在了数据库中，意味着数据不可以回滚。

ROLLBACK:回滚数据。一旦执行ROLLBACK,则可以实现数据的回滚。回滚到最近的一次COMMIT之后。
```

## #8. 对比 TRUNCATE TABLE 和 DELETE FROM 

```sql
相同点：都可以实现对表中所有数据的删除，同时保留表结构。

不同点：

TRUNCATE TABLE：一旦执行此操作，表数据全部清除。同时，数据是不可以回滚的。

DELETE FROM：一旦执行此操作，表数据可以全部清除（不带WHERE）。同时，数据是可以实现回滚的。

/*

9. DDL 和 DML 的说明
   ① DDL的操作一旦执行，就不可回滚。指令SET autocommit = FALSE对DDL操作失效。(因为在执行完DDL
   操作之后，一定会执行一次COMMIT。而此COMMIT操作不受SET autocommit = FALSE影响的。)

  ② DML的操作默认情况，一旦执行，也是不可回滚的。但是，如果在执行DML之前，执行了 
    SET autocommit = FALSE，则执行的DML操作就可以实现回滚。

*/

演示：DELETE FROM 

#1)
COMMIT;
#2)
SELECT *
FROM myemp3;
#3)
SET autocommit = FALSE;
#4)
DELETE FROM myemp3;
#5)
SELECT *
FROM myemp3;
#6)
ROLLBACK;
#7)
SELECT *
FROM myemp3;

演示：TRUNCATE TABLE

#1)
COMMIT;
#2)
SELECT *
FROM myemp3;
#3)
SET autocommit = FALSE;
#4)
TRUNCATE TABLE myemp3;
#5)
SELECT *
FROM myemp3;
#6)
ROLLBACK;
#7)
SELECT *
FROM myemp3;

#######################
```



## #9.测试MySQL8.0的新特性：DDL的原子化

```sql
CREATE DATABASE mytest;

USE mytest;

CREATE TABLE book1(
book_id INT ,rr
book_name VARCHAR(255)
);

SHOW TABLES;

DROP TABLE book1,book2;

SHOW TABLES;
```

## 内容拓展



### 拓展1：阿里巴巴《Java开发手册》之MySQL

字段命名【强制】表名、字段名必须使用小写字母或数字，禁止出现数字开头，禁止两个下划线中间只出现数字。数据库字段名的修改代价很大，因为无法进行预发布，所以字段名称需要慎重考虑。

**正例：aliyun_admin，rdc_config，level3_name**

**反例：AliyunAdmin，rdcConfig，level_3_name**

【 强制 】禁用保留字，如 desc、range、match、delayed 等，请参考 MySQL 官方保留字。【 强制 】表必备三字段：id, gmt_create, gmt_modified。

说明：其中 id 必为主键，类型为BIGINT UNSIGNED、单表时自增、步长为 1。gmt_create, gmt_modified 的类型均为 DATETIME 

类型，前者现在时表示主动式创建，后者过去分词表示被动式更新

【 推荐 】表的命名最好是遵循 “业务名称_表的作用”。正例：alipay_task 、 force_project、 trade_config

【 推荐 】库名与应用名称尽量一致。

【参考】合适的字符存储长度，不但节约数据库表空间、节约索引存储，更重要的是提升检索速度。

正例：无符号值可以避免误存负数，且扩大了表示范围

![image-20240531184947834](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240531184947834.png)

`表删除` 操作将把表的定义和表中的数据一起删除，并且**MySQL在执行删除操作时，不会有任何的确认信息提示，**因此执行删除操时应当慎重。在删除表前，最好对表中的数据进行` 备份` ，这样当操作失误时可以对数据进行恢复，以免造成无法挽回的后果。

同样的，在使用 `ALTER TABLE `进行表的基本修改操作时，在执行操作过程之前，也应该确保对数据进行完整的 备份 ，因为数据库的改变是 无法撤销 的，如果添加了一个不需要的字段，可以将其删除；相同的，如果删除了一个需要的列，该列下面的所有数据都将会丢失。

### 拓展3：MySQL8新特性—DDL的原子化

在MySQL 8.0版本中，InnoDB表的DDL支持事务完整性，即 DDL操作要么成功要么回滚 。DDL操作回滚日志写入到data dictionary数据字典表mysql.innodb_ddl_log（该表是隐藏的表，通过show tables无法看到）中，用于回滚操作。通过设置参数，可将DDL操作日志打印输出到MySQL错误日志中。

分别在MySQL 5.7版本和MySQL 8.0版本中创建数据库和数据表，结果如下：

```sql
CREATE DATABASE mytest
USE mytest;
CREATE TABLE book1(
book_id INT ,
book_name VARCHAR(255)
);
SHOW TABLES;
```

1）在MySQL 5.7版本中，测试步骤如下： 删除数据表book1和数据表book2，结果如下：

```sql
mysql> DROP TABLE book1,book2;
ERROR 1051 (42S02): Unknown table 'mytest.book2'
```

再次查询数据库中的数据表名称，结果如下：

```sql
mysql> SHOW TABLES;
Empty set (0.00 sec)
```

从结果可以看出，虽然删除操作时报错了，但是仍然删除了数据表book1。



2）在MySQL 8.0版本中，测试步骤如下： 删除数据表book1和数据表book2

，结果如下：

```sql
mysql> DROP TABLE book1,book2;
ERROR 1051 (42S02): Unknown table 'mytest.book2'
```



再次查询数据库中的数据表名称，结果如下：

```sql
mysql> show tables;
+------------------+
| Tables_in_mytest |
+------------------+
| book1 |
+------------------+
1 row in set (0.00 sec)
```

从结果可以看出，数据表book1并没有被删除。



# 数据处理之增删改

## #**0**. **储备工作**

```sql
USE atguigudb;

CREATE TABLE IF NOT EXISTS emp1(
id INT,
`name` VARCHAR(15),
hire_date DATE,
salary DOUBLE(10,2)
);

DESC emp1;

SELECT *
FROM emp1;
```

## **#1. 添加数据**

#方式1：一条一条的添加数据

```sql
① 没有指明添加的字段

#正确的
INSERT INTO emp1
VALUES (1,'Tom','2000-12-21',3400); #注意：一定要按照声明的字段的先后顺序添加
#错误的
INSERT INTO emp1
VALUES (2,3400,'2000-12-21','Jerry');

② 指明要添加的字段 （推荐）

INSERT INTO emp1(id,hire_date,salary,`name`)
VALUES(2,'1999-09-09',4000,'Jerry');

说明：没有进行赋值的hire_date 的值为 null

INSERT INTO emp1(id,salary,`name`)
VALUES(3,4500,'shk');

③ 同时插入多条记录 （推荐）

INSERT INTO emp1(id,NAME,salary)
VALUES
(4,'Jim',5000),
(5,'张俊杰',5500);
```

### #方式2：将查询结果插入到表中

```sql
SELECT * FROM emp1;

INSERT INTO emp1(id,NAME,salary,hire_date)
#查询语句
SELECT employee_id,last_name,salary,hire_date  # 查询的字段一定要与添加到的表的字段一一对应
FROM employees
WHERE department_id IN (70,60);

DESC emp1;
DESC employees;

#说明：emp1表中要添加数据的字段的长度不能低于employees表中查询的字段的长度。

如果emp1表中要添加数据的字段的长度低于employees表中查询的字段的长度的话，就有添加不成功的风险。
```

## #2. 更新数据 （或修改数据）

```sql
UPDATE .... SET .... WHERE ...

可以实现批量修改数据的。

UPDATE emp1
SET hire_date = CURDATE()
WHERE id = 5;

SELECT * FROM emp1;

#同时修改一条数据的多个字段
UPDATE emp1
SET hire_date = CURDATE(),salary = 6000
WHERE id = 4;

#题目：将表中姓名中包含字符a的提薪20%
UPDATE emp1
SET salary = salary * 1.2
WHERE NAME LIKE '%a%';

#修改数据时，是可能存在不成功的情况的。（可能是由于约束的影响造成的）
UPDATE employees
SET department_id = 10000
WHERE employee_id = 102;
```

## #3. 删除数据 DELETE FROM .... WHERE....

```sql
DELETE FROM emp1
WHERE id = 1;

#在删除数据时，也有可能因为约束的影响，导致删除失败
DELETE FROM departments
WHERE department_id = 50;

#小结：DML操作默认情况下，执行完以后都会自动提交数据。

如果希望执行完以后不自动提交数据，则需要使用 SET autocommit = FALSE.
```

## #4. MySQL8的新特性：计算列

```sql
USE atguigudb;

CREATE TABLE test1(
a INT,
b INT,
c INT GENERATED ALWAYS AS (a + b) VIRTUAL  #字段c即为计算列
);

INSERT INTO test1(a,b)
VALUES(10,20);

SELECT * FROM test1;

UPDATE test1
SET a = 100;
```

## #5.综合案例

```sql
1、创建数据库test01_library

CREATE DATABASE IF NOT EXISTS test01_library CHARACTER SET 'utf8';

USE test01_library;

2、创建表 books，表结构如下：

CREATE TABLE IF NOT EXISTS books(
id INT,
`name` VARCHAR(50),
`authors` VARCHAR(100),
price FLOAT,
pubdate YEAR,
note VARCHAR(100),
num INT
);

DESC books;

SELECT * FROM books;

3、向books表中插入记录

1）不指定字段名称，插入第一条记录

INSERT INTO books
VALUES(1,'Tal of AAA','Dickes',23,'1995','novel',11);

2）指定所有字段名称，插入第二记录

INSERT INTO books(id,NAME,AUTHORS,price,pubdate,note,num)
VALUES(2,'EmmaT','Jane lura',35,'1993','joke',22);

3）同时插入多条记录（剩下的所有记录）

INSERT INTO books(id,NAME,AUTHORS,price,pubdate,note,num)
VALUES
(3,'Story of Jane','Jane Tim',40,2001,'novel',0),
(4,'Lovey Day','George Byron',20,2005,'novel',30),
(5,'Old land','Honore Blade',30,2010,'Law',0),
(6,'The Battle','Upton Sara',30,1999,'medicine',40),
(7,'Rose Hood','Richard haggard',28,2008,'cartoon',28);

4、将小说类型(novel)的书的价格都增加5。

UPDATE books
SET price = price + 5
WHERE note = 'novel';

5、将名称为EmmaT的书的价格改为40，并将说明改为drama。

UPDATE books
SET price = 40,note = 'drama'
WHERE NAME = 'EmmaT';

6、删除库存为0的记录。

DELETE FROM books
WHERE num = 0;

7、统计书名中包含a字母的书

SELECT NAME
FROM books
WHERE NAME LIKE '%a%';

8、统计书名中包含a字母的书的数量和库存总量

SELECT COUNT(*),SUM(num)
FROM books
WHERE NAME LIKE '%a%';

9、找出“novel”类型的书，按照价格降序排列

SELECT NAME,note,price
FROM books
WHERE note = 'novel'
ORDER BY price DESC;

10、查询图书信息，按照库存量降序排列，如果库存量相同的按照note升序排列

SELECT *
FROM books
ORDER BY num DESC,note ASC;

11、按照note分类统计书的数量

SELECT note,COUNT(*)
FROM books
GROUP BY note;

12、按照note分类统计书的库存量，显示库存量超过30本的

SELECT note,SUM(num)
FROM books
GROUP BY note
HAVING SUM(num) > 30;

13、查询所有图书，每页显示5本，显示第二页

SELECT *
FROM books
LIMIT 5,5;

14、按照note分类统计书的库存量，显示库存量最多的

SELECT note,SUM(num) sum_num
FROM books
GROUP BY note
ORDER BY sum_num DESC
LIMIT 0,1;

15、查询书名达到10个字符的书，不包括里面的空格

SELECT CHAR_LENGTH(REPLACE(NAME,' ',''))
FROM books;

SELECT NAME
FROM books
WHERE CHAR_LENGTH(REPLACE(NAME,' ','')) >= 10;

16、查询书名和类型，其中note值为novel显示小说，law显示法律，medicine显示医药，

#cartoon显示卡通，joke显示笑话
SELECT NAME "书名",note,CASE note WHEN 'novel' THEN '小说'
				  WHEN 'law' THEN '法律'
				  WHEN 'medicine' THEN '医药'
				  WHEN 'cartoon' THEN '卡通'
				  WHEN 'joke' THEN '笑话'
				  ELSE '其他'
				  END "类型"
FROM books;

17、查询书名、库存，其中num值超过30本的，显示滞销，大于0并低于10的，

#显示畅销，为0的显示需要无货
SELECT NAME AS "书名",num AS "库存", CASE WHEN num > 30 THEN '滞销'
					  WHEN num > 0 AND num < 10 THEN '畅销'
					  WHEN num = 0 THEN '无货'
					  ELSE '正常'
					  END "显示状态"
FROM books;

18、统计每一种note的库存量，并合计总量

SELECT IFNULL(note,'合计库存总量') AS note,SUM(num)
FROM books
GROUP BY note WITH ROLLUP;

19、统计每一种note的数量，并合计总量

SELECT IFNULL(note,'合计总量') AS note,COUNT(*)
FROM books
GROUP BY note WITH ROLLUP;

20、统计库存量前三名的图书

SELECT *
FROM books
ORDER BY num DESC
LIMIT 0,3;

21、找出最早出版的一本书

SELECT *
FROM books
ORDER BY pubdate ASC
LIMIT 0,1;

22、找出novel中价格最高的一本书

SELECT *
FROM books
WHERE note = 'novel'
ORDER BY price DESC
LIMIT 0,1;

23、找出书名中字数最多的一本书，不含空格

SELECT *
FROM books
ORDER BY CHAR_LENGTH(REPLACE(NAME,' ','')) DESC
LIMIT 0,1;
```

# MySQL数据类型精讲  

## MySQL中的数据类型  

| 类型             | 类型举例                                                     |
| ---------------- | ------------------------------------------------------------ |
| 整数类型         | TINYINT、SMALLINT、MEDIUMINT、INT(或INTEGER)、BIGINT         |
| 浮点类型         | FLOAT、DOUBLE,会存在因四舍五入造成的精度损失                 |
| 定点数类型       | DECIMAL                                                      |
| 位类型           | BIT                                                          |
| 日期时间类型     | YEAR、TIME、DATE、DATETIME、TIMESTAMP                        |
| 文本字符串类型   | CHAR、VARCHAR、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT（处理文本数据） |
| 枚举类型         | ENUM                                                         |
| 集合类型         | SET                                                          |
| 二进制字符串类型 | BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB、LONGBLOB（处理非文本数据，比如图片、字节流之类） |
| JSON类型         | JSON对象、JSON数组                                           |
| 空间数据类型     | 单值类型：GEOMETRY、POINT、LINESTRING、POLYGON；集合类型：MULTIPOINT、MULTILINESTRING、MULTIPOLYGON、GEOMETRYCOLLECTION |

常见数据类型的属性，如下：

| MySQL关键字        | 含义                     |
| ------------------ | ------------------------ |
| NULL               | 数据列可包含NULL值       |
| NOT NULL           | 数据列不允许包含NULL值   |
| DEFAULT            | 默认值                   |
| PRIMARY KEY        | 主键                     |
| AUTO_INCREMENT     | 自动递增，适用于整数类型 |
| UNSIGNED           | 无符号                   |
| CHARACTER SET name | 指定一个字符集           |

```sql
#创建数据库时指名字符集
CREATE DATABASE IF NOT EXISTS dbtest12 CHARACTER SET 'UTF8';
SHOW CREATE DATABASE dbtest12

#创建表的时候，指名表的字符集
CREATE TABLE temp(
id INT
) CHARACTER SET 'utf8';

SHOW CREATE TABLE temp;

#创建表，指名表中的字段时，可以指定字段的字符集
CREATE TABLE temp1(
id INT,
NAME VARCHAR(15) CHARACTER SET 'gbk'

);
SHOW CREATE TABLE temp1;
```



## 类型介绍  

### 类型介绍  

整数类型一共有 5 种，包括 `TINYINT、SMALLINT、MEDIUMINT、INT（INTEGER）和 BIGINT`。它们的区别如下表所示：  

| 整数类型     | 字节 | 有符号数取值范围                         | 无符号数取值范围       |
| ------------ | ---- | ---------------------------------------- | ---------------------- |
| TINYINT      | 1    | -128~127                                 | 0~255                  |
| SMALLINT     | 2    | -32768~32767                             | 0~65535                |
| MEDIUMINT    | 3    | -8388608~8388607                         | 0~16777215             |
| INT、INTEGER | 4    | -2147483648~2147483647                   | 0~4294967295           |
| BIGINT       | 8    | -9223372036854775808~9223372036854775807 | 0~18446744073709551615 |

### 可选属性

整数类型的可选属性有三个：

#### 2.2.1 M

M : 表示显示宽度，M的取值范围是(0, 255)。例如，int(5)：当数据宽度小于5位的时候在数字前面需要用字符填满宽度。该项功能需要配合“ ZEROFILL ”使用，表示用“0”

填满宽度，否则指定显示宽度无效。如果设置了显示宽度，那么插入的数据宽度超过显示宽度限制，会不会截断或插入失败？

**答案：**不会对插入的数据有任何影响，还是按照类型的实际宽度进行保存，即 

显示宽度与类型可以存储的值范围无关 。从MySQL 8.0.17开始，整数数据类型不推荐使用显示宽度属性。整型数据类型可以在定义表结构时指定所需要的显示宽度，如果不指定，则系统为每一种类型指定默认的宽度值。举例：

`CREATE TABLE test_int1 ( x TINYINT, y SMALLINT, z MEDIUMINT, m INT, n BIGINT );  `

查看表结构 （MySQL5.7中显式如下，MySQL8中不再显式范围）  

```sql
mysql> desc test_int1;
+-------+--------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| x | tinyint(4) | YES | | NULL | |
| y | smallint(6) | YES | | NULL | |
| z | mediumint(9) | YES | | NULL | |
| m | int(11) | YES | | NULL | |
| n | bigint(20) | YES | | NULL | |
+-------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```

TINYINT有符号数和无符号数的取值范围分别为-128~127和0~255，由于负号占了一个数字位，因此TINYINT默认的显示宽度为4。同理，其他整数类型的默认显示宽度与其有符号数的最小值的宽度相同  .

举例：  

```sql
f2 INT(5),
f3 INT(5) ZEROFILL
) D
ESC test_int2;
INSERT INTO test_int2(f1,f2,f3)
VALUES(1,123,123);
INSERT INTO test_int2(f1,f2)
VALUES(123456,123456);
INSERT INTO test_int2(f1,f2,f3)
VALUES(123456,123456,123456);
```

```sql
mysql> SELECT * FROM test_int2;
+--------+--------+--------+
| f1 | f2 | f3 |
+--------+--------+--------+
| 1 | 123 | 00123 |
| 123456 | 123456 | NULL |
| 123456 | 123456 | 123456 |
+--------+--------+--------+
3 rows in set (0.00 sec)
```

#### 2.2.2   UNSIGNED  

UNSIGNED : 无符号类型（非负），所有的整数类型都有一个可选的属性UNSIGNED（无符号属性），无符号整数类型的最小取值为0。所以，如果需要在MySQL数据库中保存非负整数值时，可以将整数类型设置为无符号类型。int类型默认显示宽度为int(11)，无符号int类型默认显示宽度为int(10)。

```sql
2.2.3 ZEROFILLCREATE TABLE test_int3(
f1 INT UNSIGNED
);
mysql> desc test_int3;
+-------+------------------+------+-----+---------+-------+
| Field | Type | Null | Key | Default | Extra |
+-------+------------------+------+-----+---------+-------+
| f1 | int(10) unsigned | YES | | NULL | |
+-------+------------------+------+-----+---------+-------+
1 row in set (0.00 sec)
```

#### 2.2.3 ZEROFILL  

`ZEROFILL` : 0填充,（如果某列是ZEROFILL，那么MySQL会自动为当前列添加UNSIGNED属性），如果指定了ZEROFILL只是表示不够M位时，用0在左边填充，如果超过M位，只要不超过数据存储范围即可。

原来，在 int(M) 中，M 的值跟 int(M) 所占多少存储空间并无任何关系。 int(3)、int(4)、int(8) 在磁盘上都是占用 4 bytes 的存储空间。也就是说，**int(M)，必须和UNSIGNED ZEROFILL一起使用才有意义。**如果整数值超过M位，就按照实际位数存储。只是无须再用字符 0 进行填充。

`TINYINT` ：一般用于枚举数据，比如系统设定取值范围很小且固定的场景。

`SMALLINT` ：可以用于较小范围的统计数据，比如统计工厂的固定资产库存数量等。

`MEDIUMINT` ：用于较大整数的计算，比如车站每日的客流量等。

`INT、INTEGER` ：取值范围足够大，一般情况下不用考虑超限问题, 用得最多。比如商品编号。

`BIGINT` ：只有当你处理特别巨大的整数时才会用到。比如双十一的交易量、大型门户网站点击量、证券公司衍生产品持仓等。

### 2.4 如何选择？

在评估用哪种整数类型的时候，你需要考虑 存储空间 和 可靠性 的平衡问题：一方 面，用占用字节数少的整数类型可以节省存储空间；另一方面，要是为了节省存储空间， 使用的整数类型取值范围太小，一旦遇到超出取值范围的情况，就可能引起 系统错误 ，影响可靠性。

举个例子，商品编号采用的数据类型是 INT。原因就在于，客户门店中流通的商品种类较多，而且，每天都有旧商品下架，新商品上架，这样不断迭代，日积月累。

如果使用 SMALLINT 类型，虽然占用字节数比 INT 类型的整数少，但是却不能保证数据不会超出范围65535。相反，使用 INT，就能确保有足够大的取值范围，不用担心数据超出范围影响可靠性的问题。

你要注意的是，在实际工作中，系统故障产生的成本远远超过增加几个字段存储空间所产生的成本。因此，我建议你首先确保数据不会超过取值范围，在这个前提之下，再去考虑如何节省存储空间。

## 浮点类型  

### 类型介绍  

浮点数和定点数类型的特点是可以 处理小数 ，你可以把整数看成小数的一个特例。因此，浮点数和定点数的使用场景，比整数大多了。 MySQL支持的浮点数类型，分别是 FLOAT、DOUBLE、REAL。

* FLOAT :表示单精度浮点数；

* DOUBLE 表示双精度浮点数；

![image-20240602213152553](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240602213152553.png)

* REAL默认就是 DOUBLE。如果你把 SQL 模式设定为启用“ REAL_AS_FLOAT ”，那 么，MySQL 就认为REAL 是 FLOAT。如果要启用“REAL_AS_FLOAT”，可以通过以下 SQL 语句实现：  

  `SET sql_mode = “REAL_AS_FLOAT”;  

  问题1：FLOAT 和 DOUBLE 这两种数据类型的区别是啥呢？  

  问题2：为什么浮点数类型的无符号数取值范围，只相当于有符号数取值范围的一半，也就是只相当于有符号数取值范围大于等于零的部分呢？

  MySQL 存储浮点数的格式为： 符号(S) 、 尾数(M) 和 阶码(E) 。因此，无论有没有符号，MySQL 的浮点数都会存储表示符号的部分。因此， 所谓的无符号数取值范围，其实就是有符号数取值范围大于等于零的部分。

  ### 数据精度说明  

  对于浮点类型，在MySQL中单精度值使用 4 个字节，双精度值使用 8 个字节。

  * MySQL允许使用 `非标准语法` （其他数据库未必支持，因此如果涉及到数据迁移，则最好不要这么用）：` FLOAT(M,D) 或 DOUBLE(M,D) 。`这里，M称为 精度 ，D称为 标度 。(M,D)中 M=整数位+小数位，D=小数位。 D<=M<=255，0<=D<=30。
  * 例如，定义为FLOAT(5,2)的一个列可以显示为-999.99-999.99。如果超过这个范围会报错。
  * FLOAT和DOUBLE类型在不指定(M,D)时，默认会按照实际的精度（由实际的硬件和操作系统决定）来显示。
  * 说明：浮点类型，也可以加 UNSIGNED ，但是不会改变数据范围，例如：FLOAT(3,2) UNSIGNED仍然只能表示0-9.99的范围。
  * 不管是否显式设置了精度(M,D)，这里MySQL的处理方案如下：
    * 如果存储时，整数部分超出了范围，MySQL就会报错，不允许存这样的值
    * 如果存储时，小数点部分若超出范围，就分以下情况：
      * 若四舍五入后，整数部分没有超出范围，则只警告，但能成功操作并四舍五入删除多余的小数位后保存。例如在FLOAT(5,2)列内插入999.009，近似结果是999.01。
      * 若四舍五入后，整数部分超出范围，则MySQL报错，并拒绝处理。如FLOAT(5,2)列内插入999.995和-999.995都会报错。

  * 从MySQL 8.0.17开始，FLOAT(M,D) 和DOUBLE(M,D)用法在官方文档中已经明确不推荐使用，将来可能被移除。另外，关于浮点型FLOAT和DOUBLE的UNSIGNED也不推荐使用了，将来也可能被移除。

  ```sql
  CREATE TABLE test_double1(
  f1 FLOAT,
  f2 FLOAT(5,2),
  f3 DOUBLE,
  f4 DOUBLE(5,2)
  );
  DESC test_double1;
  INSERT INTO test_double1
  VALUES(123.456,123.456,123.4567,123.45);
  #Out of range value for column 'f2' at row 1
  INSERT INTO test_double1
  VALUES(123.456,1234.456,123.4567,123.45);
  SELECT * FROM test_double1;
  ```

  浮点数类型有个缺陷，就是不精准。下面我来重点解释一下为什么 MySQL 的浮点数不够精准。比如，我们设计一个表，有f1这个字段，插入值分别为0.47,0.44,0.19，我们期待的运行结果是：0.47 + 0.44 + 0.19 = 1.1。而使用sum之后查询：  

  ```sql
  CREATE TABLE test_double2(
  f1 DOUBLE
  );
  INSERT INTO test_double2
  VALUES(0.47),(0.44),(0.19);
  ```

  ```sql
  mysql> SELECT SUM(f1)
  -> FROM test_double2;
  +--------------------+
  | SUM(f1) |
  +--------------------+
  | 1.0999999999999999 |
  +--------------------+
  1 row in set (0.00 sec)
  ```

  ```sql
  mysql> SELECT SUM(f1) = 1.1,1.1 = 1.1
  -> FROM test_double2;
  +---------------+-----------+
  | SUM(f1) = 1.1 | 1.1 = 1.1 |
  +---------------+-----------+
  | 0 | 1 |
  +---------------+-----------+
  1 row in set (0.00 sec)
  ```

  查询结果是 1.0999999999999999。看到了吗？虽然误差很小，但确实有误差。 你也可以尝试把数据类型改成 FLOAT，然后运行求和查询，得到的是， 1.0999999940395355。显然，误差更大了。

  那么，为什么会存在这样的误差呢？问题还是出在 MySQL 对浮点类型数据的存储方式上。

  MySQL 用 4 个字节存储 FLOAT 类型数据，用 8 个字节来存储 DOUBLE 类型数据。无论哪个，都是采用二进制的方式来进行存储的。比如 9.625，用二进制来表达，就是 1001.101，或者表达成 1.001101×2^3。如果尾数不是 0 或 5（比如 9.624），你就无法用一个二进制数来精确表达。进而，就只好在取值允许的范围内进行四舍五入。

  在编程中，如果用到浮点数，要特别注意误差问题，

  因为浮点数是不准确的，所以我们要避免使用“=”来判断两个数是否相等。同时，在一些对精确度要求较高的项目中，千万不要使用浮点数，不然会导致结果错误，甚至是造成不可挽回的损失。那么，MySQL 有没有精准的数据类型呢？当然有，这就是定点数类型： `DECIMAL `。

## 定点数类型  

### 类型介绍  

* **MySQL中的定点数类型只有 DECIMAL 一种类型。  **

| DECIMAL(M,D),DEC,NUMERIC | M+2字节 | 有效范围由M和D决定 |
| ------------------------ | ------- | ------------------ |

使用 DECIMAL(M,D) 的方式表示高精度小数。其中，M被称为精度，D被称为标度。0<=M<=65， 0<=D<=30，D<M。例如，定义DECIMAL（5,2）的类型，表示该列取值范围是-999.99~999.99。

* DECIMAL(M,D)的最大取值范围与DOUBLE类型一样，但是有效的数据范围是由M和D决定的。DECIMAL 的存储空间并不是固定的，由精度值M决定，总共占用的存储空间为M+2个字节。也就是说，在一些对精度要求不高的场景下，比起占用同样字节长度的定点数，浮点数表达的数值范围可以更大一些。

* 定点数在MySQL内部是以字符串的形式进行存储，这就决定了它一定是精准的。当DECIMAL类型不指定精度和标度时，其默认为DECIMAL(10,0)。当数据的精度超出了定点数类型的精度范围时，则MySQL同样会进行四舍五入处理。
* **浮点数 vs 定点数**
  * 浮点数相对于定点数的优点是在长度一定的情况下，浮点类型取值范围大，但是不精准，适用于需要取值范围大，又可以容忍微小误差的科学计算场景（比如计算化学、分子建模、流体动力学等）
  * 定点数类型取值范围相对小，但是精准，没有误差，适合于对精度要求极高的场景 （比如涉及金额计算的场景）

```sql
CREATE TABLE test_decimal1(
f1 DECIMAL,
f2 DECIMAL(5,2)
);
DESC test_decimal1;
INSERT INTO test_decimal1(f1,f2)
VALUES(123.123,123.456);
#Out of range value for column 'f2' at r
INSERT INTO test_decimal1(f2)
VALUES(1234.34);
mysql> SELECT * FROM test_decimal1;

+------+--------+
| f1 | f2 |
+------+--------+
| 123 | 123.46 |
+------+--------+
1 row in set (0.00 sec)
```

举例

我们运行下面的语句，把test_double2表中字段“f1”的数据类型修改为 DECIMAL(5,2)：

```sql
ALTER TABLE test_double2
MODIFY f1 DECIMAL(5,2);
```

然后，我们再一次运行求和语句：  

```sql
+---------+
| SUM(f1) |
+---------+
| 1.10 |
+---------+
1 row in set (0.00 sec)
mysql> SELECT SUM(f1) = 1.1
-> FROM test_double2;
+---------------+
| SUM(f1) = 1.1 |
+---------------+
| 1 |
+---------------+
1 row in set (0.00 sec)
```

## 位类型：BIT  

BIT类型中存储的是二进制值，类似010110。  

| 二进制字符串类型 | 长度 | 长度范围     | 占用空间            |
| ---------------- | ---- | ------------ | ------------------- |
| BIT(M)           | M    | 1 <= M <= 64 | 约为(M + 7)/8个字节 |

BIT类型，如果没有指定(M)，默认是1位。这个1位，表示只能存1位的二进制值。这里(M)是表示二进制的位数，位数最小值为1，最大值为64。  

```sql
CREATE TABLE test_bit1(
f1 BIT,
f2 BIT(5),
f3 BIT(64)
);
INSERT INTO test_bit1(f1)
VALUES(1);
#Data too long for column 'f1' at row 1
INSERT INTO test_bit1(f1)
VALUES(2);
INSERT INTO test_bit1(f2)
VALUES(23);
```

注意：在向BIT类型的字段中插入数据时，一定要确保插入的数据在BIT类型支持的范围内。  

```sql
mysql> SELECT * FROM test_bit1;
+------------+------------+------------
| f1 | f2 | f3
+------------+------------+------------
| 0x01 | NULL | NULL
| NULL | 0x17 | NULL
+------------+------------+------------
2 rows in set (0.00 sec)
mysql> SELECT BIN(f2),HEX(f2)
-> FROM test_bit1;
+---------+---------+
| BIN(f2) | HEX(f2) |
+---------+---------+
| NULL | NULL |
| 10111 | 17 |
+---------+---------+
2 rows in set (0.00 sec)
mysql> SELECT f2 + 0
-> FROM test_bit1;
+--------+
| f2 + 0 |
+--------+
| NULL |
| 23 |
+--------+
2 rows in set (0.00 sec)
```

可以看到，使用b+0查询数据时，可以直接查询出存储的十进制数据的值。  

## 日期与时间类  

日期与时间是重要的信息，在我们的系统中，几乎所有的数据表都用得到。原因是客户需要知道数据的时间标签，从而进行数据查询、统计和处理。

MySQL有多种表示日期和时间的数据类型，不同的版本可能有所差异，MySQL8.0版本支持的日期和时间类型主要有：YEAR类型、TIME类型、DATE类型、DATETIME类型和TIMESTAMP

类型。

`YEAR `类型通常用来表示年

`DATE` 类型通常用来表示年、月、日

`TIME `类型通常用来表示时、分、秒

`DATETIME `类型通常用来表示年、月、日、时、分、秒

`TIMESTAMP `类型通常用来表示带时区的年、月、日、时、分、秒

| 类型      | 名称     | 字节 | 日期格式           | 最小值                 | 最大值                |
| --------- | -------- | ---- | ------------------ | ---------------------- | --------------------- |
| YEAR      | 年       | 1    | YYYY或YY           | 1901                   | 2155                  |
| TIME      | 时间     | 3    | HH:MM:SS           | -838:59:59             | 838:59:59             |
| DATE      | 日期     | 3    | YYYY-MM-DD         | 1000-01-01             | 9999-12-03            |
| DATETIME  | 日期时间 | 8    | YYYY-MM-DDHH:MM:SS | 1000-01-0100:00:00     | 9999-12-3123:59:59    |
| TIMESTAMP | 日期时间 | 4    | YYYY-MM-DDHH:MM:SS | 1970-01-0100:00:00 UTC | 2038-01-1903:14:07UTC |

# 约束

1.1 为什么需要约束

数据完整性（Data Integrity）是指数据的精确性（Accuracy）和可靠性（Reliability）。它是防止数据库中存在不符合语义规定的数据和防止因错误信息的输入输出造成无效操作或错误信息而提出的。

为了保证数据的完整性，SQL规范以约束的方式对表数据进行**额外的条件限制**。从以下四个方面考虑：

* 实体完整性（Entity Integrity） ：例如，同一个表中，不能存在两条完全相同无法区分的记录

* 域完整性（Domain Integrity） ：例如：年龄范围0-120，性别范围“男/女”

* 引用完整性（Referential Integrity） ：例如：员工所在部门，在部门表中要能找到这个部门

* 用户自定义完整性（User-defined Integrity） ：例如：用户名唯一、密码不能为空等，本部门经理的工资不得高于本部门职工的平均工资的5倍。

1.2 什么是约束

约束是表级的强制规定。可以在**创建表时规定约束**（通过 CREATE TABLE 语句），或者在**表创建之后通过 ALTER TABLE 语句规定约束**。

​			位置   		支持的约束类型 					是否可以起约束名

列级约束： 列的后面 		语法都支持，					但外键没有效果 不可以

表级约束： 所有列的下面 	默认和非空不支持，其他支持 	    可以（主键没有效果）

根据约束起的作用，约束可分为：

* NOT NULL 非空约束，规定某个字段不能为空

* UNIQUE 唯一约束，规定某个字段在整个表中是唯一的
* PRIMARY KEY 主键(非空且唯一)约束
* FOREIGN KEY 外键约束
* CHECK 检查约束
* DEFAULT 默认值约束

查看某个表已有的约束

```sql
#information_schema数据库名（系统库）

#table_constraints表名称（专门存储各个表的约束）

SELECT * FROM information_schema.table_constraints

WHERE table_name = '表名称';
```

## 非空约束

### 2.1 作用

限定某个字段/某列的值不允许为空

### 2.2 关键字

NOT NULL

### 2.3 特点

1.默认，所有的类型的值都可以是NULL，包括INT、FLOAT等数据类型

2.非空约束只能出现在表对象的列上，只能某个列单独限定非空，不能组合非空

3.一个表可以有很多列都分别限定了非空

4.空字符串''不等于NULL，0也不等于NULL

...查看文件G:\个人\02转码学习资料\12 SQL\课件pdf版\课件

# 视图

## 常见的数据库对象  

| 对象                | 描述                                                         |
| ------------------- | ------------------------------------------------------------ |
| 表(TABLE)           | 表是存储数据的逻辑单元，以行和列的形式存在，列就是字段，行就是记录 |
| 数据字典            | 就是系统表，存放数据库相关信息的表。系统表的数据通常由数据库系统维护，程序员通常不应该修改，只可查看 |
| 约束(CONSTRAINT)    | 执行数据校验的规则，用于保证数据完整性的规则                 |
| 视图(VIEW)          | 一个或者多个数据表里的数据的逻辑显示，视图并不存储数据       |
| 索引(INDEX)         | 用于提高查询性能，相当于书的目录                             |
| 存储过程(PROCEDURE) | 用于完成一次完整的业务处理，没有返回值，但可通过传出参数将多个值传给调用环境 |
| 存储函数(FUNCTION)  | 用于完成一次特定的计算，具有一个返回值                       |
| 触发器(TRIGGER)     | 相当于一个事件监听器，当数据库发生特定事件后，触发器被触发，完成相应的处理 |

## 视图概述  

### 为什么使用视图？  

### 视图的理解  

* 视图是一种 虚拟表 ，本身是 不具有数据 的，占用很少的内存空间，它是 SQL 中的一个重要概念。

* 视图建立在已有表的基础上, 视图赖以建立的这些表称为基表。  

![image-20240611123347099](https://cdn.jsdelivr.net/gh/Linff214/picodemo/img/image-20240611123347099.png)

* 视图的创建和删除只影响视图本身，不影响对应的基表。但是当对视图中的数据进行增加、删除和修改操作时，数据表中的数据会相应地发生变化，反之亦然。 
*  在数据库中，视图不会保存数据，数据真正保存在数据表中。当对视图中的数据进行增加、删除和修改操作时，数据表中的数据会相应地发生变化；反之亦然。
* 视图，是向用户提供基表数据的另一种表现形式。通常情况下，小型项目的数据库可以不使用视图，但是在大型项目中，以及数据表比较复杂的情况下，视图的价值就凸显出来了，它可以帮助我们把经常查询的结果集放到虚拟表中，提升使用效率。理解和使用起来都非常方便。
