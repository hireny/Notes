
# 一、基础

模式定义了数据如何存储、存储什么样的数据以及数据如何分解等信息，数据库和表都有模式。

主键的值不允许修改，也不允许复用（不能将已经删除的主键值赋给新数据行的主键）。

SQL（Structured Query Language）标准 SQL 由 ANSI 标准委员会管理，从而称为 ANSI SQL。各个 DBMS 都有自己的实现，如 PL/SQL、Transact-SQL 等。

SQL 语句不区分大小写，但是数据库表名、列名和值是否区分依赖于具体的 DBMS 以及配置。

### 注释

SQL 支持以下三种注释：

```sql
# 注释
SELECT *
FROM test;	-- 注释
/* 注释1
   注释2 */
```

### 数据库基本操作

各个 DBMS 的数据库操作都不相同，以下主要是 MySQL 的操作

**查看数据库**  

```sql
-- 查看当前所存在的数据库
SHOW DATABASES;
-- 查看当前数据库信息
SHOW CREATE DATABASE test;
```

**创建数据库**  

```sql
-- 创建 test 数据库
CREATE DATABASE test;
-- 使用 test 数据库
USE test;
```

**删除数据库**  

```sql
-- 删除 test 数据库
DROP DATABASE test;
```

# 二、创建和操作表

### 创建表

利用 `CREATE TABLE` 创建表，必须给出下列信息：

- 新表的名字，在关键字 `CREATE TABLE` 之后给出；
- 表列的名字和定义，用逗号分隔；
- 有的 DBMS 还要求指定表的位置。

```sql
CREATE TABLE Orders(
	# 唯一的订单号，不为空
	order_num 	INT NOT NULL,
	# 订单日期，设置默认值为当前时间
	order_date	TIMESTAMP NOT NULL DEFAULT NOW(),
	# 订单顾客ID（关联到Customers表的cust_id）
	cust_id		INT NOT NULL,
	# 设置主键为 order_num
	PRIMARY KEY (`order_inum`),
	# 设置外键（删除 CONSTRAINT orders_cust_id_customers外键名称，则外键名称为数据库自动生成）
	CONSTRAINT orders_cust_id_customers FOREIGN KEY (`cust_id`) references Customers(`cust_id`)
);
```

> 主键是其值唯一标识表中每一行的列。只有不允许NULL值的列可作为主键，允许NULL值的列不能作为唯一标识。
> 

### 查看表

```sql
SHOW TABLES;
```

查看某一张表的具体信息

```sql
SHOW CREATE TABLE Customers;
# 或者
DESC Customers;
```

### 删除表

删除表（删除整个表而不是其内容）非常简单，使用 `DROP TABLE` 语句即可。

```sql
DROP TABLE CustCopy;
```

> 使用关系规则防止意外删除
> 许多 DBMS 允许强制实施有关规则，防止删除与其它表相关联的表。在实施这些规则时，如果某个表发布一条 `DROP TABLE` 语句，且该表是某个关系的组成部分，则 DBMS 将阻止这条语句执行，直到该关系被删除为止。

### 更新表

更新使用 `ALTER TABLE` 语句。以下是使用 `ALTER TABLE` 时需要考虑的事情。

- 理想情况下，不要在表中包含数据时对其进行更新。应该在表的设计过程中充分考虑未来可能的需求，避免今后对表的结构做大改动。
- 所有的 DMBS 都允许给现有的表增加列，不过对所增加列的数据类型（以及`NULL`和`DEFAULT`的使用）有所限制。
- 许多 DBMS 不允许删除或更改表中的列。
- 多数 DMBS 允许重新命名表中的列。
- 许多 DBMS 限制对已经填有数据的列进行更改，对未填有数据的列几乎没有限制。

使用 `ALTER TABLE` 更改表结构，必须给出下面的信息：

- 在 `ALTER TABLE` 之后给出要更改的表名（该表必须存在，否则将出错）；
- 列出要做哪些更改。

添加列

```sql
ALTER TABLE Vendors
ADD vend_phone CHAR(20);
```

删除列

```sql
ALTER TABLE Vendors DROP COLUMN vend_phone;
```

复杂的表结构更改一般需要手动删除过程，它涉及以下步骤：

1. 用新的列布局创建一个新表；
2. 使用 `INSERT SELECT` 语句从旧表复制数据到新表。有必要的话，可以使用转换函数和计算字段；
3. 检验包含所需数据的新表；
4. 重命名旧表（如果确定，可以删除它）；
5. 用旧表原来的名字重命名新表；
6. 根据需要，重新创建触发器、存储过程、索引和外键。

> 警告：小心使用 ALTER TABLE
> 使用 ALTER TABLE 要极为小心，应该在进行改动前做完整的备份（模式和数据的备份）。数据库表的更改不能撤销，如果增加了不需要的列。也许无法删除它们。类似地，如果删除了不应该删除的列，可能会丢失列中的所有数据。
> 

### 重命名表

```sql
# 第一种重命名方式
ALTER TABLE Customers RENAME TO NewCustomers;
# 第二种重命名方式
RENAME TABLE NewCustomers TO Customers;
```

# 三、插入数据

`INSERT` 用来将行插入（或添加）到数据库表。插入有几种方式：

- 插入完整的行；
- 插入行的一部分；
- 插入某些查询的结果。

> 提示：插入及系统安全
> 使用 `INSERT` 语句可能需要客户端/服务器DBMS中的特定安全权限。在你试图使用`INSERT`前，应该保证自己有足够的安全权限。
>

### 插入完整的行

```sql
INSERT INTO Customers
VALUES ('1000000006',
		'Toy Land',
		'123 Any Street',
		'New York',
		'NY',
		'1111',
		'USA',
		NULL,
		NULL);
```

