# Spring框架常问面试题

- 一般问题
  - 什么是 Spring Framework
  - 不同版本的 Spring Framework 有哪些主要功能？
  - Spring Framework 有哪些不同的功能？
  - 列举Spring Framework 的优点。
  - Spring Framework框架中用到了哪些设计模式？
  - 什么是 Spring 配置文件？
  - Spring 应用程序有哪些不同的组件？
  - 使用 Spring 有哪些方式？
- Beans
  - 什么是Spring Bean
  - Spring提供了哪些配置方式？
  - Spring支持几种 Bean Scope？
  - Spring Bean容器的生命周期是什么样的？
  - 什么是Spring 的内部 Bean？
  - Spring中的单例Bean的线程安全问题
  - 什么是Spring装配
  - 自动装配有哪些方式？
  - 自动装配有什么局限？
- 依赖注入(DI)
  - 控制反转(IOC)和依赖注入(DI)
  - 什么是依赖注入(DI)？
  - Spring IOC实现原理
  - 可以通过多少种方式完成依赖注入？
  - 区分构造函数注入和Setter注入？
  - Spring循环依赖的三种方式
  - Spring中有多少中IOC容器？
  - 区分BeanFactory和ApplicationContext。
  - 列举IOC的一些好处。
  - Spring Ioc的实现机制。
- 注解
  - 你用过哪些重要的Spring注解？
  - 如何在Spring中启动注解装配？
  - @Component，@Controller，@Repository，@Service有何区别？
  - @RestController与@Controller的区别
  - @Component和@Bean的区别
  - 将一个类声明为Spring的Bean的注解有哪些？
  - @Required注解有什么用？
  - @Autowired注解有什么用？
  - @Qualifier注解有什么用？
  - @RequestMapping注解有什么用？
- 数据访问
  - Spring DAO有什么用？
  - 列举Spring DAO抛出的异常？
  - Spring JDBC API 中存在哪些类？
  - 使用 Spring 访问 Hibernate 的方法有哪些？
  - Spring 支持哪些 ORM 框架
  - 使用 Spring 支持的事务管理类型
  - Spring管理事务的方式有几种？
  - Spring事务中的隔离级别有哪几种？
  - Spring事务中哪几种事务传播行为？
  - @Transactional(rollbackFor=Exception.class)注解了解吗？
- AOP切面编程
  - 什么是AOP？
  - Spring AOP实现原理
  - AOP中的Asepect、Advice、Pointcut、JoinPoint和Advice参数分别是什么？
  - 什么是通知(Advice)？
  - 有哪些类型的通知（Advice）？
  - 指出在Spring AOP 中 concern 和cross-cutting concern 的不同之处。
  - AOP有哪些实现方式？
  - Spring AOP and AspectJ AOP 有什么区别？
  - 如何理解 Spring 中的代理？
  - 什么是编织(Weaving)？
- MVC
  - Spring MVC 框架有什么用？
  - 描述一下 DispatcherServlet 的工作流程
  - 介绍一下 WebApplicationContext
  - Spring MVC中常用的注解有哪些？



### 一、一般问题

1. 什么是 Spring Framework

    Spring是一个开源应用框架，指在降低应用程序开发的复杂度。
    它是轻量级、松散耦合的。
    它具有分层体系结构，允许用户选择组件，同时还为J2EE应用程序开发提供了一个有凝聚力的框架。
   它可以继承其它框架，如Struct2、Hibernate。EJB等，所以又称为框架的框架。

2. 不同版本的 Spring Framework 有哪些主要功能？
    |Version|Feature|
    |---|---|
    |Spring 2.5| 发布于2007年。这是第一个支持注解的版本。|
    |Spring 3.0| 发布于2009年。它完全利用了Java5中的改进，并为JEE6提供了支持。|
    |Spring 4.0| 发布于2013年。这是第一个完全支持JAVA8的版本。|

