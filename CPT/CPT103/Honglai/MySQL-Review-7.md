继续继续。。。

# `GROUP BY`

`GROUP BY` 是 MySQL 中用于对查询结果进行分组的子句。它通常与聚合函数（如 `SUM`、`COUNT`、`AVG` 等）一起使用，以便对每个组应用聚合操作。使用 `GROUP BY` 可以将结果集按照一个或多个列的值分成不同的组。

基本的 `GROUP BY` 语法如下：

```sql
SELECT column1, column2, ..., aggregate_function(column)
FROM table
WHERE condition
GROUP BY column1, column2, ...;
```

- `column1, column2, ...`: 要检索的列的名称。
- `aggregate_function(column)`: 对每个组应用的聚合函数，可以是 `SUM`、`COUNT`、`AVG` 等。
- `table`: 要查询的表的名称。
- `WHERE condition`: 可选的筛选条件。
- `GROUP BY column1, column2, ...`: 指定按哪些列进行分组。

以下是一些使用 `GROUP BY` 的示例：

## 用法

### 1. 按单个列分组：

```sql
-- 按部门分组，并计算每个部门的平均工资
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id;
```

### 2. 按多个列分组：

```sql
-- 按部门和性别分组，并计算每个组的员工数量
SELECT department_id, gender, COUNT(*) AS employee_count
FROM employees
GROUP BY department_id, gender;
```

### 3. 使用聚合函数：

```sql
-- 按部门分组，并计算每个部门的员工数量和总工资
SELECT department_id, COUNT(*) AS employee_count, SUM(salary) AS total_salary
FROM employees
GROUP BY department_id;
```

### 4. 结合 `HAVING` 子句：

```sql
-- 按部门分组，并筛选出平均工资超过 50000 的部门
SELECT department_id, AVG(salary) AS avg_salary
FROM employees
GROUP BY department_id
HAVING avg_salary > 50000;
```

### 5. 使用表达式进行分组：

```sql
-- 按出生年份分组，并计算每个年份的员工数量
SELECT YEAR(birthdate) AS birth_year, COUNT(*) AS employee_count
FROM employees
GROUP BY birth_year;
```

## `Having`

`HAVING` 关键字在 SQL 中用于过滤聚合函数的结果。它通常与 `GROUP BY` 子句一起使用，用于筛选分组后的结果。`HAVING` 在过滤分组数据时起到与 `WHERE` 类似的作用，但 `HAVING` 主要用于聚合函数的筛选。

基本的语法结构如下：

```sql
SELECT column1, column2, aggregate_function(column3)
FROM table_name
GROUP BY column1, column2
HAVING condition;
```

- `column1, column2`: 要检索的列的名称，可以是一个或多个列。
- `aggregate_function(column3)`: 对某一列进行聚合函数操作，例如 `SUM`、`COUNT`、`AVG` 等。
- `table_name`: 要检索数据的表的名称。
- `GROUP BY column1, column2`: 用于指定分组的列。
- `condition`: 用于筛选分组数据的条件。

以下是一些 `HAVING` 关键字的使用示例：

### 用法

#### 1. 使用 `HAVING` 过滤聚合结果：

```sql
SELECT department, AVG(salary) as avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 50000;
```

上述查询将返回每个部门的平均工资，但只包括那些平均工资超过 50000 的部门。

#### 2. 结合 `COUNT` 和 `HAVING`：

```sql
SELECT department, COUNT(employee_id) as employee_count
FROM employees
GROUP BY department
HAVING COUNT(employee_id) > 10;
```

上述查询将返回每个部门的员工数量，但只包括那些员工数量超过 10 人的部门。

#### 3. 使用 `SUM` 和 `HAVING`：

```sql
SELECT product_id, SUM(quantity) as total_quantity
FROM order_details
GROUP BY product_id
HAVING SUM(quantity) > 1000;
```

上述查询将返回每个产品的总销售数量，但只包括那些总销售数量超过 1000 的产品。

#### 4. 结合 `GROUP BY` 和 `HAVING`：

```sql
SELECT department, AVG(salary) as avg_salary
FROM employees
WHERE hire_date > '2022-01-01'
GROUP BY department
HAVING AVG(salary) > 50000;
```

在这个例子中，`WHERE` 子句用于在分组之前过滤数据，而 `HAVING` 子句用于筛选符合条件的分组。

## 实操

假设我们有一个 `orders` 表，其中包含订单的信息，如下：