这种语法很简单，但并不安全，应该尽量避免使用。上面的 SQL 语句高度依赖于表中的列的定义次序，还依赖于其容易获得的次序信息。即使可以得到这种次序信息，也不能保证各列在下一次表结果变动后保持完全相同的次序。因此，编写依赖于特定次序的 SQL 语句是很不安全的，这样做迟早会出问题。


### 插入行的一部分

```sql
INSERT INTO Customers(cust_id,
						cust_name,
						cust_address,
						cust_city,
						cust_state,
						cust_zip,
						cust_country)
VALUES ('1000000006',
		'Toy Land',
		'123 Any Street',
		'New York',
		'NY',
		'1111',
		'USA',
		NULL,
		NULL);
```

> 省略列
> 如果表的定义允许，则可以在 `INSERT` 操作中省略某些列。省略的列必须满足以下某个条件。
>
> - 该列定义为允许 NULL 值（无值或空值）。
>
> - 在表定义中给出默认值。这表示如果不给出值，将使用默认值。
>
> 如果对表中不允许NULL值且没有默认值的列不给出值， DMBS 将产生错误信息，并且响应的行插入不成功。

### 插入检索出的数据

`INSERT` 语句可以将 `SELECT` 语句查询的结果插入表中，这就是所谓的 `INSERT SELECT`。

加入想把另一表中的顾客列合并到 Customers 表中。不需要每次读取一行再将它用 `INSERT` 插入，可以如下进行：

```sql
INSERT INTO Customers(cust_id,
						cust_contact,
						cust_email,
						cust_name,
						cust_address,
						cust_city,
						cust_state,
						cust_zip,
						cust_country)
SELECT cust_id,
		cust_contact,
		cust_email,
		cust_name,
		cust_address,
		cust_city,
		cust_state,
		cust_zip,
		cust_country
FROM CustNew;
```

`INSERT SELECT` 中 `SELECT` 语句可以包含 `WHERE` 子句，以过滤插入的数据。

### 从一个表复制到令一个表

有一种数据插入不使用 `INSERT` 语句。要将一个表的内容复制到一个全新的表（运行中创建的表），可以使用 `SELECT INTO` 语句。

创建 CustCopy 新表，并把 Customers 表的整个内容复制到新表中。想要只复制部分的列，可以明确给出列名，而不是使用 * 通配符

```sql
CREATE TABLE CustCopy AS
SELECT * FROM Customers
```

在使用 `SELECT INTO` 时，需要知道一些事情：
- 任何 `SELECT` 选项和子句都可以使用，包括 `WHERE` 和 `GROUP BY`；
- 可利用连接从多个表插入数据；
- 不管从多少个表中检索数据，数据都只能插入到一个表中。


# 四、更新与删除

### 更新数据

更新（修改）表中的数据，可以使用 `UPDATE` 语句。有两种使用 `UPDATE` 的方式：

- 更新表中的特定行；
- 更新表中的所有行。

基本的 `UPDATE` 语句由三部分组成，分别是：

- 要更新的表；
- 列名和它们的新值；
- 确定要更新哪些行的过滤条件。

```sql
# 更新单个列
UPDATE Customers
SET cust_email = 'kim@thetoystore.com'
WHERE cust_id = '1000000005';

# 更新多个列
UPDATE Customers
SET cust_contact = 'Sam Roberts',
	cust_email = 'sam@toyland.com'
WHERE cust_id = '1000000005';

# 要删除某个列的值，可设置它为 NULL（假如表定义允许NULL值）
UPDATE Customers
SET cust_email = NULL
WHERE cust_id = '1000000005';
```

没有 `WHERE` 子句，DBMS 将会更新 `Customers` 表中的所有行。

### 删除数据

从一个表中删除（去掉）数据，使用 `DELETE` 语句。有两种使用 `DELETE` 的方式：

- 从表中删除特定的行；
- 从表中删除所有行。

> 在使用 `DELETE` 时一定要细心。因为稍不注意，就会错误地删除表中所有行。

```sql
# 删除某一行
DELETE FROM Customers
WHERE cust_id = '1000000006';
```

`TRUNCATE TABLE` 可以清空表，也就是删除所有行。

```sql
TRUNCATE TABLE Customers;
```

### 更新与删除原则

使用 `UPDATE` 或 `DELETE` 时所遵循的重要原则：

- 除非确实打算更新和删除每一行，否则绝对不要使用不带 `WHERE` 子句的 `UPDATE` 或 `DELETE` 语句。
- 保证每个表都有主键，尽可能向 `WHERE` 子句那样使用它（可以指定各主键、多个值或值的范围）。
- 在 `UPDATE` 或 `DELETE` 语句使用 `WHERE` 子句前，应该先用 `SELECT` 进行测试，保证它过滤是正确的记录，以防编写的 `WHERE` 子句不正确。
- 使用强制实施引用完整性的数据库，这样DBMS将不允许删除其数据域其它表相关联的行。
- 有的DBMS允许数据库管理员施加约束，防止执行不带 `WHERE` 子句的 `UPDATE` 或 `DELETE` 语句。如果所采用的DBMS支持这个特性，应该使用它。

使用更新和删除操作时一定要用 `WHERE` 子句，不然会把整张表的数据都破坏。可以先用 `SELECT` 语句进行测试，防止错误删除

# 五、查询

### 查询语句

```sql
# 查询指定列
SELECT col, col1 FROM test;
# 查询所有列
SELECT * FROM test;
```

