其实我在想要不要写一个数据库的复习，毕竟最近临近期末，各种考试又到来，数据库好像还是相对来说比较重要的一个技术，那就顺便来讲讲吧，主要以学校为主，以MySQL作为我们的讲解内容。

# 数据库介绍

什么是数据库？先从这个问题开始，顾名思义，数据库（Database）就是按照数据结构来存储，组织和管理数据的仓库。

可以类比我们的图书馆，里面有很多的书架，每个书架上面放着不同的书，每本书又都有自己的书名，作者，页码等等的信息。

在数据库中，书架就就相当于表，每本书就是表当中的一行数据，而书的名字，作者这些信息就相当于表当中的列。这样，当你想要寻找信息的时候，就可以做到快速的在数据库当中查找，而不用每个书架都找一遍。

数据库的好处就在于可以方便的存储，查找和管理**大量的信息**。

而我们今天所要谈到的数据库属于数据库当中的**关系型数据库**（*Relational Database*），还有另外一种称为**非关系型数据库**，这种我们以后再谈。

那么什么是关系型数据库呢？

## 关系型数据库

关系型数据库是一种特定类型的数据库，它的结构就像是一个由表格组成的巨大Excel电子表格。这些表格中的每一行都代表数据库中的一个记录，而每一列则代表这个记录的不同属性或信息。

比方说，我们可以有一个表格存储学生信息，表格的列可以包括学生的姓名、年龄、性别等信息，每一行则是一个具体的学生记录。

关系型数据库之所以叫“关系型”，是因为不同表格之间可以建立关系。比如，我们可以有一个表格存储学生的成绩，这个表格中可能有学生的ID（在学生信息表格中也有的），成绩等信息。通过学生的ID，我们就可以在两个表格之间建立关系，将学生的信息和成绩联系起来。

这种结构的好处在于它能够更好地组织和管理大量的数据。而且，如果需要更新或者修改数据，只需要在相应的表格中进行，而不需要修改整个数据库。

##  关系型数据库的特点

1. **表结构：** 数据以表格的形式组织，每个表格包含多个列，每列定义了特定的数据类型。表格中的每一行表示一个记录，也就是实际的数据项。
2. **关系：** 表格之间可以建立关系。这种关系使得不同表格中的数据可以相互关联，从而提供更丰富、更全面的信息。关系通常通过主键和外键来建立。
3. **ACID 属性：** 关系型数据库遵循ACID（原子性、一致性、隔离性、持久性）属性，确保在数据库发生故障或者其他问题时，数据保持一致性和完整性。
4. **事务管理：** 关系型数据库支持事务处理，这意味着可以将一系列数据库操作作为一个原子单元执行。如果其中任何一个操作失败，整个事务会被回滚，以保持数据库的一致性。
5. **数据完整性：** 数据库提供了丰富的约束条件，如主键、唯一键、外键等，以确保数据的完整性。这意味着数据库中的数据符合预定义的规则，不会出现不一致或错误的数据。
6. **SQL 查询语言：** 关系型数据库使用结构化查询语言（SQL）进行数据查询和操作。SQL提供了一种简单而强大的语法，用于从数据库中检索、更新和删除数据，以及定义和管理数据库结构。
7. **可伸缩性：** 关系型数据库系统通常具有良好的可伸缩性，可以处理大量数据和复杂的查询。这使得它们适用于大型企业和复杂的数据管理需求。
8. **备份与恢复：** 关系型数据库提供了备份和恢复机制，确保在系统故障或其他灾难性事件发生时，可以迅速恢复数据。

## 数据库管理系统（Database Management System）

这里我们又要引出另外一个很相关的概念，就是数据库管理系统，什么是数据库管理系统？它跟数据库有什么关系？

**数据库（Database）**和**数据库管理系统（Database Management System，简称DBMS）**是两个密切相关但又不同的概念。

1. **数据库（Database）：** 数据库是一个组织数据的集合，它可以包含各种各样的信息，并按照一定的结构存储。数据库的目的是为了方便数据的存储、检索、更新和管理。数据库中的数据以表格的形式组织，每个表格包含多个列，每列定义了特定的数据类型，而每一行则代表一个记录。
2. **数据库管理系统（DBMS）：** 数据库管理系统是一个软件，它提供了对数据库进行管理和操作的工具和接口。DBMS充当了数据库和应用程序之间的桥梁，负责处理数据库的创建、维护、查询、更新和删除等操作。DBMS负责管理数据库的物理和逻辑结构，实施安全性控制，确保数据的一致性和完整性，以及提供对数据库的并发访问控制。

简而言之，数据库是一个存储数据的仓库，而数据库管理系统是负责管理这个仓库的系统。DBMS通过提供一组功能和接口，使得用户和应用程序能够方便地与数据库交互，而不必关心底层数据库的细节。数据库管理系统还负责处理并发访问、故障恢复、安全性等方面的任务，以确保数据库的可靠性和效率。