```sql
-- 创建 orders 表
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10, 2)
);

-- 插入一些示例数据
INSERT INTO orders VALUES (1, 101, '2023-01-10', 150.00);
INSERT INTO orders VALUES (2, 102, '2023-02-15', 200.50);
INSERT INTO orders VALUES (3, 101, '2023-02-20', 120.00);
INSERT INTO orders VALUES (4, 103, '2023-03-05', 350.75);
INSERT INTO orders VALUES (5, 102, '2023-03-12', 180.25);
```

现在，我们希望按照 `customer_id` 分组，并计算每个客户的订单数量和总订单金额。以下是一个使用 `GROUP BY` 的查询：

```sql
-- 按照 customer_id 分组，并计算每个客户的订单数量和总订单金额
SELECT customer_id, COUNT(*) AS order_count, SUM(total_amount) AS total_amount
FROM orders
GROUP BY customer_id;
```

在这个查询中，我们使用了 `GROUP BY customer_id` 将结果按照 `customer_id` 列分组。然后，我们使用 `COUNT(*)` 计算每个客户的订单数量，并使用 `SUM(total_amount)` 计算每个客户的总订单金额。

结果应该如下所示：

| customer_id | order_count | total_amount |
| ----------- | ----------- | ------------ |
| 101         | 2           | 270.00       |
| 102         | 2           | 380.75       |
| 103         | 1           | 350.75       |

可以清楚的看到，101的顾客下单数为2，并且总共消费了270.00，以此类推，这就是结果。

然后我们还可以这样：

```sql
-- 按照 customer_id 分组，计算每个客户的订单数量和总订单金额，
-- 并筛选出订单数量超过 1 个且总订单金额超过 300 的客户
SELECT customer_id, COUNT(*) AS order_count, SUM(total_amount) AS total_amount
FROM orders
GROUP BY customer_id
HAVING order_count > 1 AND SUM(total_amount) > 300;
```

就会得到订单数大于一切总消费大于300的顾客：

| customer_id | order_count | total_amount |
| ----------- | ----------- | ------------ |
| 102         | 2           | 380.75       |

# 聚合函数

终于到达这一部分，怎么说呢，这一部分都很简单，都封装好了，你直接调用就可以了。

### 1. **COUNT()**

用于计算行的数量。

```sql
-- 计算 employees 表中的总行数
SELECT COUNT(*) FROM employees;
```

### 2. **SUM()**

用于计算数值列的总和。

```sql
-- 计算 employees 表中工资的总和
SELECT SUM(salary) FROM employees;
```

### 3. **AVG()**

用于计算数值列的平均值。

```sql
-- 计算 employees 表中工资的平均值
SELECT AVG(salary) FROM employees;
```

### 4. **MIN()**

用于找出数值列中的最小值。

```sql
-- 找出 employees 表中最低的工资
SELECT MIN(salary) FROM employees;
```

### 5. **MAX()**

用于找出数值列中的最大值。

```sql
-- 找出 employees 表中最高的工资
SELECT MAX(salary) FROM employees;
```

### 6. **GROUP_CONCAT()**

用于将组内的值连接成一个字符串。通常与`GROUP BY`一起使用。

```sql
-- 将每个部门的员工姓名连接成字符串
SELECT department_id, GROUP_CONCAT(first_name) AS employee_names
FROM employees
GROUP BY department_id;
```

### 7. **GROUP_CONCAT() with ORDER BY**

`GROUP_CONCAT()` 还可以结合 `ORDER BY` 子句，对连接的结果进行排序。

```sql
-- 将每个部门的员工姓名连接成按照姓名升序排序的字符串
SELECT department_id, GROUP_CONCAT(first_name ORDER BY first_name ASC) AS employee_names
FROM employees
GROUP BY department_id;
```

### 8. **GROUP_CONCAT() with DISTINCT**

`GROUP_CONCAT()` 可以与 `DISTINCT` 一起使用，以确保连接的结果中不包含重复的值。

```sql
-- 将每个部门的不重复员工姓名连接成字符串
SELECT department_id, GROUP_CONCAT(DISTINCT first_name) AS employee_names
FROM employees
GROUP BY department_id;
```

### 9. **IFNULL() / COALESCE()**

用于返回第一个非 NULL 的表达式。`IFNULL()` 是 MySQL 特有的，而 `COALESCE()` 是标准 SQL 的函数，两者在大多数情况下是等效的。

