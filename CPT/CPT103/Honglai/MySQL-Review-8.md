继续把索引一讲。。。

# 索引

在MySQL中，索引是一种用于提高数据库查询性能的数据结构。它类似于书籍的目录，可以帮助数据库引擎快速定位和检索数据。索引的主要作用是加速数据的检索过程，减少数据库的查询时间。在MySQL中，常见的索引类型包括主键索引、唯一索引、普通索引、全文索引等。

## 优缺点

### 优点

1. **提高查询性能：** 索引能够显著提高SELECT查询的速度，因为它们允许数据库引擎更快地定位和访问所需的数据行。通过减少需要扫描的数据量，索引可以大大减小查询的执行时间。
2. **加速排序和分组操作：** 如果查询包含ORDER BY或GROUP BY子句，索引可以加速这些排序和分组操作，因为索引存储了按照特定列排列的数据。
3. **加速连接操作：** 当在多个表之间进行连接操作时，索引可以加速连接的执行，特别是在连接的列上创建索引。
4. **加速唯一性检查：** 唯一索引确保列中的值是唯一的，这可以加速唯一性检查的过程，例如在插入新记录时。
5. **加速全文搜索：** 如果表中包含文本数据，并使用全文索引，可以通过索引来加速全文搜索的操作。

### 缺点

- **写操作的性能可能降低：** 对表进行INSERT、UPDATE和DELETE等写操作时，索引也需要更新。因此，频繁的写操作可能导致性能下降。
- **占用额外存储空间：** 索引数据结构会占用磁盘空间，特别是在大型表上。这需要在性能提升和存储开销之间进行权衡。
- **过多的索引可能导致性能下降：** 如果表上存在过多的索引，可能会导致查询优化器选择不恰当的索引，或者在更新操作时需要维护大量的索引，从而降低性能。

## 作用

因为MySQL这种关系型数据库不同于Redis这种利用内存来高速缓存的非关系型数据库，MySQL的数据是存储在硬盘或者说磁盘上的，查询数据的时候，如果没有索引，那么就会一次性将所有的数据加载到内存当中，然后再进行查询筛选，但是磁盘的IO是非常耗时间的，如果数据量大的话，这个用时就更长了。

如果有了索引之后，就不需要加载所有的数据了，因为MySQL底层采用的B+树进行维护，而且一般的层数在2-4层左右，所以不管多大的数据量，最多也就只需2-4次的磁盘IO。

简而言之，加速。

## 情况

有些时候其实不需要建立索引，而有些时候又必须建立索引。

### 需要建立索引的情况

1. **频繁的查询操作：** 如果某个列经常用于WHERE子句、JOIN条件或ORDER BY子句，建立索引可以显著提高查询性能。
2. **唯一性约束：** 如果某列需要保持唯一性，例如主键列或唯一键列，建议为该列创建唯一索引。
3. **连接操作：** 当在多个表之间进行连接操作时，对连接列创建索引可以加速连接的执行。
4. **排序和分组操作：** 如果查询包含ORDER BY或GROUP BY子句，通过为相关列创建索引可以提高排序和分组操作的性能。
5. **全文搜索：** 如果需要执行全文搜索，可以使用全文索引加速搜索操作。

### 不需要建立索引的情况

1. **小表：** 对于小型表，全表扫描可能比使用索引更为高效，因为索引引入的开销可能超过了全表扫描的成本。
2. **高写入负载：** 如果表经常执行INSERT、UPDATE和DELETE等写操作，索引的维护成本可能会超过读操作的性能提升，因此需要权衡。
3. **频繁更新的列：** 如果某列经常被更新，特别是对于大型表，建立索引可能会降低性能，因为每次更新都需要更新索引。
4. **不会用于查询的列：** 对于不会出现在查询条件中的列，创建索引是没有意义的。
5. **短字符串列：** 对于短字符串列，全表扫描的代价可能较小，而索引的额外开销可能不值得。



## MySQL索引的数据结构

1. **B+Tree索引：** B+Tree是B-Tree的一种变体，常用于实现聚簇索引（在`InnoDB`存储引擎中，默认的主键索引就是聚簇索引）。与B-Tree相比，B+Tree将数据仅存储在叶子节点上，非叶子节点只包含键值，这有助于提高范围查询和排序操作的性能。
2. **哈希索引：** 哈希索引使用哈希表数据结构，适用于等值查询。哈希索引在查询速度上非常快，但不适用于范围查询和排序操作。MySQL中的`MyISAM`存储引擎支持哈希索引，但`InnoDB`等存储引擎通常不使用哈希索引作为主索引。

### B+ Tree

