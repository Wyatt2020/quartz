---
created: 2023-11-23 17:42
modified: 2023-11-23 17:42
tags:
  - MySQL
---

## 一、MySQL 数据库操作

### 1. 创建数据库 CREATE DATABASE

```mysql
CREATE DATABASE `数据库名称`;
```

> 数据库名称命名规则：
> 
> 1.  不能与其他数据库重名。
> 2.  名称可以由任意字母、阿拉伯数字、下划线 (`_`)和 `$` 组成，可以使用上述的字符开头，但不能使用单独的数字。
> 3.  名称最长可 64 个字符，别名最长 256 个字符。
> 4.  默认情况下，在 Windows 平台下数据库名、表名不区分大小写，而 Linux 下区分大小写，建议全部使用小写构建数据库名和表名。

___

### 2. 查看数据库 SHOW DATABASES

```mysql
SHOW DATABASES;
```

___

### 3. 选择数据库 USE DATABASE

```mysql
USE `数据库名称`;
```

___

### 4. 删除数据库 DROP DATABASE

```mysql
DROP DATABASE `数据库名称`;
```

> 删库需谨慎

___

  

## 二、MySQL 数据表操作

### 1. 创建数据表 CREATE TABLE

**快捷创建**：

```mysql
create table table_name (列名1 属性,列名2 属性,...);
```

**示例**：

```mysql
create table db_admin(
id int auto_increment primarykey,
user varchat(32) not null,
password varchar(32) not null,
createtime datetime
);
```

**详细分析**：

```mysql
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] 数据表名[(create_definition,...)] [table_options] [select_statement]
```

CREATE TABLE 语句的参数说明如下：

| 参数 | 说明 |
| --- | --- |
| TEMPORARY | 使用该关键字创建一个临时表 |
| IF NOT EXISTS | 用于避免表存在时 MySQL 报错 |
| create\_definition | 表的列属性部分，至少一列 |
| table\_options | 表的一些特性参数 |
| select\_statement | SELECT 语句描述部分，用它可以快速的创建表 |

下面是 create\_definition 部分，每一列定义的具体格式：

```mysql
col_name type [NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [PRIMARY KEY] [reference_definition]
```

列属性 create\_definition 参数说明：

| 参数 | 说明 |
| --- | --- |
| col\_name | 字段名 |
| type | 字段类型 |
| NOT NULL | NULL | 指出该列是否允许是空值 |
| DEFAULT default\_value | 默认值 |
| AUTO\_INCREMENT | 是否为自动编号，表中只能有一个 AUTO\_INCREMENT 列，并且必须被索引 |
| PRIMARY KEY | 是否为主键，表中只能有一个 PRIMARY KEY |
| reference\_definition | 为字段添加注释 |

> PRIMARY KEY：如果一个表中没有 PRIMARY KEY，而某些应用程序需要 PRIMARY KEY，MySQL 将返回第一个没有任何 NULL 列的 UNIQUE 键作为 PRIMARY KEY。

___

### 2. 查看数据表结构 SHOW COLUMNS 或 DESCRIBE

**2.1 SHOW COLUMNS**

```mysql
SHOW [FULL] COLUMNS FROM 数据表名 [FROM 数据库名];
--或者
SHOW [FULL] COLUMNS FROM 数据库名.数据表名;
```

**2.2 DESCRIBE**

```mysql
DESCRIBE 数据表名称 [列名];
--或者
DESC 数据表名称 [列名];
```

___

### 3. 查看键表语句 SHOW CREATE TABLE

**SHOW CREATE TABLE table\_name；**