```sql
-- 返回工资列，如果为 NULL，则返回 0
SELECT IFNULL(salary, 0) FROM employees;

-- 或者使用 COALESCE
SELECT COALESCE(salary, 0) FROM employees;
```

### 10. **CONCAT()**

用于将两个或多个字符串连接在一起。

```sql
-- 连接 first_name 和 last_name 列
SELECT CONCAT(first_name, ' ', last_name) AS full_name FROM employees;
```

### 11. **SUBSTRING()**

用于提取字符串的子串。

```sql
-- 提取 first_name 列的前三个字符
SELECT SUBSTRING(first_name, 1, 3) AS short_name FROM employees;
```

### 12. **DATE_FORMAT()**

用于将日期格式化为指定的字符串。

```sql
-- 格式化 order_date 列为 '年-月-日' 形式
SELECT DATE_FORMAT(order_date, '%Y-%m-%d') AS formatted_date FROM orders;
```

### 13. **RAND()**

用于生成一个介于 0 和 1 之间的随机数。

```sql
-- 生成一个随机数
SELECT RAND() AS random_number;
```

### 14. **ROUND()**

用于将数值四舍五入到指定的小数位数。

```sql
-- 将 salary 列四舍五入到两位小数
SELECT ROUND(salary, 2) AS rounded_salary FROM employees;
```

### 15. **UPPER() / LOWER()**

用于将字符串转换为大写或小写。

```sql
-- 将 first_name 列转换为大写
SELECT UPPER(first_name) AS upper_name FROM employees;

-- 将 last_name 列转换为小写
SELECT LOWER(last_name) AS lower_name FROM employees;
```

### 16. **TRIM()**

用于去除字符串两端的空格或指定字符。

```sql
-- 去除 description 列两端的空格
SELECT TRIM(description) AS trimmed_description FROM products;
```

### 17. **LENGTH() / CHAR_LENGTH()**

用于返回字符串的字符数或字节数。

```sql
-- 返回 first_name 列的字符数
SELECT LENGTH(first_name) AS name_length FROM employees;

-- 或者使用 CHAR_LENGTH
SELECT CHAR_LENGTH(first_name) AS name_length FROM employees;
```

### 18. **DATEDIFF()**

用于计算两个日期之间的天数差。

```sql
-- 计算订单的创建日期和现在之间的天数差
SELECT DATEDIFF(NOW(), order_date) AS days_since_order FROM orders;
```

### 19. **IF() / CASE WHEN**

用于条件判断。

```sql
-- 如果 salary 大于 50000，则返回 'High'，否则返回 'Low'
SELECT employee_id, first_name, last_name, 
       IF(salary > 50000, 'High', 'Low') AS salary_status
FROM employees;
```

或者使用 `CASE WHEN`：

```sql
SELECT employee_id, first_name, last_name,
       CASE WHEN salary > 50000 THEN 'High' ELSE 'Low' END AS salary_status
FROM employees;
```

### 20. **COU**NT(DISTINCT column)

用于计算唯一值的数量。

```sql
-- 计算 employees 表中不同部门的数量
SELECT COUNT(DISTINCT department_id) AS distinct_departments FROM employees;
```

### 21. **IN()**

用于判断某个值是否在一个集合中。

```sql
-- 查询订单中客户ID为101、102、103的订单
SELECT * FROM orders WHERE customer_id IN (101, 102, 103);
```

### 22. **CONVERT() / CAST()**

用于将一个类型的值转换为另一个类型。

```sql
-- 将字符串 '123' 转换为整数类型
SELECT CONVERT('123', SIGNED) AS converted_value;
```

或者使用 `CAST()`：

```sql
SELECT CAST('123' AS SIGNED) AS casted_value;
```

### 23. LOCATE()

查找子字符串在给定字符串中的位置。它返回子字符串在字符串中的起始位置，如果找不到则返回 0。

```sql
-- 返回 'is' 在 'This is a sample string' 中的位置
SELECT LOCATE('is', 'This is a sample string') AS position;

-- 从第 6 个字符开始，在 'This is a sample string' 中查找 'is'
SELECT LOCATE('is', 'This is a sample string', 6) AS position;

-- 如果 'apple' 存在于 'This is an apple' 中，返回子字符串的位置，否则返回 0
SELECT IF(LOCATE('apple', 'This is an apple'), LOCATE('apple', 'This is an apple'), 'Not found') AS position;
```











