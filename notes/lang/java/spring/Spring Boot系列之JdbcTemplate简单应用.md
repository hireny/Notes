# 前言

在进行应用开发中，我们需要实现数据存储，一般会将数据存储在数据库中。而Java应用中要访问数据库会使用Java提供的`JDBC`进行访问。但使用原生`JDBC`与数据库交互较麻烦，Spring提供了`JDBC`模块对原生`JDBC`进行封装。

下面简单介绍下`spring-boot-starter-jdbc`组件的使用。

# 数据库

首先，我们可以执行`student`的语句的`SQL`来创建此表：

```sql
CREATE TABLE `student` (
  `sid` INT(11) PRIMARY KEY AUTO_INCREMENT 	COMMENT '学号',
  `sname` VARCHAR(20) 											COMMENT '姓名',
  `sex` VARCHAR(10) 												COMMENT '性别',
  `age` DATETIME 														COMMENT '年龄',
  `phone` VARCHAR(20) 											COMMENT '手机号码',
  `enrollment_time` TIMESTAMP       				COMMENT '入学时间'
) ENGINE=INNODB AUTO_INCREMENT=2020100001 DEFAULT CHARSET=utf8mb4;
```

# 项目创建

## 引入JDBC

创建Maven项目并在`pom.xml`中引入`spring-boot-starter-jdbc`模块：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
        <exclusions>
            <exclusion>
                <groupId>com.zaxxer</groupId>
                <artifactId>HikariCP</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>3.4.5</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## 配置数据库连接属性

