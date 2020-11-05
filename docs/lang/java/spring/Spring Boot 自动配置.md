# Spring Boot 自动配置

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