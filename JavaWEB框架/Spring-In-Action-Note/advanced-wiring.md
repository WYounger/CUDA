##### 1.Profile

当一个bean在不同的环境下，有不同的实例，来满足在不同环境下都可以运行，可以用Profile来实现。当然需要一个激发条件激活的不同状态。

激活profile的方式

1. 作为DispatcherServlet的初始化参数

2. 作为Web应用的上下文参数

3. 作为JNDI

4. 作为环境变量

5. 作为JVM的系统属性

6. 在集成测试类上，使用@ActiveProfiles注解设置

   在web.xml中设置profile

   ```xml
   为上下文设置默认的profile
   <context-param>
   	<param-name>spring.profiles.default</param-name>
     <param-value>dev</param-value>
   </context-param>
   为Servlet设置默认的profile
   <servlet>
     <servlet-name>appServlet</servlet-name>
     <servlet-class>org.springframework.web.servlet.DispatcherServelt</servlet-class>
   	<init-param>
     	<param-name>spring.profiles.default</param-name>
     	<param-value>dev</param-value>
     </init-param>
     <load-on-startop>1</load-on-startop>
   </servlet>
   <servlet-mapping>
   	<servlet-name>appServlet</servlet-name>
     <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```

**java config实现**

```java
定义一个接口，在不同的环境下有不同的实例
public interface Animal {
    void eat();
    void drink();
}
实现接口的两个类
public class Cat implements Animal {
    public void eat() {
        System.out.println("cat eat");
    }

    public void drink() {
        System.out.println("cat drink");
    }
}
public class Dog implements Animal {
    public void eat() {
        System.out.println("Dog eat");
    }

    public void drink() {
        System.out.println("Dog drink");
    }
}
java config
@Configuration
public class MyConfig {

    @Bean
    @Profile("dev")//定义状态
    public Animal cat(){
        return new Cat();
    }

    @Bean
    @Profile("prod")//定义状态
    public Animal dog(){
        return new Dog();
    }
}

测试
@ContextConfiguration(classes = {MyConfig.class})
@ActiveProfiles("dev")//激活profile
public class MyTest {
    @Autowired
    Animal animal;
    @Test
    public void test1() {
        animal.eat();
        animal.drink();
    }
}

```

**xml实现**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">
    <!--两个bean的id可以设置为一样的，但idea会提示错误，但运行是可以通过的-->
    <beans profile="dev">
        <bean id="cat" class="model.Dog"/>
    </beans>

    <beans profile="prod">
        <bean id="dog" class="model.Dog"/>
    </beans>
</beans>

其他代码同上
```

##### 2.条件化的bean