**1. 树的结构：** 想象一棵树，它有根部（最上面的节点）和一些分支。这个树是平衡的，就是说每个分支的高度都是一样的。

**2. 数据的存储：** 假设我们要在这棵树上存储一些数据，比如数字。这些数字将被放在叶子（最底部的节点）上。

**3. 排序和查找：** 想象一下，如果我们要找一个数字，我们可以从根部开始，按照一定的规则（比如说，如果要找的数字比当前节点的数字大，就往右走；反之，就往左走），最终找到我们需要的数字。

**4. B+Tree的特点：** B+Tree索引就像这样的一棵树，但有一些特殊之处：

- **只有叶子节点存储数据：** 所有的数据都存储在叶子节点上，而非叶子节点只存储索引。
- **叶子节点是有序的链表：** 叶子节点按照顺序形成一个链表，方便范围查找和排序操作。
- **平衡性：** 树的所有分支的高度是一样的，这确保了查找效率。

**5. 为什么使用B+Tree：** 使用B+Tree索引有几个好处：

- **快速查找：** 通过树的结构，我们可以快速找到需要的数据，不需要遍历整个表。
- **适用于范围查找：** 由于叶子节点是有序的链表，B+Tree适合进行范围查找，比如查找某个范围内的数据。
- **适用于排序：** B+Tree的有序性使得对数据进行排序非常高效。

