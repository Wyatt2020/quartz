---
created: 2024-03-29 10:44
modified: 2024-03-29 17:11
tags: [MySQL]
---

> [!note] 总结
> 1. INNER JOIN（内连接）: 左表 ∩ 右表
> 2. LEFT JOIN（左连接）：左表 + (左表 ∩ 右表)
> 3. RIGHT JOIN（右连接）：右表 + (左表 ∩ 右表)
> 4. FULL JOIN（全外连接）：左表 ∪ 右表
> 5. CROSS JOIN（交叉连接）：左表 X 右表

### 1. INNER JOIN（内连接）

INNER JOIN 是最常用的 JOIN 类型，它会返回两个表中满足连接条件的行。如果两个表之间没有匹配的行，则不会返回任何结果。

```sql
SELECT * FROM table1 INNER JOIN table2 ON table1.column = table2.column;
```

### 2. LEFT JOIN（左连接）

LEFT JOIN 会返回左表中的所有行，以及右表中满足连接条件的行。如果右表中没有匹配的行，则会返回 NULL 值。

```sql
SELECT * FROM table1 LEFT JOIN table2 ON table1.column = table2.column;
```

### 3. RIGHT JOIN（右连接）：

RIGHT JOIN 与 LEFT JOIN 类似，但它会返回右表中的所有行，以及左表中满足连接条件的行。如果左表中没有匹配的行，则会返回 NULL 值。

```sql
SELECT * FROM table1 RIGHT JOIN table2 ON table1.column = table2.column;
```

### 4. CROSS JOIN（交叉连接）：

不带连接条件时，CROSS JOIN 会返回两个表的笛卡尔积，即左表的每一行与右表的每一行进行组合。

```sql
SELECT * FROM table1 CROSS JOIN table2;
```

### 5. FULL JOIN（全外连接）：

FULL JOIN 会返回两个表中的所有行，不论是否有匹配的行。如果某个表中没有匹配的行，则会返回 NULL 值。

```sql
SELECT * FROM table1 FULL JOIN table2 ON table1.column = table2.column;
```

> [!attention]

#### MySQL 不直接支持 FULL OUTER JOIN

通常使用 `UNION ALL` 操作结合 `LEFT JOIN` 和 `RIGHT JOIN` 来模拟实现 `FULL JOIN` 的效果。

```sql
-- 创建示例表结构和数据
CREATE TABLE table1 (
  id INT PRIMARY KEY,
  value VARCHAR(255)
);

CREATE TABLE table2 (
  id INT PRIMARY KEY,
  value VARCHAR(255)
);

INSERT INTO table1 VALUES (1, 'A'), (2, 'B');
INSERT INTO table2 VALUES (2, 'C'), (3, 'D');
```

```sql

SELECT table1.id AS id, table1.value AS t1_value, table2.value AS t2_value
FROM table1
LEFT JOIN table2 ON table1.id = table2.id

UNION ALL

SELECT table2.id AS id, table1.value AS t1_value, table2.value AS t2_value
FROM table1
RIGHT JOIN table2 ON table1.id = table2.id;
```

![[SQL 常见的 join 语句.png]]

---

如果需要去重（即 `FULL JOIN` 效果），可以使用 `UNION` 而非 `UNION ALL`

```sql
SELECT table1.id AS id, table1.value AS t1_value, table2.value AS t2_value
FROM table1
LEFT JOIN table2 ON table1.id = table2.id

UNION

SELECT table2.id AS id, table1.value AS t1_value, table2.value AS t2_value
FROM table1
RIGHT JOIN table2 ON table1.id = table2.id;
```

![[SQL 常见的 join 语句-1.png]]