在 `src/main/resources` 目录下创建`application.properties`文件并添加以下内容：

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/xxxx?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&useSSL=false
spring.datasource.username=xxxx
spring.datasource.password=xxxx
```

## 编写Java模型类

按照以下代码创建`Student`类：

```java
public class Student {
    /** 学号 */
    private Long sid;
    /** 学生姓名 */
    private String name;
    /** 性别 */
    private String sex;
    /** 年龄 */
    private LocalDateTime age;
    /** 手机号码 */
    private Long phone;
    /** 入学时间 */
    private LocalDateTime enrollmentTime;
    ...getter与setter...
```

## 创建数据访问层

创建`StudentDao`类并注入`JdbcTemplate`，如下所示：

```java
@Repository
public class StudentDao {
    @Autowired
    private JdbcTemplate jdbcTemplate;
```

# JdbcTemplate

下面开始使用`JdbcTemplate`进行简单的`CRUD`操作。

## query

`JdbcTemplate`提供了几种`query`查询的功能。给出几个使用例子。

先实现一个简单的例子，用于获取表中行数。

```java
public int count() {
    return jdbcTemplate.queryForObject("SELECT COUNT(*) FROM STUDENT;", Integer.class);
}
```

有时候还需要使用`WHERE`语句获取需要的行数，而不是全部。

```java
public int count(String sex) {
    return jdbcTemplate.queryForObject("SELECT COUNT(*) FROM STUDENT WHERE sex=?", Integer.class, sex);
}
```

还需要简单的获取数据库中一行的单个列。

```java
public String queryStudentName(Long sid) {
    return jdbcTemplate.queryForObject("SELECT name FROM STUDENT WHERE sid=?", String.class, sid);
}
```

但查询不可能只是查询单个列，这就要填充领域模型。

```java
public Student queryStudent(Long sid) {
    Student student = jdbcTemplate.queryForObject(
            "SELECT sid, name, sex, age, phone, enrollment_time FROM STUDENT WHERE sid=?",
            new Object[]{sid}, new RowMapper<Student>() {
        @Override
        public Student mapRow(ResultSet resultSet, int i) throws SQLException {
            Student student = new Student();
            student.setSid(resultSet.getLong("sid"));
            student.setName(resultSet.getString("name"));
            student.setSex(resultSet.getString("sex"));
            student.setAge(resultSet.getTimestamp("age").toLocalDateTime());
            student.setPhone(resultSet.getLong("phone"));
            student.setEnrollmentTime(resultSet.getTimestamp("enrollment_time").toLocalDateTime());
            return student;
        }
    });
    return student;
}
```

下面在查询中填充多个领域对象，并且可以实现`RowMapper`接口，这样比较方便被引用：

```java
public List<Student> queryStudents() {
    List<Student> students = jdbcTemplate.query("SELECT sid, name, sex, age, phone, enrollment_time FROM STUDENT", 
            new StudentRowMapper());
    return students;
}
public class StudentRowMapper implements RowMapper<Student> {
    @Override
    public Student mapRow(ResultSet resultSet, int i) throws SQLException {
        Student student = new Student();
        student.setSid(resultSet.getLong("sid"));
        student.setName(resultSet.getString("name"));
        student.setSex(resultSet.getString("sex"));
        student.setAge(resultSet.getTimestamp("age").toLocalDateTime());
        student.setPhone(resultSet.getLong("phone"));
        student.setEnrollmentTime(resultSet.getTimestamp("enrollment_time").toLocalDateTime());
        return student;
    }
}
```

而如果数据库表字段和实体类自动对应，可以使用`Spring Jdbc`提供的`BeanPropertyRowMapper`类，而不用自己实现。

```java
public List<Student> queryStudents() {
    List<Student> students = jdbcTemplate.query("SELECT sid, name, sex, age, phone, enrollment_time FROM STUDENT",
            new BeanPropertyRowMapper<>(Student.class));
    return students;
}
```

## update

`JdbcTemplate`提供了`update(...)`实现增删改的操作。参数值可以通过可变参数或者封装在对象内传入。

首先，使用`update(...)`实现插入`SQL`：

```java
public int addStudent(Student student) {
    String string = "INSERT INTO STUDENT (sid, sname, sex, age, phone, enrollment_time) VALUES (?,?,?,?,?,?);";
    return jdbcTemplate.update(string, student.getSid(),
                student.getSname(), student.getSex(), student.getAge(),
                student.getPhone(), student.getEnrollmentTime());
}
```

接着，实现更新`SQL`：

```java
public int updateStudent(Student student) {
    return jdbcTemplate.update("UPDATE STUDENT SET " +
                    "name=?, sex=?, age=?, phone=? " +
                    "WHERE sid=?;",
            student.getName(), student.getSex(),
            student.getAge(), student.getPhone(), student.getSid());
}
```

最后，实现删除`SQL`：

```java
public int deleteStudent(Student student) {
    return jdbcTemplate.update("DELETE FROM STUDENT WHERE sid=?", student.getSid());
}
```

在`update(...)`方法的返回值就是`SQL`执行中受影响的行数。

`update`方法的返回值就是SQL执行受影响的行数。

## 获取自增Key

有时进行插入时要获取数据库自增`Key`。`Spring Jdbc`为我们提供了`PreparedStatementCreator`类并作为第一个参数，该类指定所需`INSERT`语句。另一个参数使用`keyHolder`，在操作成功后会产生自增`Key`。

```java
public int addStudentAndBackKey(Student student) {
    KeyHolder keyHolder = new GeneratedKeyHolder();
    int update = jdbcTemplate.update(new PreparedStatementCreator() {
        @Override
        public PreparedStatement createPreparedStatement(Connection connection) throws SQLException {
            PreparedStatement ps = connection.prepareStatement("INSERT INTO " +
                        "STUDENT (name, sex, age, phone, enrollment_time) " +
                        "VALUES (?,?,?,?,?);", Statement.RETURN_GENERATED_KEYS);
            ps.setString(1, student.getName());
            ps.setString(2, student.getSex());
            ps.setTimestamp(3, Timestamp.valueOf(student.getAge()));
            ps.setLong(4, student.getPhone());
            ps.setTimestamp(5, Timestamp.valueOf(student.getEnrollmentTime()));
            return ps;
        }
    }, keyHolder);
    student.setSid(Objects.requireNonNull(keyHolder.getKey()).longValue());
    return update;
}
```

## batchUpdate

`batchUpdate(...)`还可以实现批量操作，下面实现一个简单的批量添加操作。

```java
public int[] batchAddStudents(List<Student> students) {
        int[] updateCounts = jdbcTemplate.batchUpdate("INSERT INTO " +
                "STUDENT (name, sex, age, phone, enrollment_time) " +
                "VALUES (?,?,?,?,?);", new BatchPreparedStatementSetter() {
            @Override
            public void setValues(PreparedStatement preparedStatement, int i) throws SQLException {
                preparedStatement.setString(1, students.get(i).getName());
                preparedStatement.setString(2, students.get(i).getSex());
                preparedStatement.setTimestamp(3, Timestamp.valueOf(students.get(i).getAge()));
                preparedStatement.setLong(4, students.get(i).getPhone());
                preparedStatement.setTimestamp(5, Timestamp.valueOf(students.get(i).getEnrollmentTime()));
            }

            @Override
            public int getBatchSize() {
                return students.size();
            }
        });
        return updateCounts;
    }
```

## execute

还可以使用`execute(...)`方法执行任何`SQL`，甚至`DDL`语句。这个方法可以传入回调接口、绑定可变参数数组等。

```java
public void createTable() {
    jdbcTemplate.execute("CREATE TABLE sample (id INT PRIMARY KEY AUTO_INCREMENT ,title VARCHAR(11));");
}
```

`Jdbc`还支持调用存储过程等操作，感兴趣的可以查看`Spring`官方文档。

# 总结

本文简单的介绍了`spring-boot-jdbc`的引入与`JdbcTemplate`类的基本使用，它简化了原生`Jdbc`的样板代码，让我们较为专注于`SQL`语句的编写。

> 本文使用的 `Spring Boot` 版本为 `2.2.1.RELEASE` ，`mysql-connector-java`版本为`8.0.18`。