> 警告：一般而言，除非你确实需要表中的每一列，否则最好别使用 * 通配符。虽然使用通配符能让你自己省事，不用明确列出所需列，但检索不需要的列通常会降低检索和应用程序的性能。
> 

### DISTINCT

指示数据库只返回不同的值，作用于所有的列，也就是说所有列的值都相同才算相同。

```sql
SELECT DISTINCT col1, col2 FROM test;
```

### LIMIT

限制返回的行数。可以有两个参数，第一个参数为起始行，从 0 开始；第二个参数为返回的总行数。

返回前 5 行：

```sql
SELECT * FROM test LIMIT 5;
或者
SELECT * FROM test LIMIT 0, 5;
```

返回第 3 ~ 5 行：

```sql
SELECT * FROM test LIMIT 2, 3;
或者
SELECT * FROM test LIMIT 3 OFFSET 2;
```

> 警告：第一个被检索的行是第0行，而不是第1行。因此，LIMIT 1 OFFSET 1 会检索第2行，而不是第1行。
> 

# 六、排序

使用 `ORDER BY` 子句对输出进行排序

- `ASC`：升序（默认）
- `DESC`：降序

可以按多个列进行排序，并且为每个列指定不同的排序方式：

```sql
SELECT * FROM test ORDER BY col1 DESC, col2 ASC;
```

# 七、过滤

数据库表一般包含大量的数据，很少需要所有数据。通常需要指定搜索条件（或过滤条件）来获取表数据的子集。只检索所需数据需要指定搜索条件（search criteria），搜索条件也称为过滤条件（filter condition）。

```sql
SELECT * FROM test
WHERE col IS NULL;
```

> SQL过滤与应用过滤 <br>
> 数据也可在应用层过滤，但这样做法不妥。因为这会导致服务器通过网络发送多余的数据，从而导致网络带宽的浪费。因此尽量使用 SQL 语句过滤不必要的数据，而不是传输所有数据到客户端中进行过滤。

下标显示了 `WHERE` 子句可用的操作符

|操作符|说明|
|:---:|:---:|
|=|等于|
|<|小于|
|>|大于|
|<> 或 !=|不等于|
|<= 或 !>|小于等于|
|>= 或 !<|大于等于|
|BETWEEN|在两个值之间|
|IS NULL|为NULL值|

> 操作符兼容 <br>
> 并非所有 DBMS 都支持这些操作符。例如：Microsoft Access 支持 `<>` 而不支持 `!=`。

> NULL <br> 
> 无值（no value），它与 0、空字符串或仅包含空格不同。

`AND` 和 `OR` 用于连接多个过滤条件。优先处理 `AND`，当一个过滤表达式涉及到多个 `AND` 和 `OR` 时，可以使用 `()` 来决定优先级，使得优先级关系更清晰。

`IN` 操作符用于匹配一组值，其后也可以接一个 `SELECT` 子句，从而匹配子查询得到的一组值。

`NOT` 操作符用于否定一个条件。

# 八、通配符

通配符也是用在过滤语句中，但它只能用于文本字段（串），非文本数据类型字段不能使用通配符搜索。

- `%` 匹配 `>=0` 个任意字段；
- `_` 匹配 `==1` 个任意字段；
- `[]` 可以匹配集合内的字符，例如 `[ab]` 将匹配字符 a 或者 b。用脱字符 `^` 可以对其进行否定，也就是不匹配集合内的字符。

使用 `LIKE` 来进行通配符匹配

```sql
SELECT * FROM test
WHERE col LIKE `[^AB]%`;	-- 不以 A 和 B 开头的任意文本
```

SQL 的通配符很有用。但这种功能是由代价的，即通配符搜索一般比其它搜索要耗费更长的处理时间。下述为使用通配符的一些技巧：

- 不要过度使用通配符。如果其它操作符能达到相同的目的，应该使用其它操作符。
- 在确实需要使用通配符时，也尽量不要把它们用在搜索功能的开始处。把通配符置于开始处，搜索起来是最慢的。
- 仔细注意通配符的位置。如果放错地方，可能不会反回想要的数据。


# 九、计算字段

在数据库服务器上完成数据的转换和格式化的工作往往比客户端上快得多，并且转换和格式化后的数据量更少的话可以减少网络通信量。

计算字段通常需要使用 `AS` 来取别名，否则输出的时候字段名为计算表达式。

```sql
SELECT col1 * col2 AS alias
FROM test;
```

`CONCAT()` 用于连接两个字段。许多数据库会使用空格把一个值填充为列宽，因此连接的结果会出现一些不必要的空格，使用 `TRIM()` 可以去除首尾空格。

```sql
SELECT CONCAT(TRIM(col1), '(', TRIM(col2), ')') AS concat_col
FROM test;
```

# 十、函数

各个 DBMS 的函数都是不相同的，因此不可移植；以下主要是 MySQL 的函数。

### 聚集函数

**聚集函数（aggregate function）**对某些行运行的函数，计算并返回一个值。

|函数|说明|
|:---:|:---:|
|`AVG()`|返回某列的平均值|
|`COUNT()`|返回某列的行数|
|`MAX()`|返回某列的最大值|
|`MIN()`|返回某列的最小值|
|`SUM()`|返回某列值之和|

`AVG()` 只能用来确定特定数值列的平均值，而且列名必须作为函数参数给出。为了获得多个列的平均值，必须使用多个 `AVG()` 函数。

`AVG()` 会忽略值为`NULL` 的行。

计算行的数目，`COUNT(column)` 忽略该列中值为空的行，但如果 `COUNT(*)`  则不忽略 `NULL` 值。

`MAX()` 与 `MIN()` 函数忽略列值为 `NULL` 的行。

`SUM()` 忽略列为 `NULL` 的行。

