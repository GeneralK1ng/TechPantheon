# for 40 quiz questions

# Intro

Why：没人应该在没有真正价值的事情上浪费时间，此笔记意在帮助各位尽量少的花时间去应付CPT103考试。

Who：准备CPT103期末之前quiz的人群。

What：深入浅出的按照轻重缓急列出了知识点和对应的题目。

How to use: 按照顺序阅读复习。请确保你拥有最基础的知识储备（例如如何创建SCHEMA，如何创建TABLE等等），且善于Google自己看不懂的关键词并理解它。

How to continue: 加入我们的社群一起讨论交流，在加入之前请您保证拥有平等友善的交流心态。有任何关于改进的建议，欢迎直接私信提出。

# 往年题目数据统计

- SQL规范化理论：9题
- SQL中表的并联：6题
- SQL中值的插入：6题
- SQL中的数据库设计（包括实体-关系E-R模型和E-R之间的关系类型）：5题
- SQL创建表（包括查询顺序等）：4题
- SQL中的三值逻辑：2题
- SQL中的字符串语法：1题
- SQL当中给表添加键（主键，外键等）：2题
- SQL当中UNION的用法：1题
- SQL当中LIKE的用法：1题
- SQL当中改表：1题
- SQL术语：1题
- SQL更新异常：1题
- SQL数据库DBMS：1题

# 正文

## SQL规范化理论 9

请确保你对于SQL规范化的关键词有初步了解（1NF，2NF，3NF，Partial Dependency等），以下是相关内容。

1. **1NF（第一范式 - First Normal Form）**：
    - 1NF 是数据库规范化的第一步。一个表被认为满足1NF，当且仅当：
        - 表中的每一列都包含原子值，即不可再分。
        - 表中的每一行都具有唯一的标识，通常是一个主键。
    - 1NF 确保数据表中的每个单元格都包含一个单一的、不可再分的值。这有助于消除重复和冗余数据。
2. **2NF（第二范式 - Second Normal Form）**：
    - 2NF 是在1NF的基础上进一步发展的。一个表被认为满足2NF，当且仅当：
        - 它满足1NF。
        - 所有非主键列都完全依赖于主键。
    - 2NF 旨在消除非主键列对主键的部分依赖，确保每个非主键列都与主键相关。
3. **3NF（第三范式 - Third Normal Form）**：
    - 3NF 是在2NF的基础上发展的。一个表被认为满足3NF，当且仅当：
        - 它满足2NF。
        - 所有非主键列都不传递依赖于主键。
    - 3NF 旨在消除非主键列之间的传递依赖，确保数据表中的每一列都直接与主键相关，避免数据冗余。
4. **部分依赖（Partial Dependency）**是指在一个关系表中，一个非主键列仅依赖于复合主键的一部分而非全部。eg:对于表 (a, b, c, d)，主键(a, b)。b → d是部分依赖。
5. **传递依赖（Transitive Dependency）**是指当一个非主键列通过另一个非主键列间接依赖于主键时，就会出现数据库表中的传递依赖关系。 这个概念在数据库规范化过程中至关重要，特别是在转向第三范式（3NF）时。eg: For table (a, b, c, d) with primary key on column (a, b) and the dependency c - > d.
6. **基数（Cardinality）**是指表中独特记录的数量。

## SQL中值的插入 6

请确保你理解所有关于值的关键词（CHAR，VARCHAR，NOT NULL等）

1. 一般来说默认值都是零 eg: INTEGER 列下只能插入NULL， INTEGER（20）列下则可以插入20位及以下的整数
2. SQL当中默认的时间格式[****-**-**]  [** : ** : **.*] eg: “9876-01-01 00:00:01.1”
3. 在SQL当中每个列只能有一个默认值，不能为单个列制定多个默认值。数据库将默认值视为普通数据，默认值的灵活性在于它们可以代表不同类型的缺失信息，如未知数据或不适用。
4. 外键可以引用同一张表中的主键。
5. 插入对应的主键不可以为null，UNIQUE可以
6. VALUES前后的变量要对齐

## SQL创建表

### SQL当中表的并联 6

请确保你理解所有的创建表关键词（SELECT，INNER JOING，USING等）

1. 评估顺序：FROM→INNER JOING→ USING→ SELECT eg: SELECT a, r.b, s.b, c, d FROM r INNER JOIN s USING (r.a < s.c AND r.b < s.b)
2. 子查询（Subquery）是嵌套在其他查询中的SQL查询 eg:SELECT student FROM m_list WHERE mark > (SELECT avg(mark) FROM m_list)
3. 内部连接（Inner Join）是将两个表的行结合起来基于某个条件进行比较的方法。eg:SELECT m_list.student FROM m_list INNER JOIN (SELECT avg(mark) AS x FROM m_list) AS t2 ON (1 + 1 = 2) WHERE mark > x
4. 左外连接和右外连接，行数的底线都以左边为准。
5. 可以先选中一个变量，在从第二张表里选出第二个变量，筛选第一个变量SELECT DISTINCT staffID FROM Staff s1 WHERE age IN (SELECT age FROM Staff s2 WHERE s1.staffID <> s2.staffID); SELECT DISTINCT s1.staffID FROM staff s1, staff s2 WHERE s1.age = s2.age AND s1.staffID <> s2.staffID;

