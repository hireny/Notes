# 前言



# 索引优化

索引用在查询中，可以快速定位，减少查询，提高查询速度，从而提高数据库的性能。

有几种特殊情况，可能使得索引作用失效：

### **LIKE语句**  

```sql
LIKE '%ab%';
或
LIKE '%ab';
```

匹配符在第一个位置都会使得索引失效。

```sql
LIKE 'ab%';
```

上述例子，索引才会起到作用。

### **最左前缀法则**  

如果对多个字段创建索引，在查询条件中必须要携带第一个字段，否则索引失效。

在 `employee` 表中为 `username`，`password` 字段创建多列索引，以下写法索引不失效：

```sql
SELECT username FROM employee WHERE username='Jan';

SELECT username FROM employee WHERE username='Jan' AND password='123456';

SELECT username FROM employee WHERE password='123456' AND username='Jan';
```

当缺少最左侧 `username` 字段时，就违反了最左侧法则，索引失效，如下所示：

```sql
SELECT username FROM employee WHERE password='123456';
```

### **OR语句**  

查询语句中使用 `OR` 关键字时，左右要查询的字段均为索引时，才会生效，否则，只有一个是索引的话，会失效。

### **隐式转换**  

当数据类型出现隐式转换时，会使索引失效。

如下所示：

```sql
SELECT username FROM employee WHERE password=123456;
```

上述例子可以查出数据，但是`password` 字段是`VARCHAR` 类型，这样数据库会进行强制类型转换，产生全表扫描。

### **IS NULL 或 IS NOT NULL**  

在索引列上使用 IS NULL 或 IS NOT NULL操作。索引是不索引空值的，所以这样的操作不能使用索引，可以用其他的办法处理，例如：数字类型，判断大于0，字符串类型设置一个默认值，判断是否等于默认值即可。

### **索引列不能参与计算**  

在索引字段进行计算操作会使索引失效。

在字段上进行计算不能命中索引

```sql
select name from user where FROM_UNIXTIME(create_time) < CURDATE();
```

应该修改为：

```sql
select name from user where create_time < FROM_UNIXTIME(CURDATE());
```

### **函数**  

在索引字段上使用函数会使索引失效。

### **操作符**  

在索引字段上使用`not`，`<>`，`!=`。不等于操作符是永远不会用到索引的，因此对它的处理只会产生全表扫描。 优化方法： `key<>0` 改为 `key>0 OR key<0`。

# 插入语句的优化

# 查询语句的优化

### ORDER BY 排序优化

### LIMIT 分页优化

### GROUP BY 分组优化

负向查询不能使用索引

```sql
SELECT name FROM USER WHERE id not in (1,3,4)
```

应该修改为：

```sql
SELECT name FROM user WHERE id in (2,5,6)
```

前导模糊查询不能使用索引

```sql
SELECT name FROM user WHERE name LIKE '%zhangsan'
```

非前导则可以：

```sql
SELECT name FROM user WHERE name LIKE 'zhangsan%'
```

建议可以考虑使用 `Lucene` 等全文索引工具来代替频繁的模糊查询。

数据区分不明显的不建议创建索引

如 user 表中的性别字段；可以明显区分的才建议创建索引，如身份证等字段。

字段的默认值不要为 NULL
这样会带来和预期不一致的查询结果




如果明确知道只有一条记录返回

```sql
select name from user where username='zhangsan' limit 1
```

可以提高效率，可以让数据库停止游标移动。



如果需要进行 join 的字段量表的字段类型要相同

不然也不会命中索引。

禁止使用 select * ，要用 select 列名 代替 select *


建议使用预编译语句进行数据库操作

禁止使用不含字段的 insert 语句

一个 SQL 只能利用复合索引中的一列进行范围查询

禁止在 where 条件上对属性使用函数或表达式

禁止大表使用 join 查询，禁止大表使用子查询

避免使用 join 关联太多的表

禁止使用 OR 条件，必须改为 IN 查询

尽量减少与数据库的交互次数

禁止使用 order by rand() 进行排序