3. Spring Framework 有哪些不同的功能？
    轻量级 - Spring在代码量和透明度方面都很轻便。
    IOC - 控制反转
    AOP - 面向切面编程可以将应用业务逻辑和系统服务分离，以实现高内聚。
    容器 - Spring 负责创建和管理对象(Bean)的声明周期和配置。
    MVC - 对WEB应用提供了高度可配置性，其它框架的继承业务十分方便。
    事务管理 - 提供了用于事务管理的通用抽象层。Spring的事务支持也可用于容器较少的环境。
    JDBC异常 - Spring的JDBC抽象层提供了一个异常层次结果，简化了错误处理策略。
    
4. 列举Spring Framework 的优点。
    由于Spring Framework的分层架构，用户可以自由选择自己需要的组件。
    Spring Framework支持 POJO(Plan Old Java Object)编程，从而具备持续集成和可测试性。
    由于依赖注入和控制反转，JDBC得以简化。
    它是开源免费的。
    
5. Spring Framework框架中用到了哪些设计模式？
    工厂设计模式：SPring使用工厂模式通过BeanFactory、ApplicationContext创建bean对象。
    代理设计模式：Spring AOP功能的实现。
    单例设计模式：SPpring中的Bean默认都是单例的。
    模板方法模式：Spring中jdbcTemplate、hibernateTempate等以Template结尾的对数据库操作的类，它们就使用到了模板模式。
    包装器设计模式：我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
    观察者模式：Spring事件驱动模型就是观察者模式很经典的一个应用。
    适配器模式：Spring AOP的增强或通知(Advice)使用到了适配器模式、Spring MVC中也是用到的适配器模式适配Controller。
    装饰者模式：
    
6. 什么是 Spring 配置文件？
    Spring配置文件是XML文件。该文件主要包含类信息。它描述了这些类是如何配置以及相互引入的。但是，XML配置文件冗长且更加干净。如果没有正确规划和编写，那么在大型项目中管理变得非常困难。
    
7. Spring 应用程序有哪些不同的组件？
    ![avator](./images/0038204c-4b8a-42a5-921d-080f6674f989.png)
    Spring Container - 该层基本上是 Spring Framework的核心。它包含以下模块：
     - Spring Core
     - Spring Bean
     - SpEL(Spring Expression Language)
     - Spring Context
    数据访问/集成 - 该层提供与数据库交互的支持。它包含以下模块：
      - JDBC(Java Database Connectivity)
      - ORM(Object Relational Mapping)
      - OXM(Object XML Mappers)
      - JMS(Java Messaging Service)
      - Transaction
     WEB - 该层提供了创建Web应用程序的支持。它包含以下模块：
      - Web
      - Web - Servlet
      - Web - Socket
      - Web - Portlet
     AOP - 该层支持面向切面编程
     Instrumentation - 该层为类检测和类加载器实现提供支持。
     Test - 该层为使用 JUnit 和 TestNG 进行测试提供支持。
     几个杂项模块：
      - Messaging - 该模块为STOMP提供支持。它还支持注解编程模型，该模型用于从WebSocket客户端路由和处理STOMP消息。
      - Aspects - 该模块为与AspectJ的集成提供支持。

8. 使用 Spring 有哪些方式？
    使用Spring有以下方式：
     - 作为一个成熟的Spring Web应用程序。
     - 作为第三方Web框架，使用Spring Framework中间层。
     - 用于远程使用。
     - 作为企业级Java Bean，它可以包装现有的POJO(Plain Old Java Objects)。

### 二、Beans
1. 什么是Spring Bean？
    它们是构成用户应用程序主干的对象。
    Bean由Spring IoC容器管理。
    它们由Spring IoC容器实例化，配置，装配和管理。
    Bean是基于用户提供给容器的配置元数据创建。
    