### SQL当中的查询顺序 4

请确保你理解所有常见的的查询关键词（WHERE，GROUP BY，HAVING等）

1. SELECT并不是第一个进行评估的。FROM会先于SELECT，因为它指定了信息源。
2. 顺序：FROM → WHERE → GROUP BY → HAVING → SELECT eg: SELECT part1 FROM part2 WHERE part3 GROUPED BY part4 HAVING part5
3. 如果想要计算总体平均值，GROUP BY进行的分组需要按照(Unique)Primary Key来分组
4. SELECT avg（mark）FROM marks = SELECT sum(mark)/count(id) FROM marks

### SQL当中给表添加键（主键，外键等）2

请确保你理解所有的添建键的关键词（ALTER，CONSTRAINT，PRIMARY KEY等）

1. 添加主键应该保证主键不会重复。eg: 书的信息就不应该使用数名作为主键，应该使用ISBN等ID信息。
2. 创建主键等正确语法是使用“ADD CONSTRAINT”而不是“CREATE”， eg:ALTER TABLE Book ADD CONSTRAINT pk PRIMARY KEY (ISBN);

### SQL当中UNION的用法 1

在 SQL 中，使用 UNION 运算符来合并两个或多个 SELECT 语句的结果集需要遵循特定的语法规则。

1. 正确的UNION用法是将两个独立的SELECT语句连接起来
2. 在UNION之外多加一个SELECT * FROM是没有意义的，但是可以使用括号包裹起来后作为一个临时表来查询 eg**:**SELECT * FROM ((SELECT * FROM a) UNION (SELECT * FROM b)) AS ab;

### SQL当中LIKE的用法 1

1. 默认不区分大小写

### SQL当中改表 1

1. 在MySQL中，ALTER TABLE语句用于修改表的结构，包括添加、删除或修改列和索引。
2. 删除主键ALTER TABLE table DROP PRIMARY KEY，删除唯一键（不是UNIQUE）ALTER TABLE table DROP INDEX name，移除外键ALTER TABLE table DROP FOREIGN KEY name，移除列ALTER TABLE table DROP COLUMN name

## SQL中的数据库设计

### E-R之间的关系类型（一对一、一对多、多对多）3

请确保你理解E-R模型

1. 锚定一个对象。eg：一个教师不可以上多门课，但每一门课都可以有多个老师。这就是一个一对多的关系。这里锚定的对象是课程。
2. 多对多 eg: 一门课有很多学生，一个学生可以选择5门课。
3. 一对一的关系会导致数据冗余和表连接的效率更低

### 实体-关系E-R模型 1

请确保你理解实体-关系模型。

1. 在E-R模型中，涉及到大量的一对多的关系。而在一对多的关系中，外键（Foreign Keys）被用于在关系数据库中创建关联，允许数据的完整性和引用的完整性。

## SQL中的三值逻辑（True，False，Unknown）2

请确保你理解True和False之间AND和OR的关系。三值逻辑的重点应该放在Unknown上：

1. 在SQL当中，Unknown无论和任何数相加都是Unknown。eg: Unknown+1 = Unknown。
2. 在OR逻辑运算中，有True就是True，没有True的情况下有Unknown就是Unknown。
3. 在AND逻辑运算中，有Unknown结果就是Unknown。

## SQL中的字符串语法 1

请确保你理解单引号‘和双引号“不能混着用。字符串语法的重点应该放在引号之内的特殊字符上。

1. 反斜杠转义字符‘\’，这个字符意味着应该将紧随其后的字符串视为普通字符，而非特殊字符。 eg: 'example \'string''  ‘ → example ‘string’

## SQL术语 1

1. “领域”（Domain）指的是某个特定属性（或列）可以取值的范围或集合。它不是关于元组（或行）数量的。
2. 关系模型是由埃德加·科德（Edgar Codd）在1970年提出的，用于数据库管理。在此之前，已经存在其他数据模型，如层次模型和网络模型。

## SQL更新异常（anomalies） 1

1. 更新异常是数据库设计中一个重要的概念，指的是在不适当地设计的数据库结构中进行插入、删除或修改操作时可能遇到的问题。插入异常可能发生在表中存在部分依赖的情况下。删除异常可能发生在表中存在传递依赖的情况下。修改异常可能发生在表处于非规范化形式的情况下。规范化表可以帮助减少更新异常。

## SQL数据库DBMS 1

1. MariaDB， MySQL， Oracle DB等