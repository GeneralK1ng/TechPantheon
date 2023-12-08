今天继续复习`MySQL`当中比较重要的一点，但是前面也已经涉及到了，就是`MySQL`中的约束。

但是其实前面的`UNIQUE`，`PRIMARY`之类的也算约束。

# 约束

在关系数据库中，约束是一种用于定义和强制表中数据规则的机制。约束确保了数据的完整性、一致性和可靠性。通过使用约束，数据库管理系统可以强制执行数据表中的规则，防止插入、更新或删除操作导致数据不一致或错误。

简单来说约束就是你在建立表的时候对一些字段的约束条件，比如前面提到的主键，唯一键都是比较重要的约束条件。

## 语法格式

当在MySQL中添加约束时，你可以使用以下语法格式：

```sql
ALTER TABLE table_name
ADD CONSTRAINT constraint_name
CONSTRAINT_TYPE (constraint_details);
```

这里的 `constraint_name` 是你给约束指定的名称，`CONSTRAINT_TYPE` 是约束的类型，而 `constraint_details` 是约束的具体细节。下面是几个常见约束的例子：

比方说我们之前提到的主键

### 主键约束

```sql
ALTER TABLE table_name
ADD CONSTRAINT pk_example PRIMARY KEY (column1, column2, ...);
```

### 外键约束

这个约束有一些知识点需要记忆，这里只给出语法，后面会讲

```sql
ALTER TABLE table_name2
ADD CONSTRAINT fk_example
FOREIGN KEY (column1) REFERENCES table_name1(column1);
```

### 唯一约束

这个更加熟悉，前面也讲过了

```sql
ALTER TABLE table_name
ADD CONSTRAINT uk_example UNIQUE (column1, column2, ...);
```

### 检查约束

这个和外键一样，放到后面讲，这里只给出语法格式

```sql
ALTER TABLE table_name
ADD CONSTRAINT chk_example CHECK (condition);
```

这里介绍了一些常用的约束条件，但是其实说回来一般还是遵循最上面的通用的语法格式。

## 外键

这我猜测应该是一个非常重要的考点，虽然在实际开发中一般通过后端采取逻辑外键和事务处理来保证数据的完整性质，但是毕竟考试嘛，谁猜得到呢？

在MySQL中，外键（Foreign Key）是一种用于建立表与表之间关联的约束。外键用于定义两个表之间的引用关系，其中一个表的列值（或列组合）是另一个表的主键或唯一键的值。这样的关联关系有助于维护数据的完整性，确保相关表之间的数据一致性。

### 基本概念

1. **引用表（Referenced Table）：** 包含被引用列的表称为引用表，通常是主键或唯一键的表。
2. **引用列（Referenced Column）：** 在引用表中的主键或唯一键列被称为引用列。
3. **外键表（Referencing Table）：** 包含外键列的表称为外键表，它引用了引用表中的列。
4. **外键列（Foreign Key Column）：** 在外键表中与引用列相对应的列被称为外键列。

### 语法

上面已经介绍了表已经建立后的语法，这里两种都再给出一下。

在创建表时定义外键：

```sql
CREATE TABLE table_name1 (
    column1 datatype PRIMARY KEY,
    column2 datatype,
    ...
);

CREATE TABLE table_name2 (
    column1 datatype,
    column2 datatype,
    ...
    FOREIGN KEY (column1) REFERENCES table_name1(column1);
);
```

在已有表上添加外键：

```sql
ALTER TABLE table_name2
ADD FOREIGN KEY (column1) REFERENCES table_name1(column1);
```

### 特点

1. **数据一致性：** 外键确保在外键表中的每个值都有对应的引用表中的值，保持数据的一致性。
2. **防止孤立行：** 外键防止在外键表中存在不属于引用表的孤立行，即不存在对应的引用值的行。
3. **级联操作：** 外键可以定义级联操作，例如在引用表中更新主键值时，外键表中相关的外键列的值也会自动更新。

### 参照选项

当定义外键关系时，可以使用不同的参照选项来规定在主表（被引用表）的记录被更新或删除时，应该如何处理从表（引用表、外键表）的记录。以下是一些常见的参照选项：

**RESTRICT（限制）:**

- 如果存在关联记录，阻止对主表的相关操作。如果主表中的记录正在被引用表引用，那么主表的记录不能被删除或更新，以保持引用完整性。

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE RESTRICT
);
```

**CASCADE（级联）:**

- 如果主表的记录被更新或删除，引用表中相关的记录也会被相应地更新或删除。这就是级联操作。

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE CASCADE
);
```

