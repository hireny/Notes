# Spring Boot 启动原理

此文的SpringBoot启动流程分析是基于SpringBoot2.x。

## 核心注解@SpringBootApplication

```java
@SpringBootApplication
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```
点开 `@SpringBootApplication` 的源码，可以看到这个注解其实是一个组合注解。

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
        @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
        @Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
...
}
```

虽然定义了使用多个Annotation进行了元信息标注，但实际上重要的只有三个Annotation：
- `@SpringBootConfiguration`
-  `@EnableAutoConfiguration`
-  `@ComponentScan` 

即 `@SpringBootApplication` = (默认属性) `@SpringBootConfiguration` + `@EnableAutoConfiguration` + `@ComponentScan` 。

所以，如果我们使用如下的SpringBoot启动类，整个SpringBoot应用依然可以与之前的启动类功能对等：
```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

- `@SpringBootConfiguration`

	点进去这个注解，可以发现还有 `@Configuration` 注解，对于 `@Configuration` 注解，用过Spring或SpringBoot的基本上都不陌生，标注了 `@Configuration` 的类相当于Spring中的配置XML，不过SpringBoot社区推荐使用JavaConfig，所以 `@Configuration` 就构建出了一个基础 JavaConfig 的 Ioc 容器。
	
- `@ComponentScan`

	看到这个注解，可以回想以下以前使用Spring MVC时，XML配置文件里的一个标签 `<context:component-scan base-package="" />` ，不过这个注解一般不需要手动指定扫描的包路径，它默认会从标注了 `@ComponentScan` 的类所在包往下查找，将标注了如 `@Component`，`@Service` 等Bean加载到Ioc容器里。

- `@EnableAutoConfiguration`

	Spring中有很多 `@Enable*` 的注解，表示开启某项东西，如 `@EnableSchuduling`。 所以看这个注解的名字就知道是开启自动配置。这是一个符合注解，其中最主要的还是`@Import`，借助于 `EnableAutoConfigurationImportSelector`，将所有符合自动配置条件的Bean加载到Ioc容器里。
	SpringBoot加载自动配置的方法有两种：
	
	* 在classpath下新键META-INF/spring.factories文件，将标注了`@Configuration` 的类的全路径配置到此文件中，如：`org.springframework.boot.autoconfigure.EnableAutoConfiguration=\com.example.config.ExampleBeanConfiguration,\com.example.config.ExampleAutoConfiguration`，在启动时，通过SpringFactoriesLoader工具类，将所有META-INF目录下的spring.factories文件中的配置类加载到Ioc容器里。
	* 使用`@Import`，将配置类加载到Ioc容器里