![](https://cdn.jsdelivr.net/gh/GeneralK1ng/My_Blog_IMG@main/img/B+TreeExample.png)

### 哈希索引

**1. 键和箱子：** 假设你有很多钥匙（键），而你想快速找到它们存放在哪里。哈希索引就好像有一堆箱子，每个箱子上都有一个标签（哈希值）。

**2. 哈希函数：** 为了找到正确的箱子，你可以使用一个神奇的函数（哈希函数），这个函数可以把每个钥匙转换成一个特殊的数字（哈希值）。这个数字告诉你应该把钥匙放到哪个箱子里。

**3. 查找过程：** 当你想找到一个特定的钥匙时，你只需要把这个钥匙交给哈希函数，得到一个哈希值，然后直接去找标签上是这个哈希值的箱子。这样就很快找到了你要的东西。

### 区别

- 哈希索引**不支持排序**，因为哈希表是无序的。
- 哈希索引**不支持范围查找**。
- 哈希索引**不支持模糊查询**及多列索引的最左前缀匹配。
- 因为哈希表中会**存在哈希冲突**，所以哈希索引的性能是不稳定的，而B+树索引的性能是相对稳定的，每次查询都是从根节点到叶子节点。

## 分类

### 主键索引（Primary Key Index）

- 主键索引是一种唯一性索引，用于唯一标识表中的每一行数据。
- 每个表只能有一个主键索引。
- 主键索引自动创建，如果表定义中没有指定主键，则系统会选择一个唯一的列作为主键。

```sql
CREATE TABLE example (
    id INT PRIMARY KEY,
    name VARCHAR(50)
);
```

### 唯一索引（Unique Index）

- 唯一索引确保索引列中的所有值都是唯一的，但允许有空值。
- 表可以有多个唯一索引。

例子就不写了，跟上面差不多，相当于你在创建主键和唯一键的时候，底层就已经创建了索引，为了加快检索速度。

### 普通索引（Normal Index）

- 普通索引是最基本的索引类型，没有唯一性或空值的限制。
- 可以对表中的一个或多个列创建普通索引。

```sql
CREATE TABLE example (
    id INT,
    name VARCHAR(50),
    INDEX idx_name (name)
);
```

如果已经有创建好的表，你可以使用`CREATE INDEX`语句来添加索引。以下是一般的语法格式：

```sql
CREATE [UNIQUE] INDEX index_name
ON table_name (column1, column2, ...);
```

- `index_name`：索引的名称，应该是唯一的，用于标识这个索引。
- `table_name`：要添加索引的表名。
- `column1, column2, ...`：要包含在索引中的列名。

例如，如果你有一个名为 `employees` 的表，包含列 `employee_id` 和 `last_name`，你可以创建一个索引来加速按 `last_name` 进行查询的操作：

```sql
CREATE INDEX idx_last_name
ON employees (last_name);
```

如果你希望创建一个唯一索引，确保 `last_name` 的值是唯一的，可以使用以下语法：

```sql
CREATE UNIQUE INDEX idx_last_name_unique
ON employees (last_name);
```

需要注意的是，虽然可以在已经存在的表上添加索引，但在生产环境中，最好在设计表的时候就考虑好哪些字段需要索引，以及使用何种类型的索引。添加索引可能会导致表的锁定和大量的I/O操作，因此需要谨慎使用，特别是在大型表上。

如果你的表很大，想要添加索引的话。。。那可能需要的时间就会非常的长。

### 全文索引（Full-Text Index）

- 全文索引用于在文本数据上执行全文搜索。
- 主要用于对大文本字段（如TEXT和VARCHAR）进行高效的搜索操作。

```sql
CREATE TABLE articles (
    id INT,
    title VARCHAR(100),
    content TEXT,
    FULLTEXT (title, content)
);
```

# 集合操作

对集合（Set）的操作，它包括并集（UNION）、交集（INTERSECT）、差集（EXCEPT）等。然而，MySQL并没有提供标准的INTERSECT和EXCEPT操作符，但可以通过其他方式来实现相似的效果。以下是一些关于MySQL中SET操作的基本解释：

**UNION（并集）：**

- `UNION`操作用于合并两个查询的结果集，返回不重复的行。语法如下：

```sql
SELECT column1, column2, ...
FROM table1
UNION
SELECT column1, column2, ...
FROM table2;
```

这将返回两个查询的结果的并集。

**INTERSECT（交集）：**

- MySQL并没有内建的INTERSECT操作符。但可以通过使用INNER JOIN或IN子查询等方式来模拟交集的效果。例如：

```sql
SELECT column1, column2, ...
FROM table1
WHERE column1 IN (SELECT column1 FROM table2);
```

这将返回两个表的交集。

**EXCEPT（差集）：**

- 同样，MySQL没有内建的EXCEPT操作符。可以使用LEFT JOIN或NOT IN子查询等方法来模拟差集。例如：

```sql
SELECT column1, column2, ...
FROM table1
WHERE column1 NOT IN (SELECT column1 FROM table2);
```

这将返回在table1中但不在table2中的行。

**其他SET操作：**

- MySQL还支持一些其他的集合操作，如`UNION ALL`（返回所有行，包括重复的行）、`INTERSECT ALL`、`EXCEPT ALL`等。这些操作在语法上与对应的不带ALL的操作类似，但会保留所有行，包括重复的。

# 空值的处理

## 条件中的空值

在SQL的SELECT语句中，WHERE子句使用三值逻辑（three-valued logic）。三值逻辑是一种逻辑系统，其中逻辑语句的真（True）、假（False）以及第三个可能的值是未知（Unknown）。

在上下文中，当使用WHERE子句时，该子句中的条件会产生三种可能的结果：

1. **True（真）：** 满足条件，符合要求的行将被返回。
2. **False（假）：** 不满足条件，不符合要求的行将被排除。
3. **Unknown（未知）：** 条件对于某些行是未知的或不适用。这种情况下，行也会被排除，因为WHERE子句要求返回的是条件为True的行。

这种三值逻辑在处理包含NULL值的情况时尤为重要。在SQL中，与NULL值的比较通常返回未知，因此，当涉及到NULL值的条件时，WHERE子句可能会产生未知的结果。例如，如果你有一个条件是 `column1 = 10`，而`column1`中有NULL值，那么由于NULL的比较结果是未知，这个条件就会被视为未知，相应的行将不会包括在结果中。

举个例子来说：

让我来建立一个这个`student`表格

```sql
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    name VARCHAR(50),
    age INT,
    grade INT
);

INSERT INTO students (student_id, name, age, grade) VALUES
(1, 'Alice', 20, 85),
(2, 'Bob', 22, 92),
(3, 'Charlie', NULL, 75),
(4, 'David', 21, NULL),
(5, 'Eva', NULL, NULL);
```

现在，我们将使用一个带有条件的SELECT语句，以演示三值逻辑的概念：

```sql
-- 返回年龄大于等于21岁的学生
SELECT * FROM students WHERE age >= 21;
```

在这个例子中，我们希望选择年龄大于等于21岁的学生。这里有几个情况：

1. Alice（20岁） - 不符合条件，排除。
2. Bob（22岁） - 符合条件，包括在结果中。
3. Charlie（NULL岁） - 由于NULL的比较结果是未知，所以他的行也被排除。
4. David（21岁） - 符合条件，包括在结果中。
5. Eva（NULL岁） - 由于NULL的比较结果是未知，所以她的行也被排除。

那么如果我的查询语句加上了这个条件会怎么样？

```sql
-- 返回年龄大于等于21岁或者年龄是NULL的学生
SELECT * FROM students WHERE age >= 21 OR age IS NULL;
```

这样就会考虑空值，然后就会返回符合条件的行。

## 算术中的空值

当对包含NULL的列进行算术运算时，结果将为NULL。在SQL中，任何包含NULL值的算术运算都会导致结果为NULL，这反映了NULL的未知或缺失值的性质。

举个例子，想象一下表格：

```sql
CREATE TABLE numbers (
    num1 INT,
    num2 INT
);

INSERT INTO numbers (num1, num2) VALUES
(10, 5),
(NULL, 8),
(15, NULL),
(NULL, NULL);
```

现在，如果我们尝试进行一些算术运算：

```sql
-- 尝试加法运算
SELECT num1 + num2 AS result FROM numbers;
```

在这个查询中，我们尝试对表格中的两列进行加法运算。然而，由于表格中包含NULL值，结果列将包含NULL值：



| result |
| :----: |
|   15   |
| <null> |
| <null> |
| <null> |

这是因为在任何涉及到NULL的算术运算中，结果都是NULL。这种行为是为了确保对未知或缺失值的任何运算都不会导致不确定的结果。

如果你希望在执行算术运算时考虑NULL值，并希望将NULL值视为0，可以使用`COALESCE`函数或`IFNULL`函数：

```sql
-- 使用COALESCE函数将NULL视为0
SELECT COALESCE(num1, 0) + COALESCE(num2, 0) AS result FROM numbers;
```

这样就会忽略`NULL`将它视为0进行计算：



| result |
| :----: |
|   15   |
|   8    |
|   15   |
|   0    |

## 聚合中的空值

**COUNT函数：**

- `COUNT`函数用于计算行数，但是它会忽略包含NULL值的列，除非指定了列名或使用`COUNT(*)`。
- 如果要计算包括NULL在内的行数，可以使用`COUNT(*)`。

**SUM、AVG和其他数学函数：**

- 在执行数学函数如`SUM`和`AVG`时，它们会忽略包含NULL值的行。
- 如果希望将NULL值视为0，可以使用`COALESCE`或`IFNULL`函数进行处理。

```sql
-- 计算总和，将NULL视为0
SELECT SUM(COALESCE(column1, 0)) FROM table_name;
```

**MAX和MIN函数：**

- `MAX`和`MIN`函数会忽略包含NULL值的行，除非使用`MAX(column_name)`或`MIN(column_name)`指定了列名。

```sql
-- 获取包含NULL的列的最大值
SELECT MAX(column1) FROM table_name;
```

- 使用`MAX(column_name)`或`MIN(column_name)`可以考虑包含NULL的行。

**GROUP BY子句：**

- 在使用`GROUP BY`子句进行分组时，NULL值会被分为一组。

```sql
-- 按列分组，NULL被分为一组
SELECT column1, COUNT(*) FROM table_name GROUP BY column1;
```

- 如果要在`GROUP BY`中将NULL值视为一个特定的值，可以使用`COALESCE`或`IFNULL`函数进行处理。

```sql
-- 使用COALESCE将NULL值视为'Unknown'，然后按列分组
SELECT COALESCE(column1, 'Unknown') AS grouped_column, COUNT(*) FROM table_name GROUP BY grouped_column;
```

**ORDER BY子句：**

**默认排序规则：**

- 默认情况下，对于升序排序（`ORDER BY column1 ASC`），NULL值会被认为是最小值，因此会出现在排序的最前面。
- 对于降序排序（`ORDER BY column1 DESC`），NULL值会被认为是最大值，因此会出现在排序的最后面。

```sql
-- 默认升序排序，NULL在最前面
SELECT * FROM table_name ORDER BY column1 ASC;

-- 默认降序排序，NULL在最后面
SELECT * FROM table_name ORDER BY column1 DESC;
```

**使用COALESCE或IFNULL进行定制排序：**

- 你还可以使用`COALESCE`或`IFNULL`函数在`ORDER BY`子句中进行定制排序，将NULL视为特定的值。

```sql
-- 使用COALESCE将NULL视为'Unknown'，然后按列升序排序
SELECT * FROM table_name ORDER BY COALESCE(column1, 'Unknown') ASC;

-- 使用IFNULL将NULL视为0，然后按列降序排序
SELECT * FROM table_name ORDER BY IFNULL(column1, 0) DESC;
```

## 总结

通过一个例子来进行一个简单的总结：

```sql
-- 建表语句
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    age INT,
    salary DECIMAL(10, 2),
    department_id INT,
    hire_date DATE
);

-- 插入数据
INSERT INTO employees (employee_id, first_name, last_name, age, salary, department_id, hire_date)
VALUES
(1, 'John', 'Doe', 30, 50000.00, 1, '2020-01-15'),
(2, 'Jane', 'Smith', 28, 60000.00, NULL, '2019-05-20'),
(3, 'Bob', 'Johnson', 35, NULL, 2, '2021-03-10'),
(4, 'Alice', 'Williams', NULL, 55000.00, 1, '2022-02-05'),
(5, 'Charlie', 'Brown', 32, 70000.00, NULL, NULL);
```

然后这张表的全貌就是这样子：

| id   | first_name | last_name | age    | salary   | department_id | hire_date  |
| ---- | ---------- | --------- | ------ | -------- | ------------- | ---------- |
| 1    | John       | Doe       | 30     | 50000.00 | 1             | 2020-01-15 |
| 2    | Jane       | Smith     | 28     | 60000.00 | <null>        | 2019-05-20 |
| 3    | Bob        | Johnson   | 35     | <null>   | 2             | 2021-03-10 |
| 4    | Alice      | Williams  | <null> | 55000.00 | 1             | 2022-02-05 |
| 5    | Charlie    | Brown     | 32     | 70000.00 | <null>        | <null>     |

现在我们来分别执行一下SQL语句

### 比较运算符：

```sql
-- 选择年龄大于等于30岁或者年龄是NULL的员工
SELECT * FROM employees WHERE age >= 30 OR age IS NULL;
```

执行结果如下

| id   | first_name | last_name | age    | salary   | department_id | hire_date  |
| ---- | ---------- | --------- | ------ | -------- | ------------- | ---------- |
| 1    | John       | Doe       | 30     | 50000.00 | 1             | 2020-01-15 |
| 3    | Bob        | Johnson   | 35     | <null>   | 2             | 2021-03-10 |
| 4    | Alice      | Williams  | <null> | 55000.00 | 1             | 2022-02-05 |
| 5    | Charlie    | Brown     | 32     | 70000.00 | <null>        | <null>     |

可以看到筛选出来的结果是包含年龄为NULL的，如果我们删掉后面这个条件：

```sql
SELECT * FROM employees WHERE age >= 30;
```

结果就变成了：

| id   | first_name | last_name | age  | salary   | department_id | hire_date  |
| ---- | ---------- | --------- | ---- | -------- | ------------- | ---------- |
| 1    | John       | Doe       | 30   | 50000.00 | 1             | 2020-01-15 |
| 3    | Bob        | Johnson   | 35   | <null>   | 2             | 2021-03-10 |
| 5    | Charlie    | Brown     | 32   | 70000.00 | <null>        | <null>     |

可以看到空值在条件判断中默认是忽略的，原因看上面。

### 聚合函数：

```sql
-- 计算平均工资，将NULL值视为0
SELECT AVG(COALESCE(salary, 0)) AS avg_salary FROM employees;
```

执行结果如下：

| avg_salary   |
| ------------ |
| 47000.000000 |

可以看到我们将空值视为了0，如果我们直接计算该列的平均值呢？

```sql
-- 计算平均工资，不将NULL值视为0
SELECT AVG(salary) AS avg_salary FROM employees;
```

可以看到执行结果就变成了：

| avg_salary   |
| ------------ |
| 58750.000000 |

因为他把工资列为NULL的行都忽略了，并没有纳入平均值的计算，所以导致平均值计算变得不准确，所以实际开发中还是要根据实际情况进行调用。

### 排序：

```sql
-- 按入职日期降序排序，将NULL值放在最后
SELECT * FROM employees ORDER BY hire_date IS NULL, hire_date DESC;
```

然后就能看到结果为：

| id   | first_name | last_name | age    | salary   | department_id | hire_date  |
| ---- | ---------- | --------- | ------ | -------- | ------------- | ---------- |
| 4    | Alice      | Williams  | <null> | 55000.00 | 1             | 2022-02-05 |
| 3    | Bob        | Johnson   | 35     | <null>   | 2             | 2021-03-10 |
| 1    | John       | Doe       | 30     | 50000.00 | 1             | 2020-01-15 |
| 2    | Jane       | Smith     | 28     | 60000.00 | <mull>        | 2019-05-20 |
| 5    | Charlie    | Brown     | 32     | 70000.00 | <null>        | <null>     |

### 条件中的处理：

```sql
-- 选择属于部门2或者部门是NULL的员工
SELECT * FROM employees WHERE department_id = 2 OR department_id IS NULL;
```

返回结果为：

| id   | first_name | last_name | age  | salary   | department_id | hire_date  |
| ---- | ---------- | --------- | ---- | -------- | ------------- | ---------- |
| 2    | Jane       | Smith     | 28   | 60000.00 | <null>        | 2019-05-20 |
| 3    | Bob        | Johnson   | 35   | <null>   | 2             | 2021-03-10 |
| 5    | Charlie    | Brown     | 32   | 70000.00 | <null>        | <null>     |