以上 5 个聚集函数都可以如下使用：

- 对所有行执行计算，指定 ALL 参数或不指定参数（因为 ALL 是默认行为）。
- 只包含不同的值，指定 `DISTINCT` 参数。

使用 `DISTINCT` 可以聚集不同的值，`DISTINCT` 必须使用列名，`DISTINCT` 不能用于 `COUNT(*)`。

```sql
SELECT AVG(DISTINCT col1) AS avg_col
FROM test
WHERE id = 'DLL01';
```

### 文本处理

|函数|说明|
|:---:|:---:|
|`LEFT()`|返回字符串左边的字符|
|`RIGHT()`|返回字符串右边的字符|
|`LOWER()`|将字符串转换为小写|
|`UPPER()`|将字符串转换为大写|
|`LTRIM()`|去除字符串左边的空格|
|`RTRIM()`|去除字符串右边的空格|
|`LENGTH()`|返回字符串的长度|
|`SOUNDEX()`|返回字符串的SOUNDEX值|

其中，`SOUNDEX()` 是一个将任何文本字符串转换为描述其语言表示的字母数字模式的算法。

```sql
SELECT * FROM test
WHERE SOUNDEX(col1) = SOUNDEX('apple');
```

### 日期和时间处理

- 日期格式：YYYY-MM-DD
- 时间格式：HH:MM:SS

|函数|说明|
|:---:|:---:|
|`ADDDATE()`|增加一个日期（天、周等）|
|`ADDTIME()`|增加一个时间（时、分等）|
|`CURDATE()`|返回当前日期|
|`CURTIME()`|返回当前时间|
|`DATE()`|返回日期时间的日期部分|
|`DATEDIFF()`|计算两个日期之差|
|`DATE_ADD()`|高度灵活的日期运算函数|
|`DATE_FORMAT()`|返回一个格式化的日期或时间串|
|`DAY()`|返回一个日期的天数部分|
|`DAYOFWEEK()`|对于一个日期，返回对应的星期几|
|`HOUR()`|返回一个时间的小时部分|
|`MINUTE()`|返回一个时间的分钟部分|
|`MONTH()`|返回一个日期的月份部分|
|`NOW()`|返回当前日期和时间|
|`SECOND()`|返回一个时间的秒部分|
|`TIME()`|返回一个日期时间的时间部分|
|`YEAR()`|返回一个日期的年份部分|

```sql
# 案例1
SELECT NOW();
# 案例2
SELECT col
FROM test
WHERE YEAR(col3) = 2020
```


### 数值处理

数值处理函数仅处理数值数据。这些函数一般主要用于代数、三角或几何运算。

|函数|说明|
|:---:|:---:|
|`SIN()`|正弦|
|`COS()`|余弦|
|`TAN()`|正切|
|`ABS()`|绝对值|
|`SQRT()`|平方根|
|`MOD()`|余数|
|`EXP()`|指数|
|`PI()`|圆周率|
|`RAND()`|随机数|


# 十一、分组

把具有相同的数据值的行放在同一组中。

可以把同一分组数据使用聚集函数进行处理，例如求分组数据的平均值等。

指数的分组字段除了能按该字段进行分组，也会自动按该字段进行排序。

```sql
SELECT col, COUNT(*) AS num 
FROM test
GROUP BY col;
```

`GROUP BY` 自动按分组字段进行排序，`ORDER BY` 也可以按聚集字段来进行排序。

```sql
SELECT col, COUNT(*) AS num
FROM test
GROUP BY col
ORDER BY num;
```

`WHERE` 过滤行， `HAVING` 过滤分组，行过滤应当先于分组过滤。

```sql
SELECT col, COUNT(*) AS num
FROM test
WHERE col > 2
GROUP BY col
HAVING num >= 2;
```

`HAVING` 和 `WHERE` 的区别：`WHERE`在数据分组前进行过滤，`HAVING` 在数据分组后进行过滤。`WHERE`排除的行不包括在分组中。这可能会改变计算值，从而影响 `HAVING` 子句中基于这些值过滤掉的分组。


分组规定：

- `GROUP BY` 子句出现在 `WHERE` 子句之后，`ORDER BY` 子句之前；
- 除了聚集字段外，`SELECT` 语句中的每一字段都必须在 `GROUP BY` 子句中给出；
- `NULL` 的行会单独分为一组；
- 大多数 SQL 实现不支持 `GROUP BY` 列具有可变长度的数据类型。


### SELECT 子句顺序

|子句|说明|是否必须使用|
|:---:|:---:|:---:|
|`SELECT`|要返回的列或者表达式|是|
|`DISTINCT`|去重所需字段|否|
|`FROM`|从中检索数据的表|仅在从表里选择数据时使用|
|`WHERE`|行级过滤（过滤后的记录不在包括在分组中）|否|
|`GROUP BY`|分组说明（后面不能是聚合函数）|仅在按组计算聚集时使用|
|`HAVING`|组间过滤（用在 `GROUP BY` 之后，相当于分组后的`WHERE`，但是它是针对分组后的数组）|否|
|`ORDER BY`|设置输出排序顺序，默认升序`ASC`，降序`DESC`|否|
|`LIMIT`|要检索的函数|否|

执行顺序

`FROM` -> `WHERE` -> `GROUP BY` -> `(SELECT)` -> `HAVING` -> `SELECT` -> `ORDER BY` -> `LIMIT`


# 十二、子查询

子查询的 `SELECT` 语句只能查询单个列。企图检索多个列将返回错误。

可以将子查询的结果作为 `WHERE` 语句的过滤条件：

```sql
SELECT * 
FROM test1
WHERE col1 IN (SELECT col2 FROM test2);
```