```mysql
--示例:查看user表的键表语句
show create table user;
CREATE TABLE `user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(32) DEFAULT NULL,
  PRIMARY KEY (`id`),
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;
```

___

  

### 4. 修改表结构 ALTER TABLE

> 通过 alter 修改表结构必须是表中数据全部删除之后才能进行修改

修改表结构指的是：增加或删除字段、修改字段名称或类型、取消设置主键外键、取消设置索引以及修改表的注释等。语法：

**示例**

在 tb\_admin 添加一个新的 email，类型 varchar (50)，not null，将字段 user 的类型由 varchar (30) 改为 varchar(40)

```mysql
alter table tb_admin add email varchat(50) not null,modify user varchar(40);
```

**详细讲解**

```mysql
ALTER[IGNORE] TABLE 数据表名 alter_spec[,alter_spec]...
```

> 当指定 IGNORE 时，如果出现重复关键的行，则只执行一行，其他重复的行被删除

其中，alter\_spec 子句定义要修改的内容：

```mysql
alter_specification:
ADD [COLUM] create_definition [FIRSR | AFTER column_name ]--添加新字段
| ADD INDEX [index_name] (index_col_name,...)--添加索引名称
| ADD PRIMARY KEY (index_col_name,...)--添加主键名称
| ADD UNIQUE [index_name] (index_col_name,...)--添加唯一索引
| ALTER [COLUMN] col_name {SET DEFAULT literal | DROP DEFAULT}--修改字段名称
| CHANGE [COLUMN] old_col_name create_definition--修改字段类型
| MODIFY [COLUMN] create_definition--修改子句
| DROP [COLUMN] col_name--删除子段名称
| DROP PRIMARY KEY--删除主键名称
| DROP INDEX index_name--删除索引名称
| RENAME [AS] new_tbl_name--更改表名
| table_options
```

> ALTER TABLE 语句允许指定多个动作，每个动作使用逗号分隔。

___

### 5. 重命名表 RENAME TABLE

> 该语句可以同时对多个数据表进行重命名，多个表之间以逗号 `,` 分隔：

```mysql
RENAME TABLE 数据表名1 To 数据表名2
```

示例（将 tb\_admin 表重命名为 tb\_user）：

```mysql
RENAME TABLE 数据表名1 To 数据表名2
```

___

### 6. 删除表 DROP TABLE

```mysql
DROP TABLE 数据表名;
--或
DROP TABLE IF EXISTS 数据表名;
```

> 删表需要谨慎

___

  

## 三、数据表高级操作

### 1. 克隆表

```mysql
--实现克隆表
--1. 方法一 LIKE
CREATE TABLE 新表名 LIKE 被克隆表名;
INSERT INTO 新表名 SELECT * FROM 被克隆表名;

--2. 方法二
CREATE TABLE 新表名 (SELECT * FROM 被克隆表名);
```

> 复制会有瓶颈，不能完全复制，Key 的属性就无法复制

___

### 2. 清空表

```mysql
--清空表
--1. 记录自增 ID 未删除
DELETE FORM 表名;
--DELETE 是一行一行的删除记录。若有自增字段，再次添加会从原记录最大的自增字段开始写入记录

