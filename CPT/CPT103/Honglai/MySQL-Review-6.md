这一节来讲关系非常复杂的JOIN，有各种JOIN，一个一个看。

# JOIN

开始前放一个关系图，来自`runoob`

![](https://www.runoob.com/wp-content/uploads/2019/01/sql-join.png)

ok，这就是我们要学的各种复杂的关系。

## `INNER JOIN`

`INNER JOIN` 是 MySQL 中用于连接两个或多个表的一种常见的连接类型。它根据两个表之间的共同列的匹配行将这些表组合在一起。`INNER JOIN` 只返回匹配条件为真的行，排除了不匹配的行。

基本的 `INNER JOIN` 语法如下：

```sql
SELECT table1.column1, table1.column2, table2.column3, ...
FROM table1
INNER JOIN table2 ON table1.common_column = table2.common_column;
```

- `table1.column1, table1.column2, ...`: 要检索的列的名称，可以是一个或多个列，通常是来自第一个表。
- `table2.column3, ...`: 同样是要检索的列的名称，通常是来自第二个表。
- `table1` 和 `table2`: 要连接的两个表的名称。
- `common_column`: 两个表之间共同的列，用于匹配行的条件。

以下是一些 `INNER JOIN` 的使用示例：

### 用法

#### 1. 连接两个表的共同列：

```sql
SELECT employees.employee_id, employees.first_name, employees.last_name, departments.department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id;
```

上述查询将返回员工的信息以及他们所属的部门名称，通过连接 `employees` 表和 `departments` 表的 `department_id` 列。

#### 2. 连接多个表：

```sql
SELECT orders.order_id, customers.customer_name, products.product_name
FROM orders
INNER JOIN customers ON orders.customer_id = customers.customer_id
INNER JOIN order_details ON orders.order_id = order_details.order_id
INNER JOIN products ON order_details.product_id = products.product_id;
```

上述查询连接了 `orders`、`customers`、`order_details` 和 `products` 四个表，返回了订单号、客户名称以及产品名称的信息。

#### 3. 连接表并添加条件：

```sql
SELECT employees.employee_id, employees.first_name, employees.last_name, departments.department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id
WHERE employees.salary > 50000;
```

上述查询在连接 `employees` 表和 `departments` 表的基础上，添加了一个条件，只返回工资超过 50000 的员工信息。

这样没有感觉，有电脑的可以直接来看这个例子。

### 实操

假设我们有两个表：`employees` 和 `departments`。`employees` 表存储员工的信息，`departments` 表存储部门的信息。两个表通过 `department_id` 列关联。下面是创建这两个表的 SQL 建表语句：

```sql
-- 创建 employees 表
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,
    salary INT
);

-- 插入一些示例数据
INSERT INTO employees VALUES (1, 'John', 'Doe', 1, 60000);
INSERT INTO employees VALUES (2, 'Jane', 'Smith', 2, 55000);
INSERT INTO employees VALUES (3, 'Bob', 'Johnson', 1, 62000);
INSERT INTO employees VALUES (4, 'Alice', 'Williams', 2, 58000);

-- 创建 departments 表
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);

-- 插入一些示例数据
INSERT INTO departments VALUES (1, 'IT');
INSERT INTO departments VALUES (2, 'HR');
```

现在，我们可以使用 `INNER JOIN` 来查询员工的信息以及他们所在部门的名称。以下是一个使用 `INNER JOIN` 的查询示例：

```sql
-- 使用 INNER JOIN 查询员工信息和部门名称
SELECT employees.employee_id, employees.first_name, employees.last_name, employees.salary, departments.department_name
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id;
```

上述查询将返回以下结果：

| employee_id | first_name | last_name | salary | department_name |
| :---------: | :--------: | :-------: | :----: | :-------------: |
|      1      |    John    |    Doe    | 60000  |       IT        |
|      2      |    Jane    |   Smith   | 55000  |       HR        |
|      3      |    Bob     |  Johnson  | 62000  |       IT        |
|      4      |   Alice    | Williams  | 58000  |       HR        |

## `LEFT JOIN`

`LEFT JOIN` 是 MySQL 中的一种连接类型，它返回左表中的所有行，并包括右表中匹配的行。如果右表中没有匹配的行，结果集中将包含 NULL 值。

基本的 `LEFT JOIN` 语法如下：

```sql
SELECT table1.column1, table1.column2, table2.column3, ...
FROM table1
LEFT JOIN table2 ON table1.common_column = table2.common_column;
```

- `table1.column1, table1.column2, ...`: 要检索的列的名称，通常是来自左表。
- `table2.column3, ...`: 同样是要检索的列的名称，通常是来自右表。
- `table1` 和 `table2`: 要连接的两个表的名称。
- `common_column`: 两个表之间共同的列，用于匹配行的条件。

以下是一些 `LEFT JOIN` 的使用示例：

### 用法

#### 1. 连接两个表的共同列：

```sql
SELECT employees.employee_id, employees.first_name, employees.last_name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.department_id;
```

上述查询将返回所有员工的信息以及他们所在部门的名称，即使员工所在的部门在 `departments` 表中没有对应的记录。

#### 2. 连接多个表：

```sql
SELECT orders.order_id, customers.customer_name, products.product_name
FROM orders
LEFT JOIN customers ON orders.customer_id = customers.customer_id
LEFT JOIN order_details ON orders.order_id = order_details.order_id
LEFT JOIN products ON order_details.product_id = products.product_id;
```

上述查询连接了 `orders`、`customers`、`order_details` 和 `products` 四个表，返回了订单号、客户名称以及产品名称的信息。如果某个订单没有关联的客户或产品，对应的字段将包含 NULL 值。

#### 3. 结合 `LEFT JOIN` 和条件：

```sql
SELECT employees.employee_id, employees.first_name, employees.last_name, departments.department_name
FROM employees
LEFT JOIN departments ON employees.department_id = departments.department_id
WHERE employees.salary > 50000;
```

上述查询在连接 `employees` 表和 `departments` 表的基础上，添加了一个条件，只返回工资超过 50000 的员工信息。

### 实操

假设我们有两个表：`students` 和 `courses`。`students` 表存储学生的信息，`courses` 表存储课程的信息。两个表通过 `student_id` 列关联。下面是创建这两个表的 SQL 建表语句：

```sql
-- 创建 students 表
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    age INT,
    department VARCHAR(50)
);

-- 插入一些示例数据
INSERT INTO students VALUES (1, 'John', 'Doe', 22, 'Computer Science');
INSERT INTO students VALUES (2, 'Jane', 'Smith', 21, 'Mathematics');
INSERT INTO students VALUES (3, 'Bob', 'Johnson', 23, 'Physics');
INSERT INTO students VALUES (4, 'Alice', 'Williams', 22, 'Chemistry');

-- 创建 courses 表
CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(50),
    student_id INT,
    FOREIGN KEY (student_id) REFERENCES students(student_id)
);

-- 插入一些示例数据
INSERT INTO courses VALUES (101, 'Introduction to Computer Science', 1);
INSERT INTO courses VALUES (102, 'Linear Algebra', 2);
```

现在，我们可以使用 `LEFT JOIN` 来查询所有学生以及他们选修的课程。以下是一个使用 `LEFT JOIN` 的查询示例：

```sql
-- 使用 LEFT JOIN 查询学生信息以及选修的课程
SELECT students.student_id, students.first_name, students.last_name, students.age, students.department, courses.course_name
FROM students
LEFT JOIN courses ON students.student_id = courses.student_id;
```

上述查询将返回以下结果：

| student_id | first_name | last_name | age  |    department    |    course_name     |
| :--------: | :--------: | :-------: | :--: | :--------------: | :----------------: |
|     1      |    John    |    Doe    |  22  | Computer Science | Introduction to CS |
|     2      |    Jane    |   Smith   |  21  |   Mathematics    |   Linear Algebra   |
|     3      |    Bob     |  Johnson  |  23  |     Physics      |       <null>       |
|     4      |   Alice    | Williams  |  22  |    Chemistry     |       <null>       |

在这个例子中，`LEFT JOIN` 将 `students` 表和 `courses` 表连接在一起，通过它们的 `student_id` 列进行匹配。查询结果包含了所有学生的信息以及他们选修的课程，即使有的学生没有选修任何课程，对应的字段将包含 NULL 值。

这里可以涉及一下`RIGHT JOIN`，其实`RIGHT JOIN`就相当于把`LEFT JOIN`反过来。

```sql
-- 使用 RIGHT JOIN 查询学生信息以及选修的课程
SELECT students.student_id, students.first_name, students.last_name, students.age, students.department, courses.course_name
FROM students
RIGHT JOIN courses ON students.student_id = courses.student_id;
```

然后就会发现就返回了这两行，因为右外连接后，只会保留右表拥有的，而如果左表没有的则会用null值填充。

| student_id | first_name |                          last_name                           | age  |    department    |    course_name     |
| :--------: | :--------: | :----------------------------------------------------------: | :--: | :--------------: | :----------------: |
|     1      |    John    |                             Doe                              |  22  | Computer Science | Introduction to CS |
|     2      |    Jane    | 3 1SELECT column1, column22FROM table13WHERE EXISTS (SELECT 1 FROM table2 WHERE condition);sql |  21  |   Mathematics    |   Linear Algebra   |

## `NATURAL JOIN`

`NATURAL JOIN` 是一种在 MySQL 中进行表连接的方法，它基于两个表之间具有相同列名的原则，自动匹配这些相同列名的列进行连接。在 `NATURAL JOIN` 中，不需要显式指定连接条件，系统会自动根据相同列名进行匹配。

基本的 `NATURAL JOIN` 语法如下：

```sql
SELECT columns
FROM table1
NATURAL JOIN table2;
```

- `columns`: 要检索的列的名称。
- `table1` 和 `table2`: 要连接的两个表的名称。

以下是一些关于 `NATURAL JOIN` 的使用示例：

### 用法

#### 1. 连接两个表：

```sql
SELECT employees.employee_id, employees.first_name, employees.last_name, employees.salary, departments.department_name
FROM employees
NATURAL JOIN departments;
```

上述查询将返回所有员工的信息以及他们所在部门的名称，通过自动匹配 `employees` 表和 `departments` 表中相同列名的列，即 `department_id`。

#### 2. 连接多个表：

```sql
SELECT orders.order_id, customers.customer_name, products.product_name
FROM orders
NATURAL JOIN customers
NATURAL JOIN order_details
NATURAL JOIN products;
```

上述查询连接了 `orders`、`customers`、`order_details` 和 `products` 四个表，返回了订单号、客户名称以及产品名称的信息，通过自动匹配相同列名的列。

#### 3. 使用 `WHERE` 子句限定条件：

```sql
SELECT employees.employee_id, employees.first_name, employees.last_name, employees.salary, departments.department_name
FROM employees
NATURAL JOIN departments
WHERE employees.salary > 50000;
```

上述查询在连接 `employees` 表和 `departments` 表的基础上，添加了一个条件，只返回工资超过 50000 的员工信息。

>`NATURAL JOIN` 简化了连接条件的书写，但也有一些潜在的问题和注意事项：
>
>- **潜在的歧义：** 如果两个表中有多个相同列名的列，`NATURAL JOIN` 可能导致歧义，因此在设计数据库时需要小心避免这种情况。
>- **性能问题：** `NATURAL JOIN` 的性能可能不如显式指定连接条件的 `INNER JOIN`，因为它需要在运行时自动匹配列。
>- **不够灵活：** 由于 `NATURAL JOIN` 自动匹配所有相同列名的列，可能会导致连接的列不受控制，降低了查询的灵活性。

### 实操

这个和INNER JOIN很像，建表语句也采用上面一样的。

我们可以使用 `NATURAL JOIN` 来查询所有员工的信息以及他们所在部门的名称，通过自动匹配 `employees` 表和 `departments` 表中相同列名的列，即 `department_id`。

```sql
-- 使用 NATURAL JOIN 查询员工信息和部门名称
SELECT employee_id, first_name, last_name, salary, department_name
FROM employees
NATURAL JOIN departments;
```

上述查询将返回以下结果：

| employee_id | first_name | last_name | salary | department_name |
| :---------: | :--------: | :-------: | :----: | :-------------: |
|      1      |    John    |    Doe    | 60000  |       IT        |
|      2      |    Jane    |   Smith   | 55000  |       HR        |
|      3      |    Bob     |  Johnson  | 62000  |       IT        |
|      4      |   Alice    | Williams  | 58000  |       HR        |

在这个例子中，`NATURAL JOIN` 自动匹配了 `employees` 表和 `departments` 表中相同列名的列（即 `department_id`），并返回了所有员工的信息以及他们所在部门的名称。

但是如果我们更改一下，如果出现了NULL值，NATURAL JOIN会发生什么？

```sql
UPDATE employees t SET t.department_id = null WHERE t.employee_id = 3
```

然后我们再执行相同的SQL语句，此时就会发现返回结果为：

| employee_id | first_name | last_name | salary | department_name |
| :---------: | :--------: | :-------: | :----: | :-------------: |
|      1      |    John    |    Doe    | 60000  |       IT        |
|      2      |    Jane    |   Smith   | 55000  |       HR        |
|      4      |   Alice    | Williams  | 58000  |       HR        |

这是因为`NATURAL JOIN` 在处理含有 NULL 值的列时可能引发一些问题，因为 NULL 值会被视为不相等。在自动匹配列名时，如果有 NULL 值，匹配的结果可能不符合预期。

所以这玩意，不太好用。

## `OUTER JOIN`

`OUTER JOIN` 是一种连接类型，它包括左外连接 (`LEFT OUTER JOIN` 或简称为 `LEFT JOIN`)、右外连接 (`RIGHT OUTER JOIN` 或简称为 `RIGHT JOIN`) 以及全外连接 (`FULL OUTER JOIN` 或简称为 `FULL JOIN`)。`OUTER JOIN` 用于连接两个表，并返回满足连接条件的行，同时保留没有匹配行的记录，用 NULL 值填充这些缺失的部分。

### 1. 左外连接 (`LEFT OUTER JOIN` 或 `LEFT JOIN`)

左外连接返回左表中的所有行，并包括右表中匹配的行。如果右表中没有匹配的行，则结果集中右表的列将包含 NULL 值。

跟上面讲的一样，这里提一下

基本的语法如下：

```sql
SELECT columns
FROM table1
LEFT JOIN table2 ON table1.column_name = table2.column_name;
```

### 2. 右外连接 (`RIGHT OUTER JOIN` 或 `RIGHT JOIN`)

右外连接返回右表中的所有行，并包括左表中匹配的行。如果左表中没有匹配的行，则结果集中左表的列将包含 NULL 值。

基本的语法如下：

```sql
SELECT columns
FROM table1
RIGHT JOIN table2 ON table1.column_name = table2.column_name;
```

### 3. 全外连接 (`FULL OUTER JOIN` 或 `FULL JOIN`)

哈哈，MySQL中不支持这个连接，但可以使用 `LEFT JOIN` 和 `UNION` 或者 `RIGHT JOIN` 和 `UNION` 的组合来模拟实现全外连接的效果。

来个例子：

```sql
-- 创建 students 表
CREATE TABLE students (
    student_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50)
);

-- 插入一些示例数据
INSERT INTO students VALUES (1, 'John', 'Doe');
INSERT INTO students VALUES (2, 'Jane', 'Smith');
INSERT INTO students VALUES (3, 'Bob', 'Johnson');

-- 创建 courses 表
CREATE TABLE courses (
    course_id INT PRIMARY KEY,
    course_name VARCHAR(50)
);

-- 插入一些示例数据
INSERT INTO courses VALUES (101, 'Math');
INSERT INTO courses VALUES (102, 'English');
INSERT INTO courses VALUES (103, 'History');
```

然后我们使用左右分别连接然后进行合集操作。

```sql
-- 左外连接
SELECT students.student_id, students.first_name, students.last_name, courses.course_name
FROM students
LEFT JOIN courses ON students.student_id = courses.course_id

UNION

-- 右外连接
SELECT students.student_id, students.first_name, students.last_name, courses.course_name
FROM students
RIGHT JOIN courses ON students.student_id = courses.course_id;
```

然后返回

| id     | first_name | last_name | course_name |
| ------ | ---------- | --------- | ----------- |
| 1      | John       | Doe       | <null>      |
| 2      | Jane       | Smith     | <null>      |
| 3      | Bob        | Johnson   | <null>      |
| <null> | <null>     | <null>    | Math        |
| <null> | <null>     | <null>    | English     |
| <null> | <null>     | <null>    | History     |

这里先使用左外连接获取所有学生以及他们选修的课程，然后使用右外连接获取所有课程以及它们的学生。最后通过 `UNION` 合并这两个结果集，得到类似全外连接的效果。

# `ORDER BY`

`ORDER BY` 是 MySQL 中用于对查询结果进行排序的子句。它允许你按照一个或多个列的升序（ASC）或降序（DESC）顺序对结果进行排序。`ORDER BY` 子句通常位于 SQL 查询的最后。

基本的 `ORDER BY` 语法如下：

```sql
SELECT column1, column2, ...
FROM table
ORDER BY column1 [ASC|DESC], column2 [ASC|DESC], ...;
```

- `column1, column2, ...`: 要检索的列的名称。
- `table`: 要查询的表的名称。
- `ORDER BY column1 [ASC|DESC], column2 [ASC|DESC], ...`: 指定一个或多个列以及它们的排序顺序。默认是升序（ASC），如果要降序排序，则使用 `DESC`。

以下是一些使用 `ORDER BY` 的示例：

## 用法

### 1. 按单个列排序：

```sql
-- 按工资降序排序员工信息
SELECT employee_id, first_name, last_name, salary
FROM employees
ORDER BY salary DESC;
```

### 2. 按多个列排序：

```sql
-- 先按部门升序排序，再按工资降序排序员工信息
SELECT employee_id, first_name, last_name, department_id, salary
FROM employees
ORDER BY department_id ASC, salary DESC;
```

### 3. 对字符串列排序：

```sql
-- 按学生姓名升序排序学生信息
SELECT student_id, first_name, last_name
FROM students
ORDER BY first_name ASC;
```

### 4. 按计算列排序：

```sql
-- 按工资和奖金之和降序排序员工信息
SELECT employee_id, first_name, last_name, salary, bonus, (salary + bonus) AS total_compensation
FROM employees
ORDER BY total_compensation DESC;
```

`ORDER BY` 子句也可以与其他子句一起使用，例如 `WHERE`、`GROUP BY`、`HAVING` 等，以便更灵活地组织查询。

## 注意事项

**指定正确的列名：** 确保在 `ORDER BY` 子句中指定的列名确实存在于查询结果中。否则，可能会导致语法错误或不符合预期的结果。

```sql
-- 错误的示例：指定了不存在的列名
SELECT employee_id, first_name, last_name
FROM employees
ORDER BY non_existent_column;
```

**处理 NULL 值：** 在排序时，NULL 值的处理可能需要格外小心。默认情况下，对于升序排序，NULL 值会被排在最前面；而对于降序排序，NULL 值会被排在最后面。你可以使用 `ORDER BY column_name ASC NULLS FIRST` 或 `ORDER BY column_name DESC NULLS LAST` 来明确指定 NULL 值的位置。

```sql
-- 将 NULL 值排在最后面的示例
SELECT employee_id, first_name, last_name
FROM employees
ORDER BY last_name ASC NULLS LAST;
```

**处理文本排序：** 当排序文本列时，可能会受到大小写敏感性和特定字符集的影响。确保在排序之前了解数据库的默认排序规则，并在必要时使用 `COLLATE` 子句指定排序规则。

```sql
-- 指定不区分大小写的排序规则
SELECT student_id, first_name, last_name
FROM students
ORDER BY last_name COLLATE utf8_general_ci;
```

**谨慎使用排序表达式：** 如果在 `ORDER BY` 中使用了表达式，确保表达式的结果是可排序的。有些表达式可能会导致无法预测的排序结果。

```sql
-- 错误的示例：在 ORDER BY 中使用非可排序的表达式
SELECT employee_id, first_name, last_name
FROM employees
ORDER BY CONCAT(last_name, first_name);
```

**性能注意：** 在大型数据集上进行排序可能会影响性能。确保只对需要排序的列进行排序，避免不必要的开销。

```sql
-- 错误的示例：对所有列进行排序
SELECT *
FROM employees
ORDER BY employee_id;
```

**使用 LIMIT 时排序的影响：** 如果你在查询中使用了 `LIMIT` 子句，排序的效果可能会受到限制的影响。`ORDER BY` 通常是在应用 `LIMIT` 之前完成的，因此你可能会得到按照排序顺序的前几行，而不是按照整个结果集排序。

```sql
-- 在排序之前应用 LIMIT 的示例
SELECT employee_id, first_name, last_name
FROM employees
ORDER BY last_name ASC
LIMIT 10;
```