下面的语句可以检索出客户的订单数量，子查询语句会对第一个查询检索出的每个客户执行一次：

```sql
SELECT cust_name, (SELECT COUNT(*)
                   FROM Orders
                   WHERE Orders.cust_id = Customers.cust_id)
                   AS orders
FROM Customers
ORDER BY cust_name;
```

# 十三、连接

使用 `JOIN` 关键字，用于连接多个表，并且条件语句使用 `ON` 而不是 `WHERE` 。

连接可以替换子查询，并且比子查询的效率一般会更快。

可以用 `AS` 给列名、计算字段和表名取别名，给表名取别名是为了简化 `SQL` 语句以及连接相同表。

### 内连接

内连接又称等值连接，使用 `INNER JOIN` 关键字。

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors INNER JOIN Products
ON Vendors.vend_id = Products.vend_id;
```

可以不明确使用 `INNER JOIN` ，而使用普通查询并在 `WHERE` 中将两个表中要连接的列用等值方法连接起来。

```sql
SELECT vend_name, prod_name, prod_price
FROM Vendors, Products
WHERE Vendors.vend_id = Products.vend_id;
```

> 笛卡尔积（cartesian product）
> 由没有连接条件的表关系返回的结果为笛卡尔积。检索出的行的数目将是第一个表中的行数乘以第二个表中的行数。
> 有时，返回笛卡尔积的连接，也称为叉连接（cross join）。
> 

连接多个表

```sql
SELECT prod_name, vend_name, prod_price, quantity
FROM OrderItems, Products, Vendors
WHERE Products.vend_id = Vendors.vend_id
AND OrderItems.prod_id = Products.prod_id
AND order_num = 20007;
```

### 自连接(self-join)

自连接就是当前表与自身的连接查询，关键点在于虚拟化出一张表给一个别名。

假如要给与 Jim Jones 同一公司的所有顾客发送一封信件。这个查询要求首先找出 Jim Jones 工作的公司，然后找出在该公司工作的顾客。

子查询版本

```sql
SELECT cust_id, cust_name, cust_contact
FROM Customers
WHERE cust_name = (SELECT cust_name
					FROM CUstomers
					WHERE cust_contact
					'Jim Jones');
```

自连接版本

```sql
SELECT c1.cust_id, c1.cust_name, c1.cust_contact
FROM Customers AS c1, Customers AS c2
WHERE c1.cust_name = c2.cust_name
AND c2.cust_contact = 'Jim Jones';
```

自连接通常作为外部语句，用来替代从相同表中检索数据的使用子查询语句。虽然最终结果相同，但许多 DBMS 处理连接远比处理子查询要快。

### 自然连接(natural join)

自然连接是把同名列通过等值测试连接起来的，同名列可以有多个。自然连接排除多次出现，使每一列只返回一次。

内连接和自然连接的区别：内连接提供连接的列，而自然连接自动连接所有同名列。

```sql
SELECT C.*, O.order_name, O.order_date,
	OI.prod_id, OI.quantity, OI.item_price
FROM Customers AS C, Orders AS O, OrderItems AS OI
WHERE C.cust_id = O.cust_id
AND OI.order_num = O.order_num
AND OI.prod_id = 'RGAN01';
```

### 外连接(outer join)

外连接将一个表中的行与另一个表中的行相关联，但有时需保留没有关联的哪些行。分为左外连接(`LEFT OUTER JOIN`)，右外连接(`RIGHT OUTER JOIN`)以及全外连接(`FULL OUTER JOIN`)，左外连接就是保留左表没有关联的行。

检索所有顾客的订单信息，包括还没有订单信息的顾客。

```sql
SELECT Customers.cust_id, Orders.order_num
FROM Customers LEFT OUTER JOIN Orders
ON Customers.cust_id = Orders.cust_id;
```

# 十四、组合查询

SQL允许执行多个查询（多条`SELECT`语句），并将结果作为一个查询结果集返回。这些组合查询通常称为并（union）或复合查询（compound query）。

使用 `UNION` 来组合两个查询，如果第一个查询返回 M 行，第二个查询返回 N 行，那么组合查询的结果一般为 M+N 行。

```sql
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_state IN ('IL', 'IN', 'MI')
UNION
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_name = 'Fun4All';
```

`UNION` 非常容易使用，但在进行组合时需要注意几条规则：

- `UNION` 必须由两条或两条以上的 `SELECT` 语句组成，语句之间用关键字 `UNION` 分隔。
- 每个查询必须包含相同的列、表达式和聚集函数。
- 列数据类型必须兼容：类型不必完全相同，但必须是 DBMS 可以隐含转换的类型（例如，不同的数值类型或不同的日期类型）。

`UNION` 会默认去除相同行，如果需要保留相同行，使用 `UNION ALL`。

`SELECT`语句的输出用 `ORDER BY` 子句排序。在使用 `UNION` 组合查询时，只能使用一条 `ORDER BY` 子句，并且必须位于最后一条`SELECT`语句的之后。

```sql
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_state IN ('IL', 'IN', 'MI')
UNION
SELECT cust_name, cust_contact, cust_email
FROM Customers
WHERE cust_name = 'Fun4All'
ORDER BY cust_name, cust_contact;
```

# 十五、视图

视图是虚拟的表，与包含数据的表不一样，视图只包含使用时动态检索数据的查询。

对视图的操作和对普通表的操作一样。

视图具有如下好处：

- 重用 SQL 语句。
- 简化复杂的 SQL 操作，比如复杂的连接；
- 只使用实际表的一部分数据而不是整个表；
- 保护数据。可以授予用户访问视图的权限，保证数据的安全性；
- 更改数据格式和表示。视图可返回与底层表的表示和格式不同的数据。

### 创建视图

视图用 `CREATE VIEW` 语句来创建。可以使用 `SHOW CREATE VIEW viewname;` 来查询创建视图的语句。

```sql
CREATE VIEW ProductCustomers AS 
SELECT cust_name, cust_contact, prod_id
FROM Customers, Orders, OrderItems
WHERE Customers.cust_id = Orders.cust_id
AND OrderItems.order_num = Orders.order_num;
```

使用下列语句来查看一下创建的试图：

```sql
SELECT * 
FROM ProductCustomers;
```

用来检索视图中产品RGAN01的客户信息：

```sql
SELECT cust_name, cust_contact
FROM ProductCustomers
WHERE prod_id = 'RGAN01';
```

### 用视图重新格式化检索出的数据

```sql
SELECT RTRIM(vend_name) + '(' + RTRIM(vend_country) + ')' AS vend_title
FROM Vendors
Order BY vend_name;
```

下面是相同的语句，但使用了 || 语法：

```sql
SELECT RTRIM(vend_name) || '(' + RTRIM(vend_country) || ')' AS vend_title
FROM Vendors
Order BY vend_name;
```

假设经常需要上述的格式，我们不必每次都执行这种拼接，而是创建一个视图，使用它即可。

```sql
CREATE VIEW VendorLocations AS
SELECT RTRIM(vend_name) + '(' + RTRIM(vend_country) + ')' AS vend_title
FROM Vendors
```

下面是使用 || 语法的相同语句：

```sql
CREATE VIEW VendorLocations AS
SELECT RTRIM(vend_name) || '(' + RTRIM(vend_country) || ')' AS vend_title
FROM Vendors
```

### 用视图过滤不想要的数据

视图对于应用于普通的 `WHERE` 子句也很有用。例如，可以定义 `CustomerEMailList` 视图，过滤没有电子邮件地址的顾客。

```sql
CREATE VIEW CustomerEMailList AS
SELECT cust_id, cust_name, cust_email
FROM Customers
WHERE cust_email IS NOT NULL;
```

### 使用视图与计算字段

在简化计算字段的使用上，视图也特别有用。下述语句是检索某个订单中的物品，计算每种物品的总价格：

```sql
SELECT prod_id,
		quantity,
		item_price,
		quantity*item_price AS expanded_price
