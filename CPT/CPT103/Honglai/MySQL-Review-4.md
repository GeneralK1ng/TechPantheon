今天来继续复习`MySQL`中的`SELECT`语句，也是以后基本上用的最多的查询操作。

# `SELECT`

## 一般形式

`SELECT`主要用于从数据库当中检索数据，这是它的一般形式。

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

- `column1, column2, ...`: 要检索的列的名称，可以是一个或多个列，也可以使用通配符`*`表示选择所有列。
- `table_name`: 要检索数据的表的名称。
- `WHERE condition`: 可选的条件，用于过滤检索的数据。如果省略此部分，将检索表中的所有行。

后面我们会讲到`WHERE`关键字，现在只需要知道后面跟的是需要查询的条件。

以下是一个简单的SELECT语句的例子：

```sql
SELECT first_name, last_name
FROM employees
WHERE department = 'IT';
```

这个例子从名为"employees"的表中选择了"first_name"和"last_name"列，其中部门是"IT"的所有员工。

## 注意事项

1. **语法规范：** 确保SELECT语句的语法正确。检查列名、表名和条件是否正确拼写，并使用正确的SQL语法。
2. **数据类型匹配：** 确保条件中使用的数据类型与数据库表中的数据类型匹配。例如，如果某列是字符串类型，条件中的值应该用单引号括起来。
3. **通配符的使用：** 谨慎使用通配符`*`，因为它选择表中的所有列。这可能导致不必要的数据传输和性能问题。最好只选择需要的列。
4. **索引的使用：** 使用索引可以加速SELECT语句的执行。确保表中经常用于检索的列上创建了适当的索引。
5. **避免使用SELECT \*：** 尽量避免使用`SELECT *`，而是明确列出需要检索的列。这有助于减少数据传输和提高查询性能。
6. **条件优化：** 使用有效的条件来过滤结果集，以减少返回的数据量。这有助于提高查询性能。
7. **数据安全：** 对于用户提供的数据，使用参数化查询或预处理语句，以防止SQL注入攻击。
8. **使用LIMIT：** 当你只需要获取前几行结果时，使用`LIMIT`子句来限制结果集的大小，从而提高性能。

```sql
SELECT column1, column2
FROM table_name
WHERE condition
LIMIT 10;
```

这样会通过`LIMIT`来限制查询出来的结果只为10条。

# `DISTINCT` 和 `ALL`

`DISTINCT` 和 `ALL` 是 MySQL 中用于处理重复数据的关键字，它们通常用于配合 `SELECT` 语句，影响查询结果中的重复行。

## `DISTINCT`

`DISTINCT` 关键字用于从结果集中去除重复的行，只返回唯一的行。以下是一些基本用法：

```sql
SELECT DISTINCT column1, column2, ...
FROM table_name
WHERE condition;
```

- `column1, column2, ...`: 要检索的列的名称，可以是一个或多个列，也可以使用通配符 `*` 表示选择所有列。
- `table_name`: 要检索数据的表的名称。
- `WHERE condition`: 可选的条件，用于过滤检索的数据。

例如：

```sql
SELECT DISTINCT department
FROM employees;
```

上述查询将返回唯一的部门列表，去除了重复的部门名。

## `ALL`

`ALL` 关键字用于保留所有的行，包括重复的行。它通常用于与聚合函数（如 `COUNT()`、`SUM()` 等）一起使用，以便在整个结果集上执行聚合操作。以下是一些基本用法：

```sql
SELECT ALL column1, column2, ...
FROM table_name
WHERE condition;
```

例如：

```sql
SELECT ALL salary
FROM employees
WHERE department = 'IT';
```

这个查询将返回部门为 'IT' 的所有员工的薪水，包括重复的薪水值。

需要注意的是，默认情况下，`ALL` 是隐式的，也就是说，当没有指定 `DISTINCT` 时，`ALL` 是默认的行为。因此，通常在不需要去除重复行的情况下，你可以省略 `ALL`。

```sql
-- 下面两个查询等效
SELECT column1, column2, ...
FROM table_name
WHERE condition;

SELECT ALL column1, column2, ...
FROM table_name
WHERE condition;
```

# `AS`

在MySQL中，`AS` 关键字用于给列或表达式指定别名（alias）。别名是一个临时的名称，使得查询结果更易读或用于标识计算列的结果。

**`AS` 可以省略**，直接使用空格或不使用别名，但添加别名通常能够提高查询结果的可读性。

以下是 `AS` 关键字的基本用法：

## 1. 列别名：

```sql
SELECT column_name AS alias_name
FROM table_name;
```

例子：

```sql
SELECT first_name AS "First Name", last_name AS "Last Name"
FROM employees;
```

在上述例子中，查询结果中的 `first_name` 和 `last_name` 列被分别命名为 "First Name" 和 "Last Name"。

## 2. 表别名：

```sql
SELECT column1, column2, ...
FROM table_name AS alias_name;
```

例子：

```sql
SELECT e.first_name, e.last_name, d.department_name
FROM employees AS e
JOIN departments AS d ON e.department_id = d.department_id;
```

