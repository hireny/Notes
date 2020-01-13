# Spring简介

Spring是一个开放源代码的设计层面框架，它解决的是业务逻辑层和其它各层的松耦合问题，因此它将面向接口的编程思想贯穿整个系统应用。Spring是于2003年兴起的一个轻量级的Java开发框架，由Rod Johnson创建。简单来说，Spring是一个分层的JavaSE/EE full-stack(一站式)轻量级开源框架。

Spring被设计为非侵入式的，这意味着您的逻辑代码通常不依赖于Spring框架本身。在集成其它框架(例如数据访问框架)时，虽然存在数据访问框架和Spring库的一些整合逻辑。但是，可以很容易将这些整合逻辑与其余代码分隔开。

## 依赖注入和控制反转

Java应用 - 从嵌入式应用程序到多层服务器端架构的企业应用程序 - 通常由多个应用服务协作完成应用程序的所有功能。因此，应用程序中的对象彼此依赖。

尽管JDK提供了丰富的类库开发功能，但它缺乏将基本对象组织成一个整体的方法，所以这个就是留给架构师和开发人员的一个任务。虽然开发人员可以使用诸如Factory，Abstract Factory，Builder，Decorator和Service Locator 之类的设计模式来组成和优化各种类和对象实例，但这些设计模式也只是总结出来的最佳实践，是开发人员和架构师必须在应用程序中自己实现的内容。

Spring框架的控制反转（IoC）模块通过提供一种将不同对象和框架组合成一个可以使用的应用架构的方法来解决这一问题。Spring 将设计模式通过框架集成到您自己的应用程序中。因此用户可以通过使用Spring来优化和维护自己的应用程序。

### Spring框架模块

Spring 由大约20个模块组成。这些模块分为核心容器，数据访问/集成，Web，AOP（面向切面编程），消息队列和测试等等，如下图所示。

![avactor](./images/0038204c-4b8a-42a5-921d-080f6674f989.png)

下面列出了每个功能可用模块及其名称及其涵盖的主题。

#### 核心模块组件

核心模块由以下部分组成spring-core， spring-beans，spring-context，spring-context-support，和spring-expression （spring表达式语言）模块。所以我们要学习spring，开始的时候最少会引入这五个包。

spring-core和spring-beans模块提供框架的基本功能，包括IOC和依赖注入特性。其中的BeanFactory是工厂模式的复杂实现。它消除了单例模式对单例类的需求，并允许您从实际的程序逻辑中分离出对象之间依赖关系的配置。

上下文（spring-context）模块建立在基于spring-core和spring-beans模块之上：它提供了访问一个框架式的方式，类似于一个JNDI注册表对象的配置。Context模块从Beans模块继承其功能，并添加对国际化（例如，使用资源包），事件传播，资源加载以及通过例如Servlet容器透明创建上下文的支持。Context模块还支持Java EE功能，例如EJB，JMX和基本远程处理。该ApplicationContext接口是模块的核心。 spring-context-support支持将常见的第三方库集成到Spring应用程序上下文中，用于缓存（EhCache，Guava，JCache），邮件（JavaMail），调度（CommonJ，Quartz）和模板引擎（FreeMarker，JasperReports，Velocity）等。

spring-expression模块提供了一种功能强大的表达式语言，用于在运行时查询和操作对象。它是JSP 2.1规范中指定的统一表达式语言（统一EL）的扩展。该语言支持设置和获取属性值，给属性赋值，方法调用，访问数组，集合和索引的内容，逻辑和算术运算符，命名变量以及从Spring的IoC容器中按名称检索对象。它还支持列表选择以及常用列表聚合等。

#### AOP和Instrumentation(插装)

spring-aop模块提供了一个符合AOP 标准的面向切面的编程实现，允许用户自定义。例如，方法拦截器和切入点，以干净的解耦方式实现切面代码和功能代码分离的代码。使用元数据功能，用户还可以以类似于.NET的方式将自己的逻辑合并到代码中。

单独的spring-aspects模块提供与AspectJ的集成。

spring-instrument模块提供了在某些应用程序服务器中使用的类检测支持和类加载器实现。该spring-instrument-tomcat 模块包含Spring的Tomcat检测代理。

#### 消息组件

spring-messaging模块可以在项目中集成消息队列，例如Message，MessageChannel，MessageHandler，为基于消息的应用奠定了基础。该模块还包括一组用于将消息映射到方法的注释，类似于基于Spring MVC注释的编程模型。

#### 数据访问/集成组件

数据访问/集成 层 包括JDBC，ORM，OXM，JMS和交易模块。

spring-jdbc模块提供了一个JDBC 抽象层，无需进行繁琐的JDBC编码和解析数据库供应商特定的错误代码。

spring-tx模块支持 对实现特殊接口的类和所有POJO（普通旧Java对象）的类进行编程和声明式事务管理。

spring-orm模块为流行的对象关系映射 API 提供了集成层 ，包括JPA， JDO和Hibernate。使用该spring-orm模块，您可以将所有这些O / R映射框架与Spring提供的所有其他功能结合使用，例如前面提到的简单声明式事务管理功能。

spring-oxm模块提供了一个抽象层，支持对象/ XML映射实现，如JAXB，Castor，XMLBeans，JiBX和XStream。

spring-jms模块（Java消息服务）包含用于生成和使用消息的功能。从Spring Framework 4.1开始，它提供了与spring-messaging模块的集成 。

#### Web开发组件

网络 组件 包括 spring-web，spring-webmvc，spring-websocket，和 spring-webmvc-portlet模块。

spring-web模块提供基本的面向Web的集成功能，例如多文件上传下载功能以及使用Servlet监听器和面向Web的应用程序上下文初始化IoC容器。它还包含一个HTTP客户端以及Spring的远程支持的Web相关部分。

spring-webmvc模块（也称为Web-Servlet模块）包含用于Web应用程序的Spring的模型 - 视图 - 控制器（MVC）和REST Web服务实现。Spring的MVC框架提供了领域模型代码和Web表单之间的清晰分离，可以和Spring Framework的所有其他功能集成在一起。

spring-webmvc-portlet模块（也称为Web-Portlet模块）提供了在Portlet环境中使用的MVC的实现，并实现了基于Servlet的spring-webmvc模块的功能。

#### 测试组件

spring-test模块支持使用JUnit或TestNG对Spring组件进行单元测试和 集成测试。它提供了Spring 的初始化加载ApplicationContext和上下文的缓存。它还提供了可用于独立测试代码的模拟对象访问功能。



