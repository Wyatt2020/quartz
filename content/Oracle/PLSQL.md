---
created: 2020-10-25 23:41
modified: 2024-07-21 23:48
tags: [Oracle]
---

### 什么是 PL/SQL

- PL/SQL 是对 SQL 语言的扩展，使得 SQL 语言具有过程化编程的特性。

- PL/SQL 比一般的过程化编程语言，更加灵活高效。

- PL/SQL 主要用来编写存储过程和存储函数等。

### 声明方法

```sql
---赋值操作可以使用:= 也可以使用into查询语句赋值
declare
    i number(2) := 10;
    s varchar2(10) := '小明';
    ena emp.ename%type;---引用型变量，ena存储一个值
    emprow emp%rowtype;---记录型变量，emprow存储一行记录
begin
    dbms_output.put_line(i);
    dbms_output.put_line(s);
    select ename into ena from emp where empno = 7788;
    dbms_output.put_line(ena);
    select * into emprow from emp where empno = 7788;
    dbms_output.put_line(emprow.ename || '的工作为：' || emprow.job);
end;
```

### 条件

if-end if

```sql
---输入小于18的数字，输出未成年
---输入大于18小于40的数字，输出中年人
---输入大于40的数字，输出老年人
--需要输入ii参数
declare
  i number(3) := &ii;
begin
  if i<18 then
    dbms_output.put_line('未成年');
  elsif i<40 then
    dbms_output.put_line('中年人');
  else
    dbms_output.put_line('老年人');
  end if;
end;
```

### 循环

loop-end loop

**while 循环**

```sql
--输出1-10
declare
  i number(2) := 1;
begin
  while i<11 loop
     dbms_output.put_line(i);
     i := i+1;
  end loop;  
end;
```

**exit 循环**

```sql
--输出1-10
declare
  i number(2) := 1;
begin
  loop
    exit when i>10;
    dbms_output.put_line(i);
    i := i+1;
  end loop;
end;
```

**for 循环**

```sql
--输出1-10
declare

begin
  for i in 1..10 loop
     dbms_output.put_line(i);  
  end loop;
end;
```

> [!tip]
> 推荐用 exit 循环

### 游标

`cursor`：可以存放多个对象，多行记录；使用时，需要 **open-close**

```sql
--输出emp表中所有员工的姓名
declare
  cursor c1 is select * from emp;
  --临时变量
  emprow emp%rowtype;
begin
  open c1;
     loop
         --fetch..into：遍历c1，给emprow赋值
         fetch c1 into emprow;
         --退出条件（遍历结束）
         exit when c1%notfound;
         dbms_output.put_line(emprow.ename);
     end loop;
  close c1;
end;
```

```sql
--给指定部门员工涨工资
declare
  --c2带参数，需要输入
  cursor c2(eno emp.deptno%type) 
  is select empno from emp where deptno = eno;
  en emp.empno%type;
begin
  --10是参数，eno=10
  open c2(10);
     loop
        fetch c2 into en;
        exit when c2%notfound;
        update emp set sal=sal+100 where empno=en;
        commit;
     end loop;  
  close c2;
end;
```

### 存储过程

存储过程就是提前已经编译好的一段 PL/SQL 语言，放置在数据库端，可以直接被调用，一般都是固定步骤的业务。

#### in 类型参数如何使用 (可以省略)

创建简单的存储过程

```sql
--给指定员工涨100块钱
create or replace procedure p1(eno emp.empno%type)
is
begin
   update emp set sal=sal+100 where empno = eno;
   commit;
end;
```

测试存储过程

```sql
declare
begin
  p1(7788);
end;
```

#### out 类型参数如何使用

```sql
--使用存储过程来算年薪
create or replace procedure p_yearsal(eno emp.empno%type, yearsal out number)
is
   s number(10);
   c emp.comm%type;
begin
   select sal*12, nvl(comm, 0) into s, c from emp where empno = eno;
   yearsal := s+c;
end;
```

测试

```sql
declare
  --接收out参数
  yearsal number(10);
begin
  p_yearsal(7788, yearsal);
  dbms_output.put_line(yearsal);
end;
```

#### in 和 out 类型参数的区别

凡是涉及到 into 查询语句赋值或者 `:=` 赋值操作的参数，都必须使用 out 来修饰。

### 存储函数

- 存储过程和存储函数的参数都不能带长度

- 存储函数的返回值类型不能带长度

创建存储函数

```sql
create or replace function f_yearsal(eno emp.empno%type) return number
is
  s number(10);     
begin
  select sal*12+nvl(comm, 0) into s from emp where empno = eno;
  return s;
end;
```

测试

```sql
--存储函数在调用的时候，返回值需要接收。
declare
  s number(10); 
begin
  s := f_yearsal(7788);
  dbms_output.put_line(s);
end;
```

### 存储过程和存储函数的区别

**语法区别**：

关键字不一样，`procedure` (存储过程)，`function`(存储函数)。

存储函数比存储过程多了两个 `return`。

**本质区别**：

存储函数有返回值，而存储过程没有返回值。

如果存储过程想实现有返回值的业务，我们就必须使用 out 类型的参数。

即便是存储过程使用了 out 类型的参数，其本质也不是真的有了返回值，

而是在存储过程内部给 out 类型参数赋值，在执行完毕后，我们直接拿到输出类型参数的值。

我们可以使用存储函数有返回值的特性，来自定义函数；而存储过程不能用来自定义函数。

举个栗子：

使用存储函数来实现提供一个部门编号，输出一个部门名称。

```sql
create or replace function fdna(dno dept.deptno%type) return dept.dname%type
is
  dna dept.dname%type;
begin
  select dname into dna from dept where deptno = dno;
  return dna;
end;
```

使用 fdna 存储函数，查询出员工姓名，员工所在部门名称。

```sql
select e.ename, fdna(e.deptno)
from emp e;
```