在上述例子中，`employees` 表和 `departments` 表被分别用 `e` 和 `d` 作为别名，以简化查询语句。

## 3. 列表达式别名：

```sql
SELECT column_name + 10 AS alias_name
FROM table_name;
```

例子：

```sql
SELECT salary * 0.1 AS bonus
FROM employees;
```

在上述例子中，`salary * 0.1` 被命名为 `bonus`。

`AS` 关键字在这些情境中是可选的，你可以直接使用空格：

```sql
SELECT column_name alias_name
FROM table_name;
```

或者干脆省略：

```sql
SELECT column_name
FROM table_name;
```

但是我的习惯一般是要把`AS`加上，一种代码的规范。

# `WHERE`

`WHERE` 关键字在 MySQL 中用于过滤从表中检索的数据，它允许你指定一个条件，只有符合条件的行才会包含在查询的结果集中。以下是 `WHERE` 关键字的基本用法：

```sql
SELECT column1, column2, ...
FROM table_name
WHERE condition;
```

- `column1, column2, ...`: 要检索的列的名称，可以是一个或多个列，也可以使用通配符 `*` 表示选择所有列。
- `table_name`: 要检索数据的表的名称。
- `condition`: 过滤条件，用于确定哪些行应该包含在结果集中。

以下是一些 `WHERE` 关键字的用法和示例：

## 用法



### 1. 简单条件：

```sql
SELECT *
FROM employees
WHERE department_id = 1;
```

上述查询将返回 `department_id` 等于 1 的所有员工的信息。

### 2. 多个条件：

```sql
SELECT *
FROM orders
WHERE customer_id = 1 AND order_status = 'Shipped';
```

上述查询使用 `AND` 运算符结合两个条件，只返回 `customer_id` 为 1 且 `order_status` 为 'Shipped' 的订单。

这里的`AND`不用说也知道表示“且”，后面会单独讲，这里只作为例子。

### 3. 范围条件：

```sql
SELECT *
FROM products
WHERE price BETWEEN 50 AND 100;
```

上述查询使用 `BETWEEN` 关键字，返回价格在 50 到 100 之间的产品。

其实这里也能看出来，写SQL就跟写英语一样。。

### 4. 字符串匹配：

```sql
SELECT *
FROM customers
WHERE last_name LIKE 'S%';
```

上述查询使用 `LIKE` 关键字，返回姓氏以 'S' 开头的客户。

### 5. 空值检查：

```sql
SELECT *
FROM employees
WHERE manager_id IS NULL;
```

上述查询使用 `IS NULL`，返回 `manager_id` 列为空的员工。

### 6. IN 子句：

```sql
SELECT *
FROM products
WHERE category_id IN (1, 2, 3);
```

上述查询使用 `IN` 关键字，返回属于指定类别的产品。

### 7. 复杂条件：

```sql
SELECT *
FROM orders
WHERE (customer_id = 1 AND order_status = 'Shipped') OR (customer_id = 2 AND order_status = 'Processing');
```

就。。复杂条件，只要有小学英文水平和逻辑能力，基本上都能看懂。

## 注意事项

这一部分对于开发来说是非常重要的，不然会出大问题。

**注意条件的顺序：** `WHERE`中的条件顺序可能会影响查询的性能。一般来说，更有选择性的条件应该放在前面，以便尽早排除不符合条件的行。

```sql
-- 不推荐
SELECT *
FROM employees
WHERE department_id = 1 AND last_name LIKE 'S%';

-- 推荐
SELECT *
FROM employees
WHERE last_name LIKE 'S%' AND department_id = 1;
```

**避免在列上使用函数：** 在`WHERE`条件中，避免在列上使用函数，因为这可能导致索引失效，从而降低查询性能。

```sql
-- 不推荐
SELECT *
FROM products
WHERE YEAR(sale_date) = 2023;

-- 推荐
SELECT *
FROM products
WHERE sale_date >= '2023-01-01' AND sale_date < '2024-01-01';
```

**使用索引：** 确保涉及到`WHERE`条件的列上有适当的索引。索引可以加速数据检索，提高查询性能。

**谨慎使用`NULL`：** 当使用`NULL`作为条件时，要小心，因为在SQL中，与`NULL`的比较不同于与其他值的比较。使用`IS NULL`或`IS NOT NULL`来检查`NULL`值。

```sql
-- 不推荐
SELECT *
FROM employees
WHERE manager_id = NULL;

-- 推荐
SELECT *
FROM employees
WHERE manager_id IS NULL;
```

**避免在索引列上使用函数：** 如果在索引列上使用了函数，索引可能无法被有效利用，从而导致查询性能下降。

```sql
-- 不推荐
SELECT *
FROM products
WHERE YEAR(sale_date) = 2023;

-- 推荐
SELECT *
FROM products
WHERE sale_date >= '2023-01-01' AND sale_date < '2024-01-01';
```

**使用合适的比较运算符：** 根据具体情况选择合适的比较运算符，如`=`、`<`、`>`、`<=`、`>=`等。

```sql
SELECT *
FROM products
WHERE price > 100;
```

