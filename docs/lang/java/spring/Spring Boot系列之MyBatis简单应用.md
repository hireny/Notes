# 前言

MyBatis是常用的开源持久层框架，MyBatis简化了Java应用程序中对数据库的访问，实现了诸如动态SQL，结果集映射等，高效又不失灵活。

下面简单介绍下在 `spring-boot` 项目中整合`mybatis` 的过程。

# 项目

## 数据库

执行 `student.sql` 创建此表：

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

## 引入 MyBatis

创建 Maven 项目并在 `pom.xml` 中引入 `mybatis-spring-boot-starter` 依赖：

```xml
<dependencies>
    <dependency>
        <groupId>org.mybatis.spring.boot</groupId>
        <artifactId>mybatis-spring-boot-starter</artifactId>
        <version>2.1.3</version>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## 配置数据库连接属性

在 `src/main/resources` 目录下创建 `application.properties` 文件并添加以下内容：

```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/xxxx?serverTimezone=Asia/Shanghai&useUnicode=true&characterEncoding=UTF-8&zeroDateTimeBehavior=convertToNull&useSSL=false
spring.datasource.username=xxxx
spring.datasource.password=xxxx
```

## 编写Java模型类

按照以下代码创建 `Student` 类：

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
}
```

## 创建数据访问层

创建 `StudentMapper` 接口：

```java
@Repository
public interface StudentMapper {
}
```

接口创建好后，我们需要创建 `StudentMapper.xml` 文件：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="me.hireny.tutorial.springboot.mybatis.mapper.StudentMapper">
</mapper>
```

# Mapper

## INSERT

项目搭建完成后，我们在`StudentMapper`类中实现插入`SQL`的方法：

```java
int addStudent(Student student);
```

在 `StudentMapper.xml` 中对应如下：

```xml
<insert id="addStudent">
    INSERT INTO STUDENT (name, sex, age, phone, enrollment_time) VALUES (#{name},#{sex},#{age},#{phone},#{enrollmentTime});
</insert>
```

有时需要获取插入时数据库的自增`Key`。`MyBatis`的实现如下：

```java
int addStudentAndBackKey(Student student);
```

在 `StudentMapper.xml` 中对应如下：

```xml
<insert id="addStudentAndBackKey" useGeneratedKeys="true" keyProperty="sid" keyColumn="sid">
    INSERT INTO STUDENT (name, sex, age, phone, enrollment_time) VALUES (#{name},#{sex},#{age},#{phone},#{enrollmentTime});
</insert>
```

在`insert` 标签中有比上面添加方法多了三个参数：

- `useGeneratedKeys`：表示是否自动生成主键，默认 `false`。
- `keyProperty`：表示返回的主键赋值给哪个属性。
- `keyColumn`：表示数据库中的自增主键的列名，默认是数据库表的第一列。当主键列不是表中的第一列的时候需要设置，PostgreSQL 必须设置。

主键自动生成，取决于数据库是否支持自增主键。实际上当设置了 `useGeneratedKeys=true`时，MyBatis会调用 Jdbc 的 `getGeneratedKeys` 方法，并将获取的主键赋值给`keyProperty`指定的属性。

## DELETE

接着，实现删除`SQL`：

```java
int deleteStudent(Student student);
```

在 `StudentMapper.xml` 中对应如下：

```xml
<delete id="deleteStudent">
    DELETE FROM STUDENT WHERE sid=#{sid};
</delete>
```

## UPDATE

最后，实现更改`SQL`：

```java
int updateStudent(Student student);
```

在 `StudentMapper.xml` 中对应如下：

```xml
<update id="updateStudent">
    UPDATE STUDENT SET name=#{name}, sex=#{sex}, age=#{age}, phone=#{phone} WHERE sid=#{sid};
</update>
```

## SELECT

现在，我们先简单的获取表中行数：

```java
int count();
```

在 `StudentMapper.xml` 中对应如下：

```xml
<select id="count" resultType="java.lang.Integer">
    SELECT COUNT(*) FROM STUDENT;
</select>
```

放在这里的 `StudentMapper.xml` 会被自动扫描到，但是有另外一个 Maven 带来的问题，就是 Java 目录下的 `xml` 资源在项目打包时会被忽略掉，所以，如果 `StudentMapper.xml` 放在包下，需要在 `pom.xml` 文件中再添加如下配置，避免打包时 Java 目录下的 `XML` 文件被自动忽略掉：

```xml
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.xml</include>
            </includes>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
        </resource>
    </resources>
</build>
```

`StudentMapper.xml` 也可以直接放在 `resources` 目录下，这样就不用担心打包时被忽略了，但是放在 `resources` 目录下，必须创建和 `Mapper` 接口包目录相同的目录层级，这样才能打包后 `XML` 和 `Mapper` 接口处于一起，否则 `XML` 文件不能被自动扫描，这个时候就需要添加额外配置，例如，在 `resources` 目录下创建 `mapper` 目录用来放 `mapper` 文件，如下：

此时在 `application.properties` 中告诉 `MyBatis` 去哪里扫描 `mapper`：

```properties
mybatis.mapper-locations = classpath:mapper/*.xml
```

如此配置后，`mapper`就可正常使用。注意这种方式不需要在 `pom.xml` 文件中配置文件过滤。

# 总结

> 本文使用的 `Spring Boot` 版本为 `2.2.1.RELEASE` ，`mysql-connector-java`版本为`8.0.18`。