FROM OrderItems
WHERE order_num = 20008;
```

将其转换为一个视图，如下所示：

```sql
CREATE VIEW OrderItemsExpanded AS
SELECT order_num,
		prod_id,
		quantity,
		item_price,
		quantity*item_price AS expanded_price
FROM OrderItems;
```


### 删除视图

使用 `DROP` 删除视图，其语法为 `DROP VIEW viewname;` ，下列语句将删除视图 ProductCustomers：

```sql
DROP VIEW ProductCustomers;
```

### 更新视图

更新视图时，可以先用 `DROP` 删除视图，再用 `CREATE` 创建视图。也可以直接用 `CREATE OR REPLACE VIEW`。如果要更新的视图不存在则会创建一个视图，若存在则会替换原来的视图。


# 十六、存储过程

存储过程保存一条或多条 SQL 语句。可将其视为批处理。

使用存储过程的理由：

- SQL语句封装，从而简化复杂的操作；
- 代码复用，保证数据的一致性；
- 简化对变动的管理。如果表、列或业务逻辑有变化，只需要更改存储过程的代码，使得调动存储过程的人不需要知道这些变化，保证了一定的安全性；
- 由于是预先编译，因此具有很高的性能。
- 存在一些职能用在单个请求中的 SQL 元素和特性，存储过程可以使用它们来编写功能更强更灵活的代码。

### 创建存储过程

命令行中创建存储过程需要自定义分隔符，因为命令行是以 ; 为结束符，而存储过程中也包含了分号，因此会错误把这部分分号当成是结束符，造成语法错误。

包含 `in`、`out` 和 `inout` 三种参数。

给变量赋值都需要用 `SELECT INTO` 语句。

每次只能给一个变量赋值，不支持集合的操作。

```sql
DELIMITER //

	CREATE PROCEDURE Proc(out ret INT)
		BEGIN
			SELECT COUNT(*)
			FROM CUstomers
			WHERE NOT cust_email IS NULL
			INTO ret;
		END //
		
DELIMITER ;
```

`DELIMITER //` 与存储语句无关。`DELIMITER` 语句将分隔符 `;` 更改为 `//`。存储过程作为整体进行解释，在 `END` 关键字之后使用 `//` 来指示存储过程结束。最后一个命令 `DELIMITER ;` 用来将分隔符改回`;`。

### 执行存储过程

```sql
CALL Proc(@ret);
SELECT @ret;
```

### 查看存储过程

```sql
SHOW CREATE PROCEDURE Proc;
```

### 删除存储过程

```sql
DROP PROCEDURE Proc;
```


# 十七、游标

在存储过程中使用游标可以对一个结果集进行移动遍历。

游标常见的选项和特性：

- 能够标记游标为只读，使数据能读取，但不能更新和删除。
- 能控制可以执行的定向操作（向前、向后、第一、最后、绝对位置、相对位置等）。
- 能标记某些列为可编辑的，某些列为不可编辑的。
- 规定范围，使游标对创建它的特定请求（如存储过程）或对所有请求可访问。
- 指示DBMS对检索出的数据（而不是指出表中活动数据）进行复制，使数据在游标打开和访问期间不变化。

游标主要用于交互式应用，其中用户需要对数据集中的任意行进行浏览和修改。

使用游标的四个步骤：