**SET NULL（设置为空）:**

- 如果主表的记录被更新或删除，引用表中相关的外键列将被设置为NULL。

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE SET NULL
);
```

**SET DEFAULT（设置为默认值）:**

- 如果主表的记录被更新或删除，引用表中相关的外键列将被设置为它们的默认值。

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT DEFAULT 1,
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id) ON DELETE SET DEFAULT
);
```

这些选项可以应用于两种情况：`ON DELETE` 和 `ON UPDATE`。前者用于指定在主表的记录被删除时应该如何处理，而后者用于指定在主表的记录被更新时应该如何处理。

意思就是，当主表（被引用表）发生更改的时候，从表（外键表）会怎么样，会根据你提前设计的这些进行一些默认操作。

### 示例

考虑一个部门（`departments`）和员工（`employees`）两个表的情况：

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);
```

1. **确保数据一致性：** 外键关系确保`employees`表中的每个员工的`department_id`值都存在于`departments`表的`department_id`列中。这样，你不能在`employees`表中插入一个不存在的部门ID，从而确保了数据的一致性。
2. **防止孤立行：** 外键关系防止`employees`表中的行变得孤立，即存在不属于`departments`表中任何部门的员工。每个在`employees`表中的`department_id`值都必须在`departments`表中有对应的部门ID。
3. **禁止删除被引用的行：** 如果你尝试删除`departments`表中的某个部门（带有相关的员工），MySQL默认情况下将不允许这样的删除，以防止破坏外键约束。这防止了误删除操作，确保数据的完整性。
4. **级联操作：** 可以通过定义外键时的级联选项来实现级联操作。例如，如果定义了级联更新，当`departments`表中的某个部门ID更新时，`employees`表中所有相关的`department_id`也会自动更新

具体来说：

假设我们有两张表，一张是部门表，另一张是员工表。我们想要在员工表中记录每个员工所在的部门。

部门表 (`departments`)：

| department_id | department_name  |
| ------------- | ---------------- |
| 1             | Sales Department |
| 2             | IT Department    |
| 3             | HR Department    |

员工表 (`employees`)：

| employee_id | first_name | last_name | department_id |
| ----------- | ---------- | --------- | ------------- |
| 101         | John       | Smith     | 1             |
| 102         | Emily      | Johnson   | 2             |
| 103         | Michael    | Wang      | 1             |

#### 为什么使用外键？

1. **关联员工和部门：** 我们使用外键来确保员工表中的 `department_id` 值始终对应着部门表中的 `department_id` 值。这样，我们知道每个员工属于哪个部门。
2. **防止错误：** 外键帮助我们防止错误的数据插入，例如，如果我们试图在员工表中插入一个不存在的部门ID，系统会阻止这样的操作。

#### 外键语法中的哪个是引用表，哪个是外键表？

在这个例子中：

- **被引用表 (`Referenced Table`)：** 部门表 (`departments`) 是引用表，因为它包含了被引用的列 `department_id`，这是员工表中 `department_id` 列的来源。
- **外键表 (`Referencing Table`)：** 员工表 (`employees`) 是外键表，因为它包含了外键列 `department_id`，这是与部门表中的 `department_id` 列相关联的列。

外键建立了两张表之间的关系，就像一种连接。它确保了员工表中的每个员工都与部门表中的一个部门相对应，而且部门表中的每个部门都可以与员工表中的多个员工相对应。这种关系有助于我们更好地组织和理解数据。

#### 操作被引用表（`departments`）：

1. **插入新的部门：** 如果你想在`departments`表中插入新的部门，这是允许的，因为`departments`表是引用表，没有其他表直接引用它。
2. **更新部门ID：** 如果你更新了`departments`表中的某个部门ID，且该部门ID在`employees`表的`department_id`列中有对应的值，系统的默认设置下可能会阻止这个更新操作。这是因为外键关系会防止引用表中的主键（或唯一键）发生变化，以维护数据的完整性。你可能需要先更新`employees`表中的相关外键列，然后再更新`departments`表。
3. **删除部门：** 如果你尝试删除`departments`表中的某个部门，系统可能会阻止这个操作，因为还存在与该部门相关联的员工（在`employees`表中有相应的`department_id`）。你可能需要先删除相关的员工记录，然后才能删除部门。

#### 操作外键表（`employees`）：

1. **插入新员工：** 你可以在`employees`表中插入新员工，但是要确保插入的`department_id`值存在于`departments`表中，否则系统会阻止这个操作。
2. **更新员工所在部门：** 如果你更新了`employees`表中的某个员工的`department_id`，只要新的`department_id`值在`departments`表中存在，这是允许的。
3. **删除员工：** 你可以删除`employees`表中的员工，不会直接影响`departments`表。但是，如果你删除了`departments`表中的某个部门，而该部门在`employees`表中有员工，系统可能会阻止这个操作，要求你先删除相关的员工。

## 检查约束

在MySQL中，检查约束（Check Constraint）是一种用于定义在插入或更新行时必须满足的条件的机制。它允许你指定在某个列或一组列上的数据必须满足的条件，以确保数据的有效性和一致性。如果检查约束中的条件不满足，MySQL将拒绝插入或更新操作。

语法格式如下：

```sql
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype,
    ...
    CHECK (condition);
);
```

或者在已有表上添加检查约束：

```sql
ALTER TABLE table_name
ADD CHECK (condition);
```

其中，`condition` 是一个逻辑表达式，表示需要满足的条件。如果 `condition` 为真，操作将被允许，否则将被拒绝。

### 示例 

假设我们有一个存储订单信息的表，要求订单的总金额不能为负数：

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    order_date DATE,
    total_amount DECIMAL(10,2),
    CHECK (total_amount >= 0)
);
```