--2. 记录自增 ID 删除
TRUNCATE TABLE 表名;
--TRUNCATE 不会返回被删除条目，TRUNCATE 是将表重建，速度快于 DELETE，自增值从初始值开始
```

___

### 3. 创建临时表

> 使用 SHOW TABLES 是看不见临时表的，临时表会在连接推出后销毁。

```mysql
CREATE TEMPORARY TABLE 表名 (...);
--示例:
create temporary table tb_admin (
id int (4) zerofill primary key auto_increment,
    name varchar (32) not null,
    sex char (2) not null
);
```

___

  

## 四、MySQL 语句操作

### 1. 插入记录 INSERT

```mysql
insert into 数据表名[(column_name 1, column_name 2,...)] value (v 1, v 2,...)[, (v 1, v 2,...)];
--后面给定多个括号进行批量添加，中间使用逗号隔开
```

___

### 2. 查询数据库记录 SELECT

```mysql
SELECT [DISTINCT] [CONCAT (col 1,": ", col 2) as col] selection_list --要查询的内容, 选择哪些列
FROM 数据表名 tb_list--指定数据表
WHERE primary_constraint--查询时需要满足的内容
GROUP BY grouping_columns--如何对结果进行分组
ORDER BY sorting_columns--如何对结果进行排序
HAVING secondary_constraint--查询时满足的第二条件
LIMIT count--限定输出的查询结果
```

这就是 SELECT 查询语句的语法，下面对它的参数进行讲解：

**1. selection\_list**

设置查询内容，查询所有列使用`*`，多个列名以`,`分隔。

```mysql
select * from tb_admin;--查询数据表中所有数据
select user, pass from tb_admin;--查询数据表中 user 和 pass 列的数据
```

**2. tb\_list**

数据表名，既可以从一个数据表中查询，也可以从多个数据表中查询，使用`,`隔开多个数据表，并且通过 WHERE 子句确定表之间的联系。

```mysql
select tb_book. id, tb_book. name, tb_bookinfo. author, tb_bookinfo. price--查询的列
from tb_book, tb_bookinfo --数据表
where tb_book. name = tb_bookinfo. name--匹配条件
and tb_bookinfo. name = '演员的自我修养';--外部条件
```

**3. where 条件语句**

设定查询条件，可以通过它实现很多复杂的条件查询。where 子句中常用的比较运算符：

| 运算符 | 名称 | 示例 |
| --- | --- | --- |
| \= | 等于 | id=2 |
| \> | 大于 | id>2 |
| \>= | 大于等于 | id>=2 |
| < | 小于 | id<2 |
| <= | 小于等于 | id<=2 |
| != 或 <> | 不大于 | id!=2 或 id<>2 |
| IS NULL | n/a (Not applicable) | id IS NULL |
| IS NOT NULL | n/a | id IS NOT NULL |
| BETWEEN … AND … | n/a | id BETWEEN 2 AND 12 |
| IN | n/a | id IN (1,2,3) |
| NOT IN | n/a | id NOT IN (1,2,3) |
| LIKE | 模式匹配 | name LIKE (‘shi%’) |
| NOT LIKE | 模式匹配 | name NOT LIKE (‘shi%’) |
| REGEXP | 正则表达式 | name 正则表达式 |

示例：

```mysql
--在 tb_admin 表中查询 id 为 1,2,3 或 name 开头为 p 的所有列
select * from tb_admin where id IN (1,2,3) OR name LIKE ('p%');
```

> 模式匹配中的符号得学习一下

**4. GROUP BY**

将数据划分到不同的组中，实现对记录的分组查询，在与 AVG ()和 SUM ()函数一起使用时，GROUP BY 子句发挥最大作用。示例：

```mysql
--查询 tb_book 表，按照 type 分组，求每类图书的平均价格,
select type,avg (price) from tb_book group by type;
```

**5. DISTINCT**

去除重复的行。

```mysql
--查询 tb_book 表，除去重复的 type 数据
select distinct type from tb_book;
```

**6. ORDER BY**

对查询结果进行升序（默认）或降序（DESC）排列。升序 NULL 在最前面，降序 NULL 在最后面。

```mysql
--查询 tb_book 表中的所有记录，按照 id 降序排列，显示三条记录
select * from tb_book order by id desc limit 3;
```

**7. LIKE 模糊查询**

可实现模糊查询，它有两种通配符：`%`和`_`，`%`可以匹配一个或多个字符，`_`可以匹配一个字符。示例：

```mysql
--查找所有第二个字符是 h 的图书
select * from tb_book where name like ('_h%');
```

> 单个中文也算一个字符，比如`卷`就算一个字符。

**8. CONCAT 联合多列**

联合多个字段，构成一个总的字符串。

```mysql
--把 name 和 price 合并成一个新的字符串输出
select id,concat (name,": ", price) as info, type from tb_book;
```

**9. LIMIT 限定结果行数**

示例：

```mysql
--1. 降序显示 3 条记录
select * from tb_book order by price desc limit 3;

--2. 从 id=1 显示 4 条记录
select * from tb_book where id limit 1,4;
```

**10. 使用函数和表达式**

示例：

```mysql
--计算 tb_book 表中各类图书的总价格
select sum (price) as total, type from tb_book group by type;
--price 打八折
select *, (price * 0.8) as "八折" from tb_book;
```

下面是常用统计函数：

| 名称         | 说明                                                                                                                          |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------- |
| avg (字段名) | 获取指定列的平均值                                                                                                            |
| count ()     | 如果指定了一个字段会统计出该字段非空记录；在前面加上 DISTINCT 会统计不同的记录；  使用 COUNT (\*)会统计包括 NULL 的所有记录数 |
| min ()       | 获取指定字段的最小值                                                                                                          |
| max ()       | 获取指定字段的最大值                                                                                                          |
| std ()       | 指定字段的标准背离值                                                                                                          |
| stdtev ()    | 与 STD 相同，为了兼容 Oracle                                                                                                  |
| sum ()       | 指定字段所有记录的总和                                                                                                        |

> 还可使用算术运算符、字符串运算符和逻辑运算符构成表达式

___

### 3. 修改记录 UPDATE

```mysql
UPDATE 数据表名 SET column_name 1 = new_v 1, column_name 2 = new_v 2,... where condition;
```

其中`set`子句指出要修改的列和它们所给定的值；where 可选，给出则指定某一行更新，否则全部记录行被更新。

```mysql
--将 username=admin 的密码修改为 123
update tb_user set password = '123' where username = 'admin';
```

> 更新时一定要保证 WHERE 子句正确，一旦 WHERE 子句出错，将会破坏所有改变的数据

___

### 4. 删除记录 DELETE

```mysql
DELETE FROM 数据表名 WHERE condition;
```

> 删除需谨慎，一般 where 条件以 id 为判断，减少很多不必要的麻烦

___

  

## 五、数据库用户操作

### 1. 新建用户 USER

```sql
CREATE USER '用户名'@'来源地址' [IDENTIFIED BY [PASSWORD] '密码'];

