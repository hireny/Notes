# 前言

# SELECT语句优化

对于低效的查询，我们发现下面两个步骤来分析总是很有效：

1. 确认应用程序是否在检索大量超过需要的数据。这通常意味着访问了太多的行，但有时候也可能是访问了太多的列。
2. 确认 MySQL 服务器是否在分析大量超过需要的数据行。

### 自动杀掉慢查询语句

使用参数 `max_statement_time` 控制 SQL 执行 SQL 执行时间（单位：秒）。默认值 0，表示不限制 SQL 执行时间。

```shell
举例：set global max_statement_time=1;	# 由 MySQL 自动杀死超过 1s 的慢查询语句。
```

### 避免子查询

尽量不要编写子查询，虽然 MySQL 5.6 开始有内部的优化器将子查询改写为关联查询，但这个改写过程总是有些开销。比如如下语句在 5.5 版本性能很差（5.5版本MySQL会先全表扫描外表，每条外表数据会传到内标与之关联，外表如果记录数多，性能就差）。

```sql
SELECT country_id, country_name, continent, code 
FROM country WHERE continent='Europe' AND country.id
IN (SELECT city.country FROM city WHERE city, population>1*1000*1000);
```

### WHERE优化

### RANGE优化

`RANGE` 访问方法使用单一索引来搜索包含在一个或几个索引值距离内的表记录的子集。可以用于单部分或多元索索引。

### IS NULL优化

MySQL可以对可以结合 `col_name=constant_value` 使用的 `col_name IS NULL` 进行相同的优化。例如，MySQL可以使用索引和范围用 `IS NULL` 搜索 `NULL`。

```sql
SELECT * FROM tb_name WHERE key_col IS NULL;
SELECT * FROM tb_name WHERE key_col <=> NULL;
SELECT * FROM tb_name WHERE key_col-const1 OR key_col=const2 OR key_col IS NULL;
```

### DISTINCT优化

### JOIN优化

### 简化外部联合

### ORDER BY优化

### GROUP BY优化

### LIMIT优化

如下SQL：

```sql
SELECT * FROM test ORDER BY id LIMIT 100000, 10;
```

如上 SQL 虽然用到了 id 索引，但是从第一行起要定位到 100000 行，再向后扫描 10 行，等于在做全表扫描，性能低下。优化后的 SQL 如下：

```sql
SELECT * FROM test WHERE id>100000 ORDER BY id LIMIT 10;
```

以上 SQL利用 id 索引直接定位到第 100001 行，再向后扫描 10 行，相当于一个 `RANGE` 范围扫描。性能提升不少。

也可以使用表内连接进行 `LIMIT` 分页的优化

如下 SQL：

```sql
SELECT id,title,createdate FROM test ORDER BY createdate ASC LIMIT 100000, 10;
```

优化后的 SQL：

```sql
SELECT a.id, a.title, a.createdate FROM test a 
	JOIN (
		SELECT id FROM test ORDER BY createdate ASC LIMIT 100000, 1
	) b
ON a.id>=b.id LIMIT 10
```

优化的思路是先取出 100000 行后面的 1 条记录的 id，再用表内连接的方法取出后面 10 条。

在系统中需要进行分页操作的时候，通常会使用 `LIMIT` 加上偏移量的办法实现，同时加上合适的 `ORDER BY` 子句。如果有对应的索引，通常效率会不错，否则，MySQL需要做大量的文件排序操作。

一个非常常见又令人头疼的问题就是，在偏移量非常大的时候，例如可能是 `LIMIT 1000, 20`这样的查询，这时MySQL需要查询10020条记录然后只返回最后20条，前面10000条记录都将被抛弃，这样的代价非常高。如果所有的页面被访问的频率都相同，那么这样的查询平均需要访问半个表的数据。要优化这种查询，要么是在页面中限制分页的数量，要么是优化大偏移量的性能。

优化此类分页查询的一个最简单的办法就是尽可能地使用索引覆盖扫描，而不是查询所有的列。然后根据需要做一次关联操作再返回所需的列。对于偏移量很大的时候，遮掩做的效率会提升非常大。考虑下面的查询：

```sql
SELECT film_id, description 
FROM sakila.film
ORDER BY titile
LIMIT 50, 5;
```

如果这个表非常大，那么这个查询最好改写成下面的样子：

```sql
SELECT film.film_id, film.description
FROM sakila.film 
	INSERT JOIN (
		SELECT film_id FROM sakila.film
		ORDER BY title LIMIT 50, 5
	)
```