在这个例子中，我们使用检查约束确保 `total_amount` 列的值必须大于等于零。如果尝试插入或更新一条订单，使得总金额小于零，系统将拒绝这个操作。

### 注意事项：

1. **CHECK约束条件：** `condition` 中可以包含对一列或多列的条件，以及多个逻辑操作符（AND、OR、NOT）。
2. **可选性：** CHECK约束是可选的，你可以选择使用或不使用它，具体取决于业务需求。
3. **兼容性：** 需要注意的是，MySQL在早期版本中对CHECK约束的支持并不完整，因此在使用CHECK约束时需要确保使用的MySQL版本支持这一特性。

这玩意也不常用，一般都靠我们的后端来进行校验，数据库层面一般很少做校验的。

# 字符串比较

没想到啊，这个课件真是误人子弟，说MySQL的字符串比较是不区分大小写的。。。

其实，字符串的比较是否区分大小写，本质上，MySQL的默认字符串比较是取决于所使用的字符集和排序规则（collation）的。一般而言，对于一些常见的字符集，例如UTF-8，其默认的排序规则通常是不区分大小写的，但这并不是绝对的。

在MySQL中，可以通过查看字符集和排序规则的默认值来了解默认的字符串比较行为。例如，对于UTF-8字符集，`utf8_general_ci`是一个常见的不区分大小写的排序规则，而`utf8_bin`是区分大小写的规则。

这个`bin`就是二进制`binary`的简写。

## 二进制比较

- **基于字节比较：** 二进制比较将字符串视为一系列字节，而不考虑字符的编码。每个字符都是由一个或多个字节组成的。
- **区分大小写：** 二进制比较是区分大小写的，即大写字母和小写字母被视为不同的值。
- **不受字符集和排序规则影响：** 二进制比较不考虑字符集和排序规则，直接比较字符串的字节。

```sql
SELECT 'abc' = BINARY 'ABC';  -- 返回 0，因为它是区分大小写的二进制比较
```

## 字符集比较

- **基于字符比较：** 字符集比较考虑字符的编码和排序规则。字符集规定了字符的编码方式，而排序规则定义了字符串的排序规则，包括如何处理大小写、重音符号等。
- **可能不区分大小写：** 根据具体的字符集和排序规则，字符集比较可能是区分大小写的，也可能是不区分大小写的。
- **受字符集和排序规则影响：** 字符集比较会受到所使用的字符集和排序规则的影响。

```sql
SELECT 'abc' COLLATE utf8_general_ci = 'ABC' COLLATE utf8_general_ci;  -- 返回 1，因为它是不区分大小写的字符集比较
```

## 选择使用的情况

- **二进制比较：** 适用于需要完全区分大小写、直接比较字节的场景，或者在处理二进制数据时。
- **字符集比较：** 适用于普通文本字符串的比较，可以根据语言、地区等要求选择合适的字符集和排序规则，确保字符串的比较和排序符合预期。