-----------------------------------解释--------------------------------
'用户名'：指定将创建的用户名
'来源地址'：指定新创建的用户可在哪些主机上登录，可使用 IP 地址、网段、主机名的形式，
          本地用户可用 localhost，允许任意主机登录可用通配符%
'密码'：若使用明文密码，直接输入'密码'，插入到数据库时由 Mysql 自动加密；
       若使用加密密码，需要先使用 SELECT PASSWORD ('密码'); 获取密文，再在语句中添加 PASSWORD '密文';
       若省略“IDENTIFIED BY”部分，则用户的密码将为空（不建议使用）
----------------------------------------------------------------------
例如：
create user 'test 1'@'localhost' IDENTIFIED BY '123456';

select password ('123456');
create user 'test 2'@'localhost' IDENTIFIED
BY PASSWORD '*6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9';
```

___

### 2. 查看用户信息

> 创建后的用户保存在 mysql 数据库的 user 表里

```sql
--切换数据库
use mysql;
select user, authentication_string, Host from user;
```

___

### 3. 重命名用户名

```sql
RENAME USER '用户名'@'来源地址' TO '新用户名'@'新来源地址';
```

___

### 4. 删除用户

```sql
DROP USER '用户名'@'来源地址';
```

来源地址可填 `IP`、`主机名`、`%`

___

### 5. 修改用户密码

```sql
--1. 修改当前登陆用户密码
SET PASSWORD = PASSWORD('123');

--2. 修改其他用户密码
SET PASSWORD FOR '用户名'@'来源地址' = PASSWORD('123');
```

___

### 6. 忘记 root 密码的解决办法

在 /etc/my. cnf 配置文件下的\[mysqld\]添加：`skip-grant-tables`，之后重启服务，直接输入 mysql 就可以登陆

![在这里插入图片描述](https://img-blog.csdnimg.cn/016d172f921d40d19e3bea8e58a40167.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3llYWhQZW5nMTE=,size_16,color_FFFFFF,t_70#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/e5e028709825485aa661d5cf95ab1992.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3llYWhQZW5nMTE=,size_16,color_FFFFFF,t_70#pic_center)

之后使用：

```sql
--重置密码
update mysql. user set AUTHENTICATION_STRING = PASSWORD ('abc 123') where user='root';
--刷新
FLUSH PRIVILEGES;
```

### 7. 禁止远程访问 root 账户

### 方法一

禁止远程访问 `root` 账户

```sql
DROP USER 'root'@'%';
```

刷新权限

```sql
FLUSH PRIVILEGES;
```


### 方法二

1. **登录 MariaDB**：首先，以 `root` 用户身份登录到 MariaDB 服务器
2. **撤销远程访问权限**：执行`REVOKE`命令来撤销`root`用户从远程主机（除本地外的所有主机，即`%`）的访问权限。
	```sql
	REVOKE ALL PRIVILEGES, GRANT OPTION FROM 'root'@'%';
	```
	这条命令会移除所有远程`root`用户的权限，但不影响本地（`localhost`）的`root`用户权限。
3. **刷新权限**：撤销权限后，需要刷新权限，使更改立即生效。
	```sql
	FLUSH PRIVILEGES;
	```
4. **检查权限**：可以再次执行查询来确认更改是否成功：
	```sql
	SELECT User, Host FROM mysql.user WHERE User = 'root';
	```
	确认`Host`列为`%`的条目已不再存在，或者该条目的权限已被正确地限制。

完成上述步骤后，`root`用户将无法再从远程主机进行访问，仅限于本地访问（`localhost`）。这是提高数据库安全性的常见做法，因为在多数情况下，直接远程访问`root`账户应尽量避免，以减少潜在的安全风险。

**注意**：在进行此类权限变更时，务必确保你有其他方式可以访问数据库以进行管理，比如通过SSH隧道或者有其他具有必要权限的用户账户。

## 参考

> [!link]- Reference
> [MySQL基本语句手册](https://blog.csdn.net/yeahPeng11/article/details/119486404)
> [SQL语法基础知识总结 | JavaGuide](https://javaguide.cn/database/sql/sql-syntax-summary.html)
