##### 1.装配方式

1. 在xml中显示配置
2. 在Java中显示配置
3. 隐式的bean发现机制和自动装配

##### 2.自动化装配bean

实现方式:

组件扫描:spring 会自动发现应用上下文中所创建的Bean

自动装配:spirng自动满足bean之间的依赖关系

创建Bean

```java
@Component//id为默认名称类名首字母小写
//@Component("customerClassID") 自定义id
class A{}
```

启用组件扫描

第一种方式Java配置

```java
@Configuration
@ComponentScan//默认扫描同一个包的bean
//@ComponentScan(basePackages={"package1","package2") 扫描多个包
//@ComponentScan(basePackageClasses={"package1.Class1","package2.Class2") 扫描Class1和Class2所在的包
public class MyConfig{}
```

第二种方式 xml配置

```xml
<context:component-scan base-package="pojo"/>
```

组件扫描器会发现带有@Component注解的bean，然后spring容器会管理这些bean.

测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes="pojo.MyConfig")
//@ContextConfiguration(locations={"xml1","xml2"})
public class Test_{
  @Autowired//由容器自动注入A的对象
  private A a;
  
  @Test
  public void testNull(){
    assert(a,null);
}
}
```

##### 3.通过Java代码装配bean

当需要使用第三方库的时候，没有办法再他们的类上使用注解，那么就必须进行显示配置(xml,java代码)

声明简单的bean

```java
@Configuation//声明这是一个配置类
public class MyConfig{
  @Bean//将方法的返回的返回的对象注册为sping应用上下文中的bean
  //默认情况下，bean的ID与带有@Bean注解的方法名是一样的
  //当然可以使用@Bean(name="customerID")来实现自定义
  public A a(){
    //产生bean实例的逻辑
    return new A();
  }
}
```

借助JavaConfig实现注入

当B依赖于A时，那么如何将A注入到B中呢?

第一种方式

```java
@Bean
public B b(){
  return B(a());//这里调用a()会传入一个A的Beam，并非真实调用a()，因为bean默认单例的
}
```

第二种方式

```java
@Bean
public B (A a){//容器自动注入A的实例
  这里a不必与B在同一个配置类中，它可以是其他三种方式在容器中注册的bean
  //1.通过构造注入
  return new B(a);
  //2.通过set注入
  B b = new B();
  b.setA(a);
  return b;
}
```

##### 4.通过xml配置

[查看](https://github.com/WYounger/Java/blob/master/JavaWEB%E6%A1%86%E6%9E%B6/Spring.md)





