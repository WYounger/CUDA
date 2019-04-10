### 一.基本IOC

#### 1.IOC

控制反转，是一种编程思想，也称DI。调用者不需要显示创建依赖的对象，而是由容器创建并注入到调用者域中。主要作用是为了解耦。

#### 2.Spring IOC容器

主要基于BeanFactory和ApplicationContext两个接口，Application是BeanFactory的子接口之一，在BeanFactory基础扩展了很多功能.

#### 3.Spring Bean的生命周期

[官方文档](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-nature)

#### 4.Spring Bean的作用域

[官方文档](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-factory-scopes)

### 二.Spring Bean的装配

#### 1.依赖注入的三种方式

注入:给spring容器创建的Bean进行**设值**

##### 1.构造器注入

必须描述具体的类,构造方法并设置对应的参数.容器通过反射来完成对象的创建和初始化工作

```xml
<bean id="beanId" class="Bean">
  <constructor-arg name="name" value="value"/>
  ...
</bean>
```

##### 2.setter注入

利用Java Bean中定义的setter方法来完成注入.首先容器会通过反射调用无参构造方法来创建对象,同时通过反射对应的serrer注入配置的值

```xml
<bean id="beanId" class="Bean">
  <property name="name" value="value"/>
  ...
</bean>
```

##### 3.接口注入

略

#### 2.Bean的装配

装配:**向spring容器注册Bean**,并由容器完成实例化.此后可以采用自动注入的方式使用Bean对象

#### 1.通过XML配置装配

##### 1.含有基本数据类型的装配

```xml
<bean id="beanId" class="Bean的全限定类名">
  <property name="name" value="value"/>
  <property name="name" ref="beanId2"/>
  ...
</bean>
```

##### 2.含有集合的装配

```xml
<bean id="moreComplexObject" class="example.ComplexObject">
    <!-- results in a setSomeList(java.util.List) call -->
    <property name="someList">
        <list>
            <value>a list element followed by a reference</value>
            <ref bean="myDataSource" />
        </list>
    </property>
    <!-- results in a setSomeMap(java.util.Map) call -->
    <property name="someMap">
        <map>
            <entry key="an entry" value="just some string"/>
            <entry key ="a ref" value-ref="myDataSource"/>
        </map>
    </property>
    <!-- results in a setSomeSet(java.util.Set) call -->
    <property name="someSet">
        <set>
            <value>just some string</value>
            <ref bean="myDataSource" />
        </set>
    </property>
   <!-- results in a setAdminEmails(java.util.Properties) call -->
    <property name="adminEmails">
        <props>
            <prop key="administrator">administrator@example.org</prop>
            <prop key="support">support@example.org</prop>
            <prop key="development">development@example.org</prop>
        </props>
    </property>
</bean>
```

#### 2.通过注解自动装配

步骤:

**1.创建组件:**

@Component|@Service|@Repository("别名")标识一个class

**2.扫描(xml|java配置)**

扫描带有@Component|@Service|@Repository注解的Class,注册入容器中

```xml
<context:component-scan base-package="..."/>
```

```java
@Configuration
@ComponentScan(basePackages="...")
public class AppConfig{}
```

**3.注入**

1. **基本数据类型注入**

```java
@value("value")
```

2. **引用类型注入**

   1. @Autowired:依据类型自动注入

   2. 由于提倡面向接口编程编,所以一个接口或抽象类会有多个实现类,所以使用@Autowired注解会找到多个实现类,这时会抛出异常

      解决办法:

      1. 使用@Primary注解,标识实现类.表明首先使用该类注入
      2. @Autowired + @Qualifier("别名")

#### 3.Java配置

[官方文档](https://docs.spring.io/spring-framework/docs/current/spring-framework-reference/core.html#beans-annotation-config)

#### 4.总结

1. 程序系统内部的Bean使用注解形式装配,较为方便
2. 建立第三库Bean时,建议使用xml方式装配
3. 通用配置,例如数据库,使用xml方式装配

### 三.测试

```Java
//第一种方法:引入junit,直接使用ClassPathXmlApplicationContext加载应用上下文
//getBean()获取Bean对象
public class MyTest {
    @Test
    public void test1(){
        //首先必须要加载上下文
        ApplicationContext applicationContext =
                new ClassPathXmlApplicationContext("applicationContext.xml");
        model.Car car = applicationContext.getBean(model.Car.class);
        System.out.println(car);

        Human human = applicationContext.getBean(Human.class);
        System.out.println(human);
    }
}
//第二种方法:需要引入junit和sring-test,使用ContextConfiguration加载应用上下文
//采用自动注入方式获取Bean对象
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class MyTest {
    @Autowired
    Human human;
   
    @Test
    public void test2(){
        System.out.println(human);
    }
}

```

