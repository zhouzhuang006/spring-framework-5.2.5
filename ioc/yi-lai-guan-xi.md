## 依存关系

典型的企业应用程序不包含单个对象（或Spring的bean）。即使是最简单的应用程序，也有一些对象可以协同工作，以呈现最终用户视为一致的应用程序。下一部分将说明如何从定义多个独立的Bean定义到实现对象协作以实现目标的完全实现的应用程序。

### 依赖注入

依赖注入（DI）是一个过程，通过该过程，对象只能通过构造函数参数，工厂方法的参数或在构造或创建对象实例后在对象实例上设置的属性来定义其依赖关系（即，与它们一起工作的其他对象）。从工厂方法返回。然后，容器在创建bean时注入那些依赖项。从本质上讲，此过程是通过使用类的直接构造或服务定位器模式来控制其依赖关系的实例化或位置的Bean本身的逆过程（因此称为控制反转）。

使用DI原理，代码更加简洁，当为对象提供依赖项时，去耦会更有效。该对象不查找其依赖项，也不知道依赖项的位置或类。结果，您的类变得更易于测试，尤其是当依赖项依赖于接口或抽象基类时，它们允许在单元测试中使用存根或模拟实现。

DI存在两个主要变体：[基于构造函数的依赖注入](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#beans-constructor-injection)和[基于Setter的依赖注入](https://docs.spring.io/spring/docs/5.2.5.RELEASE/spring-framework-reference/core.html#beans-setter-injection)。

#### 基于构造函数的依赖注入

基于构造函数的DI是通过容器调用具有多个参数（每个参数代表一个依赖项）的构造函数来完成的。调用`static`带有特定参数的工厂方法来构造Bean几乎是等效的，并且本次讨论将构造函数和`static`工厂方法的参数视为类似。以下示例显示了只能通过构造函数注入进行依赖项注入的类：

```java
public class SimpleMovieLister {

    // the SimpleMovieLister has a dependency on a MovieFinder
    private MovieFinder movieFinder;

    // a constructor so that the Spring container can inject a MovieFinder
    public SimpleMovieLister(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }

    // business logic that actually uses the injected MovieFinder is omitted...
}
```

注意，该类没有什么特别的。它是一个POJO，不依赖于特定于容器的接口，基类或注释。

**构造函数参数解析**

构造函数参数解析匹配通过使用参数的类型进行。如果Bean定义的构造函数参数中不存在潜在的歧义，则在实例化Bean时，在Bean定义中定义构造函数参数的顺序就是将这些参数提供给适当的构造函数的顺序。考虑以下类别：

```java
package x.y;

public class ThingOne {

    public ThingOne(ThingTwo thingTwo, ThingThree thingThree) {
        // ...
    }
}
```

假设`ThingTwo`和`ThingThree`类不是通过继承关联的，则不存在潜在的歧义。因此，以下配置可以正常工作，并且您无需在`<constructor-arg/>`元素中显式指定构造函数参数索引或类型。

```XML
<beans>
    <bean id="beanOne" class="x.y.ThingOne">
        <constructor-arg ref="beanTwo"/>
        <constructor-arg ref="beanThree"/>
    </bean>

    <bean id="beanTwo" class="x.y.ThingTwo"/>

    <bean id="beanThree" class="x.y.ThingThree"/>
</beans>
```

当引用另一个bean时，类型是已知的，并且可以发生匹配（与前面的示例一样）。当使用诸如的简单类型时 &lt;value&gt;true&lt;/value&gt;，Spring无法确定值的类型，因此在没有帮助的情况下无法按类型进行匹配。考虑以下类别：

```java
package examples;

public class ExampleBean {

    // Number of years to calculate the Ultimate Answer
    private int years;

    // The Answer to Life, the Universe, and Everything
    private String ultimateAnswer;

    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

**构造函数参数类型匹配**

在上述情况下，如果通过使用`type`属性显式指定构造函数参数的类型，则容器可以使用简单类型的类型匹配。如以下示例所示：

```XML
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg type="int" value="7500000"/>
    <constructor-arg type="java.lang.String" value="42"/>
</bean>
```

**构造函数参数索引**

您可以使用该`index`属性来明确指定构造函数参数的索引，如以下示例所示：

```XML
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg index="0" value="7500000"/>
    <constructor-arg index="1" value="42"/>
</bean>
```

除了解决多个简单值的歧义性之外，指定索引还可以解决歧义，其中构造函数具有两个相同类型的参数。

> 索引从0开始。

**构造函数参数名称**

您还可以使用构造函数参数名称来消除歧义，如以下示例所示：

```XML
<bean id="exampleBean" class="examples.ExampleBean">
    <constructor-arg name="years" value="7500000"/>
    <constructor-arg name="ultimateAnswer" value="42"/>
</bean>
```

请记住，要立即使用该功能，必须在启用调试标志的情况下编译代码，以便Spring可以从构造函数中查找参数名称。如果您不能或不希望使用debug标志编译代码，则可以使用[@ConstructorProperties](https://download.oracle.com/javase/8/docs/api/java/beans/ConstructorProperties.html)JDK注释显式命名构造函数参数。然后，样本类必须如下所示：

```java
package examples;

public class ExampleBean {

    // Fields omitted

    @ConstructorProperties({"years", "ultimateAnswer"})
    public ExampleBean(int years, String ultimateAnswer) {
        this.years = years;
        this.ultimateAnswer = ultimateAnswer;
    }
}
```

#### 基于Setter的依赖注入

基于设置器的DI是通过在调用无参数构造函数或无参数`static`工厂方法以实例化您的bean之后，在您的bean上调用setter方法来完成的。下面的示例显示只能通过使用纯setter注入来依赖注入的类。此类是常规的Java。它是一个POJO，不依赖于容器特定的接口，基类或注释。









