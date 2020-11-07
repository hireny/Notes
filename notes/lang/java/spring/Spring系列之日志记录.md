Spring Boot 使用 Commons Logging 进行所有内部日志记录，但保留底层日志实现。为 Java Util Logging，Log4J2 和 Logback 提供了默认配置。在每种情况下，记录器都预先配置为使用控制台输出，并且还提供可选的文件输出。

默认情况下，如果使用 `starters`，则使用 Logback 进行日志记录。还包括适当的 Logback 路由，以确保使用 Java Util Logging，Commons Logging，Log4J 或 SLF4J 的依赖库都能正常工作。

## 日志格式

Spring Boot 的默认日志输出类似于以下示例：

输出以下项目：