好，那么其实在MySQL当中会有一些默认的情况会使用字符集比较，比如在主键和唯一键的判断当中，就会默认使用字符集比较，而且并不是二进制比较，这也是课件说MySQL的字符串比较是不区分大小写的原因。

在MySQL中，主键和唯一键（Unique Key）的约束条件判断默认是受到字符集和排序规则的影响的。具体来说，如果使用的字符集和排序规则是区分大小写的，那么主键和唯一键的约束条件也会区分大小写；如果字符集和排序规则是不区分大小写的，那么约束条件也会不区分大小写。

比如：

```sql
CREATE TABLE example_table (
    id INT PRIMARY KEY,
    name VARCHAR(50) UNIQUE
) COLLATE utf8_general_ci;
```

在上述例子中，表 `example_table` 的字符集和排序规则被指定为 `utf8_general_ci`，这是一个不区分大小写的排序规则。因此，主键和唯一键 `name` 的约束条件在比较时将不区分大小写。

如果你使用的字符集和排序规则是区分大小写的，你可以在创建表时显式指定，例如：

```sql
CREATE TABLE example_table (
    id INT PRIMARY KEY,
    name VARCHAR(50) UNIQUE
) COLLATE utf8_bin;
```

在这种情况下，主键和唯一键 `name` 的约束条件将区分大小写。

而二进制比较只会在你使用`BINARY`关键字的时候才会使用。

> 字符集的比较在以下情况下非常重要：
>
> 1. **文本搜索和匹配：** 如果你的应用需要执行基于文本的搜索、匹配或过滤操作，字符集比较就变得至关重要。例如，你可能希望在数据库中执行不区分大小写的搜索，或者处理包含重音符号的字符。
> 2. **排序和比较：** 如果你需要对字符串进行排序，或者在查询中进行字符串的比较，字符集和排序规则就会影响到排序的结果和比较的行为。在不同的语言环境下，字符集和排序规则的选择可能有所不同。
> 3. **唯一性约束：** 当你在数据库中定义唯一键或唯一索引时，字符集和排序规则会影响到是否区分大小写。这对于确保唯一性约束的正确性非常重要。
> 4. **国际化和多语言支持：** 如果你的应用需要支持多种语言，特别是包含非拉丁字符的语言，选择正确的字符集和排序规则是确保正确处理和显示文本的关键。
> 5. **用户输入和输出：** 当用户与应用交互时，字符集和排序规则会影响到用户输入的处理和在界面上显示的文本。确保使用合适的字符集可以提供更好的用户体验。

# `ALTER`语句

前面也看到了很多`ALTER`的操作，一般都是在表格创建好之后，如果对于表还有什么额外的操作，那么我们就可以使用这个关键字，来进行一些额外的操作。

这里就进行一下关于这个语句的一些总结。

### 1. 添加列：

```sql
ALTER TABLE table_name
ADD COLUMN column_name datatype;
```

这将在指定的表中添加一个新列。

### 2. 修改列的数据类型：

```sql
ALTER TABLE table_name
MODIFY COLUMN column_name new_datatype;
```

这将更改表中指定列的数据类型。

### 3. 修改列的名称：

```sql
ALTER TABLE table_name
CHANGE COLUMN old_column_name new_column_name datatype;
```

这将修改表中指定列的名称和数据类型。

### 4. 删除列：

```sql
ALTER TABLE table_name
DROP COLUMN column_name;
```

这将从表中删除指定的列。

### 5. 添加主键：

```sql
ALTER TABLE table_name
ADD PRIMARY KEY (column1, column2, ...);
```

这将在表上添加主键。

### 6. 移除主键：

```sql
ALTER TABLE table_name
DROP PRIMARY KEY;
```

这将从表上移除主键。

### 7. 添加外键：

```sql
ALTER TABLE table_name
ADD CONSTRAINT fk_name
FOREIGN KEY (column_name)
REFERENCES referenced_table(referenced_column);
```

这将在表上添加外键约束。

### 8. 移除外键：

```sql
ALTER TABLE table_name
DROP FOREIGN KEY fk_name;
```

这将从表上移除指定的外键约束。

### 9. 修改表名：

```sql
ALTER TABLE old_table_name
RENAME TO new_table_name;
```

其实之前都见过，这里只是做个总结。
