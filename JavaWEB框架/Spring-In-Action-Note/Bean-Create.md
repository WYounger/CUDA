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
@ContextConfiguration(class="pojo.MyConfig")
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

##### 3.通过Javadiamagnetic装配bean

