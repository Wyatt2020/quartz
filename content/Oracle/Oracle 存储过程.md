---
created: 2020-07-28 23:50
modified: 2024-07-21 23:53
tags: [Oracle]
---

### 编写规范

PL/SQL 块是 PL/SQL 的基本程序单元，PL/SQL 块右三个部分构成：定义部分、执行部分、例外处理部分

如下所示：

```sql
declare
/* 定义常量、变量、游标、例外、复杂数据结构 */
begin
/* 要执行的PL/SQL语句和sql语句 */
exception
/* 处理运行时的错误 */
end;
```

### 调用存储过程

`exec` \\ `call `

```sql
call procedure_name('张三', 18);
```

简单栗子

```sql
CREATE OR REPLACE PROCEDURE "demo_01"
AS
BEGIN
	dbms_output.put_line('hello ');
END;
```

### in 指定输入参数，out 指定输出参数

**带参数的存储过程**

```sql
CREATE OR REPLACE PROCEDURE "demo_01" (val IN VARCHAR, val2 out VARCHAR)
AS
BEGIN
	dbms_output.put_line('hello '||val);
END;
```

**不带参数的存储过程**

`||` 相当于字符串连接符

```sql
CREATE OR REPLACE PROCEDURE "demo_01"
AS
	--定义变量
	id number(10);
	name varchar(20);
    --:=是给变量赋值
BEGIN
	--给变量赋值
	select stu_id, name into id, name from student where stu_id=20200731;
	--打印变量值
	dbms_output.put_line('id:'||id||' name:'||name);
END;
```

### 语法

```sql
create [or replace] procedure 存储过程名(param1 in type，param2 out type)
    as
    变量1 类型（值范围）;
    变量2 类型（值范围）;
begin
    select count(*) into 变量1 from 表A where列名=param1；
    if (判断条件) then
        select 列名 into 变量2 from 表A where列名=param1；
        dbms_output.Put_line('打印信息');
   elsif (判断条件) then
       dbms_output.Put_line('打印信息');
   else
       raise 异常名（NO_DATA_FOUND）;
   end if;
exception
   when others then
       rollback;
end;
```

### 预定义异常

```sql
EXCEPTION
	when no_data_found then dbms_output.put_line('没有数据');
```

### 判断

```sql
CREATE OR REPLACE PROCEDURE "demo_01"
AS
BEGIN
	insert into student(stu_id,name,birthday) values(student_stu_id_seq.nextval,'宝可梦',TO_DATE('1996-7-28', 'YYYY-MM-DD'));
	if SQL%FOUND then
		dbms_output.put_line('新增成功');
	else
		dbms_output.put_line('新增失败');
	end if;
	--delete from student where name='宝可梦';
END;
```
