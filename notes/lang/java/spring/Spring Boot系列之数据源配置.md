在实际的应用开发中，与数据库交互通常使用数据库连接池来重用Connection对象，减少资源消耗。

Spring Boot 的数据源是自动配置的。在 Spring Boot 2.0 总，有几种数据源配置可选，它们按照 HikariCP -> Tomcat pooling -> Commons DBCP2 优先顺序来选择最后实际使用哪个数据源。

Spring Boot 创建默认 DataSource 时，规则如下：

- 优先寻找创建 Tomcat 连接池
- 如果没有 Tomcat 连接池，会查找创建 HikariCP
- 如果没有 HikariCP 连接池，会查找创建 dbcp
- 如果没有 dbcp 连接池，会查找创建 dbcp2
- 可以使用 spring.datasource.type 属性指定连接池类型

查看一下数据源的自动配置方案：

```java
@Configuration(
    proxyBeanMethods = false
)
@ConditionalOnClass({DataSource.class, EmbeddedDatabaseType.class})
@EnableConfigurationProperties({DataSourceProperties.class})
@Import({DataSourcePoolMetadataProvidersConfiguration.class, DataSourceInitializationConfiguration.class})
public class DataSourceAutoConfiguration {
    public DataSourceAutoConfiguration() {
    }

    static class EmbeddedDatabaseCondition extends SpringBootCondition {
        private final SpringBootCondition pooledCondition = new DataSourceAutoConfiguration.PooledDataSourceCondition();

        EmbeddedDatabaseCondition() {
        }

        public ConditionOutcome getMatchOutcome(ConditionContext context, AnnotatedTypeMetadata metadata) {
            Builder message = ConditionMessage.forCondition("EmbeddedDataSource", new Object[0]);
            if (this.anyMatches(context, metadata, new Condition[]{this.pooledCondition})) {
                return ConditionOutcome.noMatch(message.foundExactly("supported pooled data source"));
            } else {
                EmbeddedDatabaseType type = EmbeddedDatabaseConnection.get(context.getClassLoader()).getType();
                return type == null ? ConditionOutcome.noMatch(message.didNotFind("embedded database").atAll()) : ConditionOutcome.match(message.found("embedded database").items(new Object[]{type}));
            }
        }
    }

    static class PooledDataSourceAvailableCondition extends SpringBootCondition {
        PooledDataSourceAvailableCondition() {
        }

        public ConditionOutcome getMatchOutcome(ConditionContext context, AnnotatedTypeMetadata metadata) {
            Builder message = ConditionMessage.forCondition("PooledDataSource", new Object[0]);
            return DataSourceBuilder.findType(context.getClassLoader()) != null ? ConditionOutcome.match(message.foundExactly("supported DataSource")) : ConditionOutcome.noMatch(message.didNotFind("supported DataSource").atAll());
        }
    }

    static class PooledDataSourceCondition extends AnyNestedCondition {
        PooledDataSourceCondition() {
            super(ConfigurationPhase.PARSE_CONFIGURATION);
        }

        @Conditional({DataSourceAutoConfiguration.PooledDataSourceAvailableCondition.class})
        static class PooledDataSourceAvailable {
            PooledDataSourceAvailable() {
            }
        }

        @ConditionalOnProperty(
            prefix = "spring.datasource",
            name = {"type"}
        )
        static class ExplicitType {
            ExplicitType() {
            }
        }
    }

    @Configuration(
        proxyBeanMethods = false
    )
    // 判断是否引入依赖的数据源：HikariDataSource、tomcat.jdbc.pool.DataSource、BasicDataSource
    @Conditional({DataSourceAutoConfiguration.PooledDataSourceCondition.class})
    @ConditionalOnMissingBean({DataSource.class, XADataSource.class})
    // 如果同时导入了Hikari、Tomcat、Dbcp2以及其它数据源的依赖，那么顺序注册数据源，Hikari优先级最高，注册了Hikari数据源，其它数据源就不注册了
    @Import({Hikari.class, Tomcat.class, Dbcp2.class, Generic.class, DataSourceJmxConfiguration.class})
    protected static class PooledDataSourceConfiguration {
        protected PooledDataSourceConfiguration() {
        }
    }

    @Configuration(
        proxyBeanMethods = false
    )
    @Conditional({DataSourceAutoConfiguration.EmbeddedDatabaseCondition.class})
    @ConditionalOnMissingBean({DataSource.class, XADataSource.class})
    @Import({EmbeddedDataSourceConfiguration.class})
    protected static class EmbeddedDatabaseConfiguration {
        protected EmbeddedDatabaseConfiguration() {
        }
    }
}
```

首先需要在 `pom.xml` 中添加数据库驱动依赖，这里使用 `MySQL`：

```xml
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

在 `application.properties` 中存在通用的数据源配置如下：

```properties
#通用数据源配置
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/spring-boot-jdbc?charset=utf8mb4&useSSL=false
spring.datasource.username=xxx
spring.datasource.password=xxx
```

剩下的配置项基本上都是专用配置。下面介绍几种数据库连接池：

## HikariCP

HikariCP 号称是目前世界上最快的连接池，在 `pom.xml` 依赖引入：

```xml
<dependency>
    <groupId>com.zaxxer</groupId>
    <artifactId>HikariCP</artifactId>
    <version>3.4.5</version>
</dependency>
```

而 Spring Boot 2.x 工程中默认推荐使用，如果在项目中引入`spring-boot-starter-jdbc`依赖来操纵数据库，就已经包括 HikariCP 数据源的依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

接下来是在`application.properties` 配置文件中配置连接池的专用配置信息，而如果你不配置，Spring Boot 会使用默认配置信息：

```properties
# Hikari 数据源专用配置
spring.datasource.type=com.zaxxer.hikari.HikariDataSource
spring.datasource.hikari.auto-commit= true
spring.datasource.hikari.minimum-idle=5
spring.datasource.hikari.idle-timeout= 60000
spring.datasource.hikari.connection-timeout= 30000
spring.datasource.hikari.max-lifetime= 1800000
spring.datasource.hikari.pool-name= DatebookHikariCP
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.connection-test-query=SELECT 1
```


其中 Hikari 数据源的大部分配置如下图。每个配置代表的含义可以自行查询一下。

## tomcat-jdbc



## 配置自定义数据源

要配置您自己的 `DataSource`，请在配置中定义该类型的 `@Bean`，Spring Boot 在任何需要的地方重用 `DataSource`，包括数据库初始化。如果需要外部化某些设置，可以将 `DataSource` 绑定到环境中。

以下示例显示如何在 `bean` 中定义数据源：

```java
@Bean
@ConfigurationProperties(prefix="app.datasource")
public DataSource dataSource() {
	return new FancyDataSource();
}
```