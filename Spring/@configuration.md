#### 1.@Configuration简介

在一个普通类上加一个@Configuration注解，声明这是一个配置类，结果和spring.xml配置一样，可以向容器注册Bean和开启包扫

```java
@Configuration
@ComponentScan(basePackages = {"com.younger.spring.model"})
public class TestConfig {
    @Bean//默认使用方法名，作为该Bean的id
//    @Bean(value="myBook"),自定义Bean的id
    public Book book(){
        return new Book("CN_123_javaConfig");
    }
}
```

等价于:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

   <!--扫描指定的包,如果有多个包，可以用',',';'隔开-->
    <context:component-scan base-package="com.younger.spring.model"/>
    
    <bean id="myBook" class="com.younger.spring.model.Book">
         <!--构造器注入。直接通过构造函数来创建对象-->
         <constructor-arg name="isbn" value="CN_123xml_constructor"/>
         <!--属性注入。先使用默认构造函数(没有使用构造注入的前提下)创建对象，然后通过setter方法设值--->
         <property name="isbn" value="CN_123xml_property"/>
        <!--同时有以上两中方法对同一属性设值，属性注入会覆盖构造注入设值-->
    </bean>
</beans>
```

#### 2.基本用法

一个Bean注入另一个Bean中，这里都是依据类型来注入

```java
@Configuration
public class TestConfig {
    public TestConfig(){
        System.out.println("初始化容器");
    }

    @Bean
    public Book book(){
        return new Book("CN_123_in");
    }
    //1.传参注入
    @Bean
    public Student student(Book book){
        return new Student("young",10,book);
    }
    //2.调用方法注入
    @Bean
    public Student student(){
        //注意这里注入的写法很像方法调用而创建一个新的实例，其实不然，依旧返回的是容器中那个singleton的Bean
        return new Student("young",10,book());
    }
}
```

如果两个Bean不在同一个配置文件怎吗办呢?

1.另一个Bean来自Java配置类，则使用@Import

```java
@Configuration
public class Test2Config {
    @Bean
    public Book book(){
        return new Book("CN_123_From_javaConfig");
    }
}

@Configuration
@Import(Test2Config.class)
public class TestConfig {
    public TestConfig(){
        System.out.println("初始化容器");
    }
    
    //参数注入
    @Bean
    public Student student(Book book){
        return new Student("young",10,book);
    }
}
```

2.另一个Bean来自xml配置文件，则使用@ImportResource

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">
    <bean id="book" class="com.younger.test.model.Book">
        <property name="isbn" value="CN_123_from_XML"/>
    </bean>
</beans>
```

```java
@Configuration
@ImportResource({"classpath:config.xml"})
public class TestConfig {
    public TestConfig(){
        System.out.println("初始化容器");
    }
    //参数注入
    @Bean
    public Student student(Book book){
        return new Student("young",10,book);
    }
}
```

ps：以上所做的其实没必要，只要spring容器中存在另一个Bean，不必要导入配置文件(java,xml),也是可完成注入。另外是依据类型注入的。