1. 声明游标，这个过程没有实际检索出数据，它只是定义要使用的 `SELECT` 语句和游标选项；
2. 打开游标。这个过程用前面定义的 `SELECT` 语句把数据实际检索出来。；
3. 取出数据；
4. 关闭游标。

```sql
DELIMITER //

CREATE PROCEDURE Proc(OUT ret INT)
    BEGIN
        DECLARE done boolean default 0;
		-- 声明游标
        declare mc cursor for
        SELECT cust_name FROM Customers;
        -- 定义了一个 continue handler，当 sqlstate '02000' 这个条件出现时，会执行 set done = 1
        declare continue handler for sqlstate '02000' set done = 1;
		-- 打开游标
        open mc;

        repeat
        	-- 获取结果
            fetch mc into ret;
            -- 这里是为了显示获取结果
            select ret;
        until done end repeat;
		-- 关闭游标
        close mc;
    END //
DELIMITER ;
```

# 十八、触发器

触发器是特殊的存储过程，它会在某个表执行以下语句时而自动执行：`DELETE`、`INSERT`、`UPDATE`。

与存储过程不一样（存储过程只是简单的存储SQL语句），触发器与单个表相关联。

触发器内的代码具有以下数据的访问权：

- `INSERT` 操作中的所有数据；
- `UPDATE` 操作中的所有新数据和旧数据；
- `DELETE` 操作中删除的数据。

触发器必须指定在语句执行之前还是之后自动执行，之前执行使用 `BEFORE` 关键字，之后执行使用 `AFTER` 关键字。`BEFORE` 用于数据验证和净化，`AFTER` 用于审计跟踪，将修改记录到另外一张表中。

常见用途：

- 保证数据一致。例如，在 `INSERT` 或 `UPDATE` 操作中将所有州名转换为大写。
- 基于某个表的变动在其它表上执行活动。例如，每当更新或删除一行时将审计跟踪记录写入某个日志表。
- 进行额外的验证并根据需要回退数据。例如，保证某个顾客的可用资金不超限定，如果已经超出，则阻塞插入。
- 计算计算列的值或更新时间戳。

`INSERT` 触发器包含一个名为 `NEW` 的虚拟表。

```sql
CREATE TRIGGER customer_state AFTER INSERT ON Customers
FOR EACH ROW SELECT NEW.customer_state into @result;

SELECT @result; -- 获取结果
```

`DELETE` 触发器包含一个名为 OLD 的虚拟表，并且是只读的。

`UPDATE` 触发器包含一个名为 NEW 和一个名为 OLD 的虚拟表，其中 NEW 是可以被修改的，而 OLD 是只读的。

MySQL 不允许在触发器中使用 `CALL` 语句，也就是不能调用存储过程。

# 十九、约束

约束（constraint）：管理如何插入或处理数据库数据的规则。

### 主键

主键是一种特殊的约束，用来保证一列（或一组列）中的值是唯一的，而且永不改动。没有主键，要安全地 `UPDATE` 或 `DELETE` 特定行二不影响其它行会非常困难。

表中任意列只要满足以下条件，都可以用于主键：

- 任意两行的主键值都不相同。
- 每行都具有一个主键值（即列中不允许NULL值）。
- 包含主键值的列从不修改或更新。
- 主键值不能重用。如果从表中删除某一行，其主键值不分配给新行。

一种定义主键的方法是创建它，如下所示：

```sql
CREATE TABLE Vendors(
	vend_id			CHAR(10) NOT NULL	PRIMARY KEY,
	vend_name		CHAR(50) NOT NULL,
	vend_address	CHAR(50) NULL,
	vend_city 		CHAR(50) NULL,
	vend_state 		CHAR(5) NULL,
	vend_zip 		CHAR(10) NULL,
	vend_country 	CHAR(50) NULL
);
或
CREATE TABLE Vendors(
	vend_id			CHAR(10) NOT NULL,
	vend_name		CHAR(50) NOT NULL,
	vend_address	CHAR(50) NULL,
	vend_city 		CHAR(50) NULL,
	vend_state 		CHAR(5) NULL,
	vend_zip 		CHAR(10) NULL,
	vend_country 	CHAR(50) NULL,
	PRIMARY KEY(vend_id)
);
```

另一种定义主键的方法是给表添加属性，如下所示：

```sql
ALTER TABLE Vendors
ADD PRIMARY KEY (vend_id);
```

### 外键

外键是表中的一列，其值必须列在另一表的主键中。外键是保证引用完整性的极其重要部分。

```sql
CREATE TABLE Orders(
	order_num	INTEGER	NOT NULL PRIMARY KEY,
	order_date	DATETIME	NOT NULL,
	cust_id		CHAR(10)	NOT NULL	REFERENCES Customers(cust_id)
);
```

相同的工作也可以在 `ALTER TABLE` 语句中用 `CONSTRAINT` 语法来完整：

```sql
ALTER TABLE Orders
ADD CONSTRAINT
FOREIGN KEY (cust_id) REFERENCES Customers (cust_id);
```

外键除保证引用完整性外，还不允许删除在另一个表中具有关联行的行，因而可以防止意外删除数据。

### 唯一约束

唯一约束用来保证一列（或一组列）中的数据是唯一的。它们类似于主键，但存在以下重要区别。

- 表中包含多个唯一约束，但每个表只允许一个主键。
- 唯一约束列可包含NULL值。
- 唯一约束列可修改或更新。
- 唯一约束列的值可重复使用。
- 与主键不一样，唯一约束不能用来定义外键。