这里的 "延迟关联" 将大大提升查询效率，它让 MySQL 扫描尽可能少的页面，获取需要访问的记录后再根据关联列回原表查询需要的所有列。这个技术也可以用于优化关联查询中的 `LIMIT` 子句。

有时候也可以将 `LIMIT` 查询转换为已知位置的查询，让 MySQL 通过范围扫描获得到对应的结果。例如，如果在一个位置列上有索引，并且预先计算出了边界值，上面的查询就可以改写为：

```sql
SELECT film_id, description 
FROM sakila.film
WHERE position BETWEEN 50 AND 54 ORDER BY position;
```

`LIMIT` 和 `OFFSET` 的问题，其实是 `OFFSET` 的问题，它会导致 MySQL 扫描大量不需要的行然后再抛弃掉。如果可以使用书签记录上次取数据的位置，那么下次就可以直接从该书签记录的位置开始扫描，这样就可以避免使用 `OFFSET`。例如，若需要按照租借记录做翻页，那么可以根据最新一条租借记录向后追溯，这种做法可行是因为租借的主键是单调增长的。首先使用下面的查询获得第一组结果：

```sql
SELECT * FROM sakila.rental
ORDER BY rental_id DESC LIMIT 20;
```
假设上面的查询返回的是主键为 16049 到 16030 的租借记录，那么下一页查询就可以从 16030 这个点开始：

```sql
SELECT * FROM sakila.rental
WHERE rental_id < 16030
ORDER BY rental_id DESC LIMIT 20;
```

### UNION 优化

MySQL 总是通过创建并填充临时表的方式来执行 `UNION` 查询。因此很多优化策略在 `UNION` 查询中都没法很好地使用。经常需要手工地将 `WHERE`、`LIMIT`、`ORDER BY` 等子句 "下推" 到 `UNION` 的各个子查询中，以便优化器可以充分利用这些条件进行优化（例如，查接将这些子句冗余地写一份到各个子查询）。

除非确实需要服务器消除重复的行，否则就一定要使用 `UNION ALL`，这一点很重要。如果没有 `ALL` 关键字，MySQL 会给临时表加上 `DISTINCT` 选项，这会导致对整个临时表的数据做唯一性检查。这样做的代价非常高。即使有 `ALL` 关键字，MySQL 仍然会使用临时表存储结果。事实上，MySQL总是将结果放入临时表，然后再读出，再返回给客户端。虽然很多时候这样做是没有必要的（例如，MySQL可以直接把这些结果返回给客户端）。


### 如何避免表扫描

#### OR子句优化

如下 SQL：

```sql
SELECT * FROM test WHERE category='A' OR category='B';
```

虽然在 `category` 字段加上了索引，但走不到索引，可以用 `UNION ALL` 语句优化：

```sql
SELECT * FROM test WHERE category='A'
UNION ALL
SELECT * FROM test WHERE category='B'
```

# INSERT语句优化

插入一个记录需要的时间由下列因素组成，其中的数字表示大约比例：

- 连接
- 发送查询给服务器
- 分许查询
- 插入记录
- 插入索引
- 关闭

这不考虑打开表的初始开销，每个并发运行的查询打开

表的大小以 logN(B树)的速度减慢索引的插入。

加快插入的一些方法：

如果同时从一个客户端插入很多行，使用含多个 `VALUE` 的 `INSERT` 语句同时插入几行。这比使用单行 `INSERT` 语句块（在某些情况下快几倍）。如果你正向一个非空表添加数据，可以调节 `bulk_insert_buffer_size` 变量，使数据插入更快。

如果你从不同的客户端插入很多行，能通过 `INSERT DELAYED` 语句加快所读。

用 MyISAM，如果在表中没有删除的行，能在 `SELECT` 语句正在运行的同时插入行。

当从一个文本文件装载一个表时，使用 `LOAD DATA INFILE`。这通常比使用很多 `INSERT` 语句快 20 倍。

当有很多索引时，有可能要多做些工作使得 `LOAD DATA INFILE` 更快写。


# UPDATE语句优化

更新查询的优化同 `SELECT` 查询一样，需要额外的写开销。写速度依赖于更新的数据大小和更新的索引的数量。没有更改的索引不被更新。

使更改更快的另一个方法是推迟更改然后在一行内进行多次更新。如果锁定表，同时做多个更新比一次做一个快的多。

# DELETE语句优化

删除一个记录的时间与索引数量确切成正比。为了更快速地删除记录，可以增加键告诉缓冲的大小。

如果想要删除一个表的所有行，使用 `TRUNCATE TABLE tb_name` 而不是用 `DELETE FROM tb_name`。