2. Spring提供了哪些配置方式？
    基于XML配置
      Bean所需的依赖项和服务在XML格式的配置文件中指定。这些配置文件同行包含许多bean定义和特定于应用程序的配置选项。它们通常以bean标签开头。例如：
    
    ```xml
<bean id="studentbean" class="org.edureka.firstSpring.StudentBean">
		<property name="name" value="Edureka"></property>
    </bean>
    ```
    基于注解配置
      您可以通过在相关的类，方法或字段声明上使用注解，将 bean 配置为组件类本身，而不是使用 XML 来描述 bean 装配。默认情况下，Spring 容器中未打开注解装配。因此，您需要在使用它之前在 Spring 配置文件中启用它。例如：
      ```xml
    <beans>
	<context:annotation-config/>
	<!-- bean definitions go here -->
</beans>
      ```
    基于Java API配置
      Spring的Java配置是用过使用@Bean和@Configuration来实现。
      1. @Bean注解扮演与元素相同的角色。
      2. @Configuration类允许通过简单地调用同一个类中的其它@Bean方法来定义bean间的依赖关系。
    
        例如：
      ```java
@Configuration
    public class StudentConfig {
    	@Bean
    	public StudentBean myStudent() {
        	return new StudentBean();
		}
    }
      ```
    
3. Spring支持几种 Bean Scope？
    Spring Bean支持5中Scope：
      1. Singleton：每个Spring IoC容器仅有一个单实例。
      2. Prototype：每次请求都会产生一个新的实例。
      3. Request：每一次HTTP请求都会产生一个新的实例，并且该Bean仅在当前HTTP Session内有效。
      4. Global-Session：类似于标准的HTTP Session作用域，不过它不仅仅在基于protlet的web应用中才有意义。Portlet规范定义了全局Session的概念，它被所有构成某个portlet web应用的各种不同的 portlet 所共享。在 global session 作用域中定义的 bean 被限定于全局 portlet Session 的生命周期范围内。如果你在 web 中使用 global session 作用域来标识 bean，那么 web 会自动当成 session 类型来使用。
    
        仅当用户使用支持Web的ApplicationContext时，最后三个才可用。
    
4. Spring Bean容器的生命周期是什么样的？
    Spring Bean容器的生命周期流程如下：
      1. Spring容器根据配置中的bean定义中实例化bean。
      2. Spring使用依赖注入填充所有属性，如bean中所定义的配置。
      3. 如果bean实现BeanNaneAware接口，则工厂通过传递bean的ID来调用setBeanName()。
      4. 如果bean实现BeanFactoryAware接口，工厂通过传递自身的实例来调用setBeanFactory()。
      5. 如果存在于bean关联的任何BeanPostProcessors，则调用preProcessBeforeInitialization()方法。
      6. 如果为bean指定了init方法(init-method属性)，那么将调用它。
      7. 最后，如果存在与bean关联的任何BeanPostProcessors，则将调用postProcessAfterInitialization()方法。
      8. 如果bean实现DisposableBean接口，当Spring容器关闭时，会调用destory()。
      9. 如果为bean指定了destroy方法(destroy-method属性)，那么将调用它。
    
        ![avator](./images/0038204c-4b8a-42a5-921d-080f6674f990.png)
    
5. 什么是Spring 的内部 Bean？
    只有将bean用作另一个bean的属性时，才能将bean声明为内部bean。为了定义bean，Spring的基于XML的配置元数据在<property>或<constructor-arg>中提供了<bean>元素的使用。内部bean总是匿名的，它们总是作为原型。
    例如，假设我们有一个Student类，其中引用了Person类。这里我们将只创建一个Person类实例并在Student中使用它。
    Student.java
    ```java
    public class Student {
    	private Person person;
    	//Setters and Getters
}
public class Person {
    	private String name;
    	private String address;
    	//Setters and Getters
}
    ```
    bean.xml
    ```xml
    <bean id=“StudentBean" class="com.edureka.Student">
    <property name="person">
        <!--This is inner bean -->
        <bean class="com.edureka.Person">
            <property name="name" value=“Scott"></property>
            <property name="address" value=“Bangalore"></property>
        </bean>
    </property>
</bean>
```
    