```sql
CREATE TABLE Customers(
	cust_id	INT NOT NULL PRIMARY KEY,
	cust_contact CHAR(100) NOT NULL,
	cust_email CHAR(100) NULL UNIQUE,
	...
	cust_country CHAR(100) NULL
);
或
CREATE TABLE Customers(
	cust_id	INT NOT NULL PRIMARY KEY,
	cust_contact CHAR(100) NOT NULL,
	cust_email CHAR(100) NULL,
	...
	cust_country CHAR(100) NULL,
	UNIQUE (cust_email)
);
```

相同的工作也可以在 `ALTER TABLE` 语句中用 `CONSTRAINT` 语法来完整：

```sql
ALTER TABLE Customers 
ADD UNIQUE(cust_email);
或者
CREATE UNIQUE index CustEmailIndex ON Customers(cust_email);
```

### 检查约束

检查约束用来保证一列（或一组列）中的数据满足一组指定的条件。检查约束的常见用途有以下几点。

- 检查最小或最大值。例如，防止 0 个物品的订单（即使0是合法的数）。
- 指定范围。例如，保证发货日期大于等于今天的日期，但不超过今天起一年后的日期。
- 只允许特定的值。例如，在性别字段中只允许M或F。

检查约束可以确保数据库的数据是想要的数据。

```sql
CREATE TABLE OrderItems(
	order_num	INTEGER			NOT NULL,
	order_item	INTEGER 		NOT NULL,
	prod_id		CHAR(10)		NOT NULL,
	quantity	INTEGER			NOT NULL,
	CHECK(quantity > 0),
	item_price	DECIMAL(5,2)	NOT NULL
);
```

利用这个约束，任何插入（或更新）的行都会被检查，保证 `quantity` 大于0。

```sql
ALTER TABLE OrderItems 
ADD CONSTRAINT check_quantity 
CHECK(quantity > 0);
```

删除检查约束

```sql
ALTER TABLE OrderItems
DROP CONSTRAINT check_quantity;
```


# 二十、索引

索引用来排序数据以加快搜索和排序操作的速度。

可以在一个或多个列上定义索引，使其数据库将内容排序并保存。

在开始创建索引前，应该记住以下内容：

- 索引改善检索操作的性能，但降低了数据插入、修改和删除的性能。在执行这些操作时，DBMS必须动态地更新索引。
- 索引数据可能要占用大量的存储空间。
- 并非所有数据都适合做索引。取值不多的数据（如州）不如具有更多可能值的数据（如姓或名），能通过索引得到那么多的好处。
- 索引用于数据过滤和数据排序。如果你经常以某种特定的顺序排序数据，则该数据可能适合做索引。
- 可以在索引中定义多个列（例如，州加上城市）进行联合索引，但这种进行单独检索没有用处。

```sql
CREATE INDEX prod_name_ind
ON Products(prod_name);
```

# 二十一、事务管理

事务处理（transaction processing）可以确保成批的 SQL 操作要么完全执行，要么完全不执行，维护了数据库的完整性。

基本术语：

- 事务（transaction）指一组 SQL 语句；
- 回退（rollback）指撤销指定 SQL 语句的过程；
- 提交（commit）指将未存储的 SQL 语句结果写入数据库表；
- 保留点（savepoint）指事务处理中设置的临时占位符（placehodler），可以对它发布回退（与回退整个事务处理不同）。

事务处理用来管理 `INSERT`、`UPDATE` 和 `DELETE` 语句。不能回退 `SELECT` 语句，回退 `SELECT` 语句也没意义；也不能回退 `CREATE` 和 `DROP` 语句。事务处理中可以使用这些语句，但进行回退时，这些操作也不撤销。

管理事务的关键在于将 SQL 语句组分解为逻辑块，并明确规定数据何时应该回退，何时不应该回退。

MySQL 的事务提交默认是隐式提交，每执行一条语句就把这条语句当成一个事务然后进行提交。当出现 `START TRANSACTION` 语句时，会关闭隐式提交；当 `COMMIT` 或 `ROLLBACK` 语句执行后，事务会自动关闭，重写回复隐式提交。

设置 `autocommit` 为 0 可以取消自动提交；`autocommit` 标记是针对每个连接而不是针对服务器的。

如果没有设置保留点，`ROLLBACK` 会回退到 `START TRANSACTION` 语句处；如果设置了保留点，并且在 `ROLLBACK` 中指定该保留点，则会回退到该保留点。可以在 SQL 代码中设置任意多的保留点，越多越好。因为保留点越多，就越能灵活地进行回退。

```sql
START TRANSACTION
// ...
SAVEPOINT delete1
// ...
ROLLBACK TO delete1
// ...
COMMIT
```

# 二十二、字符集

基本术语：

- 字符集为字母和符号的集合；
- 编码为某个字符集成员的内部表示；
- 校对字符指定如何比较，主要用于排序和分组。

给表指定字符集：

```sql
CREATE TABLE test(
	id VARCHAR(10) NOT NULL PRIMARY KEY,
	user_name VARCHAR(100) NOT NULL DEFAULT ''
)ENGINE=INNODB DEFAULT CHARACTER SET utf8mb4;
```

新增column的字符集

```sql
ALTER TABLE test ADD COLUMN new_column VARCHAR(20) CHARACTER SET utf8;
```


# 二十三、数据库安全

大多数DBMS都给管理员提供了管理机制，对数据的访问进行授予或限制。

任何安全系统的基础都是用于授予和身份确认。

需要保护的操作：

- 对数据库管理功能（创建表、更改或删除已存在的表等）的访问；
- 对特定数据库或表的访问；
- 访问的类型（只读、对特定列的访问等）；
- 仅通过视图或存储过程对表进行访问；
- 创建多层次的安全措施，从而允许多种基于登录的访问和控制；
- 限制管理用户账户的能力。