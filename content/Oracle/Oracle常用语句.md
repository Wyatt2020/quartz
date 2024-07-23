---
created: 2021-07-13 23:08
modified: 2024-07-21 23:12
tags: [Oracle]
---

### 管理员登录

密码为安装时设置的口令

```javascript
sqlplus
sys as sysdba
```

![[Pasted image 20240721231110.png]]

[如果忘记密码，点击一下](https://blog.csdn.net/qq_42192693/article/details/87924264)

### 创建表空间

```sql
create tablespace myspace
datafile 'c:\myspace.dbf'
size 100m
autoextend on
next 10m;
```

说明：

1. 给表空间命名

2. 存放数据文件的路径

3. dbf 的初始大小 100m

4. 自动扩容开关

5. 自增大小 10m

### 删除表空间

```sql
drop tablespace myspace
```

### 创建 'tom' 用户，密码为 '1234'

```javascript
create user tom identified by 1234;
```

default tablespace myspace

### 修改用户密码

```javascript
alter user tom identified by 123456;
```

给 'tom' 授权

常用的角色有：

- *CONNECT*：连接服务器，用于临时用户

- *RESOURCE*：创建和使用资源（表，索引，视图，存储过程等）

- *DBA*：拥有所有系统权限，包括创建用户和授权等

```javascript
grant connect,resource to tom;
```

### 用户登录

```javascript
connect tom@orcl;
```

> [!tip] 
> - `connect` 可以缩写成 `conn`
> - tom 用户仅存在于 *orcl* 数据库中，因此，必须在 `connect` 命令中明确指定用户名为 `tom@orcl`

### scott 用户

Oracle 内置的用户，初学者可用来练习

```sql
--解锁scott用户 锁定：lock
alter user scott account unlock;
--解锁scott用户的密码，默认是tiger(此句可用于修改密码)
alter user scott identified by tiger;
```

### 创建表

```sql
create table student(
  stu_id number(10) primary key, --学号
  name varchar2(20), --姓名
  gender char(1), --性别，1：男，0：女
  birthday date, --出生日期
  age number(2), --年龄
  scholarship number(7,2), --奖学金
  class_id number(4) --班级编号
);
```

### 增加主键

```sql
alter table 表名 add constraint 主键名 primary key (字段名1);
```

### 增加外键

```sql
alter table 表名 add constraint 外键名 foreign key (字段名1) references 关联表 (字段名2);
```

### 查看 TOM 用户的所有表

用户名必须是大写

```sql
select table_name from all_tables where owner='TOM'；
```

### 查看当前登录的用户的表

```sql
select table_name from user_tables;
```

### 设置主键自增

公式：create sequence 序列名 start with 起始数 increment by 自增量

```sql
create sequence student_stu_id_seq start with 20200724 increment by 1
```

### INSERT

```sql
insert into student values(student_stu_id_seq.nextval,'小明', 1, '24-7月-2020', 18, 2000.5,2002);
```

使用自增值：`序列名.nextval`

查看自增值：`序列名.currval`

### 插入时间类型数据

#### TO_DATE()

```javascript
TO_DATE('1997-7-24 17:18:19', 'YYYY-MM-DD HH24:MI:SS')
```

#### TO_CHAR()

```javascript
TO_CHAR(sysdate, 'yyyy-mm-dd') --系统时间转字符串
```

### UPDATE

```sql
update student set gender=1 where name='小智'
```

### DELETE

```javascript
delete from student where name='小明'
```

### 三种删除表的方式

```sql
--删除表数据
delete from tb_name
--删除表
drop table tb_name
--先删除表，再创建表
--在数据量大的情况下，而且表带有索引，该操作效率高
truncate table tablespace myspace
```

### rownum

查询第一行记录

```sql
select * from people where rownum=1
```

查询第一到第八行记录

```sql
select * from people where rownum<=8
```

查询第二行记录，将 2 换成别的数字可以查第 n 行

```sql
select * from 
(select rownum as rn, t.* from people where rownum<=2) 
where rn=2
```

### NVL

NVL(Expression1, Expression2)

如果 Expression1 的计算结果为 null 值，则 NVL() 返回 Expression2。如果 Expression1 的计算结果不是 null 值，则返回 Expression1。

### 获取当前系统时间

```sql
select TO_CHAR(sysdate,'yyyy-mm-dd hh24:mi:ss') from dual
```

### 修改表结构

```sql
修改表名：alter table 表名 rename to 新名
添加字段：alter table 表名 add 字段名 字段类型
删除字段：alter table 表名 drop column 字段名
修改字段：alter table 表名 rename column 原字段名 to 新名;
修改字段类型(前提:表中无数据)：alter table 表名 modify 字段名 新类型;
```

举个栗子

```sql
--新增多个字段
alter table tb_name add (upload_status varchar(1),upload_time date);
--设置默认值
alter table tb_name modify upload_status default '0';
--添加备注
comment on column tb_name.upload_time is '上传时间';
```