**防止SQL注入：** 使用参数化查询或预处理语句，以避免SQL注入攻击。不要直接将用户输入的值嵌入到SQL查询中。

```sql
-- 避免
$user_input = "Robert'; DROP TABLE employees;";
$sql = "SELECT * FROM users WHERE username = '$user_input'";

-- 推荐
$user_input = "Robert'; DROP TABLE employees;";
$stmt = $pdo->prepare("SELECT * FROM users WHERE username = ?");
$stmt->execute([$user_input]);
```

这些是开发层面的注意事项，对于考试应该帮助不大。

# `LIKE`

`LIKE` 关键字在 MySQL 中用于模糊匹配，通常与通配符一起使用，以便在搜索中匹配模式而不是确切的值。

这是它的一般语法

```sql
SELECT column1, column2, ...
FROM table_name
WHERE column_name LIKE pattern;
```

- `column1, column2, ...`: 要检索的列的名称，可以是一个或多个列，也可以使用通配符 `*` 表示选择所有列。
- `table_name`: 要检索数据的表的名称。
- `column_name`: 要进行模糊匹配的列的名称。
- `pattern`: 匹配模式，可以包含通配符。

以下是一些 `LIKE` 关键字的使用示例：

## 用法

### 1. 百分号 `%` 通配符：

```sql
-- 以 'S' 开头的所有数据
SELECT *
FROM employees
WHERE last_name LIKE 'S%';
```

上述查询将返回姓氏以 'S' 开头的所有员工的信息。

```sql
-- 包含 'son' 的任何位置的数据
SELECT *
FROM products
WHERE product_name LIKE '%son%';
```

上述查询将返回产品名称中包含 'son' 的所有产品。

而且结合之前讲的字符串比较，可以想想这里的匹配是否区分大小写？

### 2. 下划线 `_` 通配符：

```sql
-- 第三个字母为 'i' 的所有数据
SELECT *
FROM customers
WHERE last_name LIKE '__i%';
```

上述查询将返回姓氏中第三个字母为 'i' 的所有客户的信息。

### 3. NOT LIKE：

```sql
-- 不以 'A' 开头的所有数据
SELECT *
FROM employees
WHERE last_name NOT LIKE 'A%';
```

上述查询将返回姓氏不以 'A' 开头的所有员工的信息。

### 4. 使用 ESCAPE：

```sql
-- 查找包含 '%' 的数据
SELECT *
FROM products
WHERE product_name LIKE '%\%%' ESCAPE '\';
```

在某些情况下，如果你需要搜索包含 `%` 字符的实际数据，你可以使用 `ESCAPE` 子句来指定一个转义字符。

### 5. 指定多个匹配条件：

```sql
-- 以 'S' 或 'M' 开头的数据
SELECT *
FROM employees
WHERE last_name LIKE 'S%' OR last_name LIKE 'M%';
```

在某些情况下，你可能需要指定多个匹配条件。

## 注意事项

这些也是开发层面需要注意的事情。

**性能问题：** `LIKE`语句可能会导致性能问题，尤其是在大型表上，因为它可能无法使用索引。当`LIKE`模式以通配符开头（例如，`%something`）时，索引无法有效使用。在这种情况下，可以考虑其他优化方法，例如全文搜索。

**通配符的位置：** 通配符的位置影响查询的性能。使用通配符 `%` 在模式的开头（例如，`%something`）可能导致全表扫描，而使用通配符在模式的结尾（例如，`something%`）可能能够利用索引。

**大小写敏感性：** 默认情况下，`LIKE`是大小写敏感的。如果你希望进行大小写不敏感的匹配，可以使用`LOWER`或`UPPER`函数：

```sql
SELECT *
FROM employees
WHERE LOWER(last_name) LIKE 's%';
```

1. **字符集和排序规则：** MySQL的`LIKE`操作是基于字符集和排序规则的。确保你了解你的数据表和数据库使用的字符集及排序规则，以免导致意外的匹配或不匹配。
2. **避免在长文本上使用：**`%`在长文本字段上可能导致性能问题。如果只是需要检查是否包含某个子字符串，考虑使用`LOCATE`或`INSTR`等函数。

```sql
SELECT *
FROM products
WHERE LOCATE('son', product_name) > 0;
```

**注意转义字符：** 如果你的模式中包含通配符字符 `%` 或 `_`，并且你确实想要匹配这些字符本身，而不是它们的通配符含义，你需要使用转义字符。在MySQL中，默认的转义字符是`\`。例如，如果要匹配包含 `%` 字符的数据，你可以使用：

```sql
SELECT *
FROM products
WHERE product_name LIKE '%\%%' ESCAPE '\';
```

**使用全文搜索：** 对于大型文本数据，全文搜索可能是更有效的选择。MySQL提供了全文搜索的功能，例如`MATCH ... AGAINST`语法。

**避免在计算列上使用：**`%`在计算列上使用可能导致索引失效。如果可能，尽量在原始列上使用`LIKE`。

```sql
-- 不推荐
SELECT *
FROM employees
WHERE CONCAT(first_name, last_name) LIKE 'John%';

-- 推荐
SELECT *
FROM employees
WHERE first_name LIKE 'John%';
```





