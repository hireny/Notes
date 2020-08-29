# 什么是JPA

JPA（Java Persistence API）是用于管理Java EE 和 Java SE 环境中的持久化，以及对象/关系映射的Java API

最新规范为 "JSR 338: Java Persistence 2.1" http://jcp.org/en/jsr/detail?id=338

实现：EclipseLink、Hibernate、Apache OpenJPA


# JPA核心概念

实体

- 实体表示关系数据库中的表
- 每个实体实例对应于表中的行
- 类必须用 javax.persistence.Entity注解

类必须有一个 public 或 protected 的无参数的构造函数
实体实例被当作值以分离对象方式进行传递（例如通过会话bean的远程业务接口），则该类必须实现 Serializable 接口
唯一的对象标识符：简单主键（javax.persistence.Id）、复合主键（javax.persistence.EmbeddedId和javax.persistence.IdClass）

关系

- 一对一：@OneToOne
- 一对多：@OneToMany
- 多对一：@ManyToOne
- 多对多：@ManyToMany