举例来说，关系型数据库（如MySQL、Oracle、SQL Server）是一种数据库类型，而针对这种数据库类型的管理系统（MySQL Server、Oracle Database Server、SQL Server）则是具体的数据库管理系统。不同类型的数据库可能需要不同类型的DBMS来进行管理。

懂吧，我们所学的MySQL本质上是一种数据库管理系统，并且是**关系型数据库管理系统**（*Relational Database Management System*，简称RDBMS）。

# RDBMS当中的术语

在学习MySQL也就是关系型数据库之前，我们需要了解一些必须的术语，这些也适用于其他RDBMS。

1. **数据库（Database）：** 就像一个超级大文件夹，可以存储很多很多的数据。
2. **表（Table）：** 就像一张电子表格，有很多行和列，每一行代表一条记录，每一列代表一种信息。
3. **记录（Record）：** 表格中的一行，包含了某个人、物或事件的所有相关信息。
4. **字段（Field）：** 表格中的一列，表示一种特定类型的信息，比如姓名、年龄或者成绩。
5. **主键（Primary Key）：** 就像是一个独一无二的ID，确保每一条记录都有一个唯一的标识符。
6. **外键（Foreign Key）：** 是一个特殊的字段，连接不同表格的关系，就像是表格之间的桥梁。
7. **查询（Query）：** 就像是在数据库中提出的问题，要求获取符合条件的信息。
8. **SQL（Structured Query Language）：** 是一种用于和数据库交流的语言，就像是和数据库对话的方式。
9. **插入（Insert）：** 就是往表格里新增一条记录，就像是在表格中填写新的一行。
10. **更新（Update）：** 就是修改表格中已有记录的信息，就像是更正之前填写的错误。
11. **删除（Delete）：** 就是把表格中的某一行记录移除，就像是把一行数据擦掉。
12. **事务（Transaction）：** 就像是进行一系列的操作，要么全部成功，要么全部失败，确保数据的一致性。
13. **备份（Backup）：** 就像是数据库的复制，以防止意外情况导致数据丢失。
14. **恢复（Recovery）：** 就是在出现问题后，通过备份重新获得数据库的正确状态。
15. **冗余（Redundancy）：** 想象一下你有一份电话簿，如果同一个人的信息在不同的页面出现了很多次，这就是冗余。数据库设计时要尽量避免冗余，以免浪费空间和增加混乱。而且虽然冗余降低了性能，但提高了数据的安全性。。
16. **复合键（Composite Key）：** 如果一个表格中的主键不止一个字段，而是由两个或更多字段组成，那么这就是复合键。就像用姓名和生日一起来唯一标识一个人，而不仅仅是一个属性。
17. **索引（Index）：** 想象一本书的目录，索引就是数据库的目录，可以让我们更快地找到需要的信息。它是一种优化方法，提高数据检索的速度。
18. **参照完整性（Referential Integrity）：** 假设你有两个表格，一个存储学生信息，另一个存储课程信息。参照完整性就是确保如果在课程表中有学生的信息，那么这个学生在学生表中一定存在。这样可以防止出现“无根的”数据，确保数据的关联性。

# MySQL介绍

自己百度吧，这个没啥要讲的，而且安装我也就不说了，这个安装都不会装我的建议是当初为什么要学这个专业？

MySQL有一下特点：

1. **开源性（Open Source）：** MySQL 是一个开源的关系型数据库管理系统，这意味着任何人都可以免费使用它，并且可以查看和修改其源代码。这使得它成为许多开发者和企业的首选。
2. **跨平台性（Cross-Platform）：** MySQL 可以在多个操作系统上运行，包括 Windows、Linux、macOS 等，这使得它非常灵活，能够满足不同用户的需求。
3. **高性能（High Performance）：** MySQL 被设计成高性能的数据库系统，能够处理大规模的数据并支持高并发访问。它采用了一些优化技术，例如索引、缓存等，以提高查询和操作的速度。
4. **支持多种存储引擎（Storage Engines）：** MySQL 支持多种存储引擎，例如 InnoDB、MyISAM 等。每个存储引擎都有其特定的优势和用途，可以根据应用的需求选择合适的引擎。
5. **事务支持（Transaction Support）：** MySQL 支持事务处理，确保一系列操作要么全部成功，要么全部失败，以保持数据的一致性。
6. **安全性（Security）：** MySQL 提供了一系列的安全性功能，包括用户认证、访问控制、加密传输等，以确保数据库的数据安全。
7. **备份和恢复（Backup and Recovery）：** MySQL 允许用户进行定期的数据库备份，以防止数据丢失。在发生故障时，可以通过备份进行恢复。
8. **简单的 SQL 语法（Simple SQL Syntax）：** MySQL 使用标准的 SQL 语法，这使得学习和使用 MySQL 相对简单，特别适合初学者。
9. **社区支持（Community Support）：** 由于 MySQL 是开源的，有一个庞大的用户社区，用户可以在社区中分享经验、解决问题，获得及时的支持和帮助。