6. Spring中的单例Bean的线程安全问题
    大部分时候我们并没有在系统中使用多线程，所以很少有人会关注这个问题。单例bean存在线程问题，主要是因为当多个线程操作同一个对象的时候，对这个对象的非静态成员变量的写操作会存在线程安全问题。
    常见的有两种解决办法：
      1. 在Bean对象中尽量避免定义可变的成员变量(不太现实)。
      2. 在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中(推荐的一种方式。)
    
    
7. 什么是Spring装配
    当bean在Spring容器中组合在一起时，它被称为装配或bean装配。Spring容器需要知道需要什么bean以及容器应该如何使用依赖注入将bean绑定在一起，同时装配bean。
    
8. 自动装配有哪些方式？
    Spring 容器能够自动装配 bean。也就是说，可以通过检查 BeanFactory 的内容让 Spring 自动解析 bean 的协作者。
    自动装配的不同模式：
      - no - 这是默认设置，表示没有自动装配。应使用显式 bean 引用进行装配。
      - byName - 它根据 bean 的名称注入对象依赖项。它匹配并装配其属性与 XML 文件中由相同名称定义的 bean。
      - byType - 它根据类型注入对象依赖项。如果属性的类型与 XML 文件中的一个 bean 名称匹配，则匹配并装配属性。
      - 构造函数 - 它通过调用类的构造函数来注入依赖项。它有大量的参数。
      - autodetect - 首先容器尝试通过构造函数使用 autowire 装配，如果不能，则尝试通过 byType 自动装配。
    
9. 自动装配有什么局限？
    - 覆盖的可能性 - 您始终可以使用和设置指定依赖项，这将覆盖自动装配。
    - 基本原数据类型 - 简单属性(如原数据类型，字符串和类)无法自动装配。
    - 令人困惑的性质 - 总是喜欢使用明确的装配，因为自动装配不太精确。

### 三、依赖注入(DI)
1. 控制反转(IOC)和依赖注入(DI)
2. 什么是依赖注入(DI)？
3. Spring IOC实现原理
4. 可以通过多少种方式完成依赖注入？
5. 区分构造函数注入和Setter注入？
6. Spring循环依赖的三种方式
7. Spring中有多少中IOC容器？
8. 区分BeanFactory和ApplicationContext。
9. 列举IOC的一些好处。
10. Spring Ioc的实现机制。

### 四、注解
1. 你用过哪些重要的Spring注解？
2. 如何在Spring中启动注解装配？
3. @Component，@Controller，@Repository，@Service有何区别？
4. @RestController与@Controller的区别
5. @Component和@Bean的区别
6. 将一个类声明为Spring的Bean的注解有哪些？
7. @Required注解有什么用？
8. @Autowired注解有什么用？
9. @Qualifier注解有什么用？
10. @RequestMapping注解有什么用？


### 五、数据访问
  - Spring DAO有什么用？
  - 列举Spring DAO抛出的异常？
  - Spring JDBC API 中存在哪些类？
  - 使用 Spring 访问 Hibernate 的方法有哪些？
  - Spring 支持哪些 ORM 框架
  - 使用 Spring 支持的事务管理类型
  - Spring管理事务的方式有几种？
  - Spring事务中的隔离级别有哪几种？
  - Spring事务中哪几种事务传播行为？
  - @Transactional(rollbackFor=Exception.class)注解了解吗？
- AOP切面编程
  - 什么是AOP？
  - Spring AOP实现原理
  - AOP中的Asepect、Advice、Pointcut、JoinPoint和Advice参数分别是什么？
  - 什么是通知(Advice)？
  - 有哪些类型的通知（Advice）？
  - 指出在Spring AOP 中 concern 和cross-cutting concern 的不同之处。
  - AOP有哪些实现方式？
  - Spring AOP and AspectJ AOP 有什么区别？
  - 如何理解 Spring 中的代理？
  - 什么是编织(Weaving)？
- MVC
  - Spring MVC 框架有什么用？
  - 描述一下 DispatcherServlet 的工作流程
  - 介绍一下 WebApplicationContext
  - Spring MVC中常用的注解有哪些？
```

```

  ```

  ```