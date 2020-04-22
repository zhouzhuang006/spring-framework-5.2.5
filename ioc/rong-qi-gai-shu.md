该`org.springframework.context.ApplicationContext`接口代表Spring IoC容器，并负责实例化，配置和组装Bean。容器通过读取配置元数据来获取有关要实例化，配置和组装哪些对象的指令。配置元数据以XML，Java批注或Java代码表示。它使您能够表达组成应用程序的对象以及这些对象之间的丰富相互依赖关系。

`ApplicationContext`Spring提供了该接口的几种实现。在独立应用程序中，通常创建[`ClassPathXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.5.RELEASE/javadoc-api/org/springframework/context/support/ClassPathXmlApplicationContext.html)或的实例[`FileSystemXmlApplicationContext`](https://docs.spring.io/spring-framework/docs/5.2.5.RELEASE/javadoc-api/org/springframework/context/support/FileSystemXmlApplicationContext.html)。尽管XML是定义配置元数据的传统格式，但是您可以通过提供少量XML配置来声明性地启用对这些其他元数据格式的支持，从而指示容器将Java注释或代码用作元数据格式。

在大多数应用场景中，不需要显式的用户代码来实例化一个Spring IoC容器的一个或多个实例。例如，在Web应用程序场景中，应用程序文件中的简单八行（约）样板Web描述符XML`web.xml`通常就足够了（请参阅[Web应用程序的便捷ApplicationContext实例化](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#context-create)）。如果您使用[Spring Tools for Eclipse](https://spring.io/tools)（Eclipse支持的开发环境），则只需单击几下鼠标或击键即可轻松创建此样板配置。

下图显示了Spring的工作原理的高级视图。您的应用程序类与配置元数据结合在一起，以便在`ApplicationContext`创建和初始化之后，您便拥有了一个完全配置且可执行的系统或应用程序。

![](/assets/import.png)

图1. Spring IoC容器

#### 配置元数据 {#beans-factory-metadata}

如上图所示，Spring IoC容器使用一种形式的配置元数据。此配置元数据表示您作为应用程序开发人员如何告诉Spring容器实例化，配置和组装应用程序中的对象。

传统上，配置元数据以简单直观的XML格式提供，这是本章大部分内容用来传达Spring IoC容器的关键概念和功能的内容。



> 基于XML的元数据不是配置元数据的唯一允许形式。Spring IoC容器本身与实际写入此配置元数据的格式完全脱钩。如今，许多开发人员为他们的Spring应用程序选择

有关在Spring容器中使用其他形式的元数据的信息，请参见：

* [基于注释的配置](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#beans-annotation-config)：Spring 2.5引入了对基于注释的配置元数据的支持。

* [基于Java的配置](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#beans-java)：从Spring 3.0开始，Spring JavaConfig项目提供的许多功能成为核心Spring Framework的一部分。因此，您可以使用Java而不是XML文件来定义应用程序类外部的bean。要使用这些新功能，请参阅[`@Configuration`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Configuration.html)，[`@Bean`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Bean.html)，[`@Import`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/Import.html)，和[`@DependsOn`](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/DependsOn.html)注释。

Spring配置由容器必须管理的至少一个（通常是一个以上）bean定义组成。基于XML的配置元数据将这些bean配置为`<bean/>`顶级元素内的`<beans/>`元素。Java配置通常`@Bean`在`@Configuration`类中使用带注释的方法。

这些bean定义对应于组成应用程序的实际对象。通常，您定义服务层对象，数据访问对象（DAO），表示对象（例如Struts`Action`实例），基础结构对象（例如Hibernate`SessionFactories`，JMS`Queues`等）。通常，不会在容器中配置细粒度的域对象，因为创建和加载域对象通常是DAO和业务逻辑的职责。但是，您可以使用Spring与AspectJ的集成来配置在IoC容器的控制范围之外创建的对象。请参阅[使用AspectJ通过Spring依赖注入域对象](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#aop-atconfigurable)。

以下示例显示了基于XML的配置元数据的基本结构：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="..." class="...">  
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <bean id="..." class="...">
        <!-- collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions go here -->

</beans>
```



* 该`id`属性是标识单个bean定义的字符串。

* 该\`class\` 属性定义bean的类型， 并使用完全限定的类名。

该`id`属性的值是指协作对象。在此示例中未显示用于引用协作对象的XML。有关更多信息，请参见



































