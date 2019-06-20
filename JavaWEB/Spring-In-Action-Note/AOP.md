### 依赖

```xml
    <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjweaver</artifactId>
			<version>1.9.2</version>
		</dependency>

		<!-- https://mvnrepository.com/artifact/aspectj/aspectjrt -->
		<dependency>
			<groupId>aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>1.5.3</version>
		</dependency>
```



### 1.基于AspectJ

**动态代理实现**，所以动态代理生成的Bean必须采用接口来接收，不能使用目标bean来接收

**AOP利用AspectJ实现的一般步骤**

1. 定义切面
2. 注册切面到spring容器 
3. 开启动态代理
4. 定义目标类
5. 注册目标类到spring容器

MyAspect.class

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@Component//直接声明为组件，然后通过组件扫描注册到容器
@Aspect //声明为切面，java5引入
public class MyAspect {
  
  	//切入点表达式
    //访问权限类型 返回值类型 权限定性类名 方法名(参数名) 抛出异常的类型
    @Pointcut("execution(* model.*.*(..))")
    public void selectAll(){}

    //在目标方法执行前执行
    @Before("selectAll()")
    public void beforeAdvice(){
        System.out.println("beforeAdvice");
    }

    //正常情况下:在目标方法执行完后执行，在目标方法返回前执行
    //异常情况下:仍然会执行,在异常抛出前执行
    @After("selectAll()")
    public void afterAdvice(){
        System.out.println("afterAdvice");
    }

    //在目标方法返回后执行,可以获取到返回值
    //若目标方法没有返回值，则返回值为null
    @AfterReturning(pointcut = "selectAll()",returning = "returnValue")
    public void afterReturningAdvice(Object returnValue){
        System.out.println("AfterReturning:"+returnValue);
    }

    //目标方法抛出异常后执行
    @AfterThrowing(pointcut = "selectAll()",throwing = "ex")
    public void afterThrowingAdvice(Exception ex){
        System.out.println("AfterThrowing"+ex.getMessage());
    }

    //在目标方法执行前和执行后执行
    //可以获取目标方法返回值，且可以修改，然后将结果返回给真正的调用者
    @Around("selectAll()")
    public Object aroundAdvice(ProceedingJoinPoint proceedingJoinPoint){
        System.out.println("aroundAdvice:-----前");
        Object returnValue = null;
        try {
            //执行目标方法
           returnValue = proceedingJoinPoint.proceed();//可以传入目标方法的参数(如果有)
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        System.out.println("aroundAdvice:-----后");
        return returnValue.toString()+"++++";
    }
  
    //内联引入切入点表达式
    @Before("execution(* model.*.*(..))")
    public void before(){
    }
}
```

applicationContext1.xml

```xml
<context:component-scan base-package="model"/>
<!--开启aspectJ自动代理-->
<aop:aspectj-autoproxy/>
```

pojo

```java
package model;
public interface Animal {
    void eat();
    void drink();
    String study();
}

@Component
public class Cat implements Animal {
    public void eat() {
        System.out.println("cat eat before");
        int a = 1/0;
        System.out.println("cat eat after");
    }

    public void drink() {
        System.out.println("cat drink");
    }

    public String study(){
        System.out.println("目标方法执行");
        return "i have learned spring today!";
    }
}
```

测试

```java
 ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("applicationContext1.xml");

Animal cat = (Animal) context.getBean("cat");//获取加强后的bean
cat.study();
```

输出

```bash
beforeAdvice
aroundAdvice:-----前
目标方法执行
aroundAdvice:-----后
AfterReturning:i have learned spring today!++++
afterAdvice
```

### 2.基于XML

**使用常规类和xml配置来实现一般步骤**

1. 定义切面类和目标类

2. 在xml中注册切面类和目标类
3. 在xml中配置`aspect`,`pointcut`,`advice`

切面常规类

```java
package model;
import org.aspectj.lang.ProceedingJoinPoint;
public class MyAspect {

    public void beforeAdvice(){
        System.out.println("beforeAdvice");
    }

    public void afterAdvice(){
        System.out.println("afterAdvice");
    }
  
    public void afterReturningAdvice(Object returnValue){
        System.out.println("AfterReturning:"+returnValue);
    }

    public void afterThrowingAdvice(Exception ex){
        System.out.println("AfterThrowing"+ex.getMessage());
    }

    public Object aroundAdvice(ProceedingJoinPoint proceedingJoinPoint){
        System.out.println("aroundAdvice:-----前");
        Object returnValue = null;
        try {
            //执行目标方法
           returnValue = proceedingJoinPoint.proceed();//可以传入目标方法的参数(如果有)
        } catch (Throwable throwable) {
            throwable.printStackTrace();
        }
        System.out.println("aroundAdvice:-----后");
        return returnValue.toString()+"++++";
    }
}

```

pojo

```java
package model;
public interface Animal {
    void eat();
    void drink();
    String study();
}

public class Cat implements Animal {
    public void eat() {
        System.out.println("cat eat before");
        int a = 1/0;
        System.out.println("cat eat after");
    }

    public void drink() {
        System.out.println("cat drink");
    }

    public String study(){
        System.out.println("目标方法执行");
        return "i have learned spring today!";
    }
}
```

xml配置

```xml
    <!--切面-->
    <bean id="myAspect" class="model.MyAspect"/>

    <!--目标类-->
    <bean id="cat" class="model.Cat"/>

    <aop:config>
        <aop:aspect ref="myAspect">
            <aop:pointcut id="selectAll" expression="execution(* model.*.*(..))"/>
            <aop:before method="beforeAdvice" pointcut-ref="selectAll"/>
            <aop:after method="afterAdvice" pointcut-ref="selectAll"/>
            <aop:after-returning method="afterReturningAdvice" pointcut-ref="selectAll" returning="returnValue"/>
            <aop:after-throwing method="afterThrowingAdvice" pointcut-ref="selectAll" throwing="ex"/>
            <aop:around method="aroundAdvice" pointcut-ref="selectAll" arg-names="proceedingJoinPoint"/>
        </aop:aspect>
    </aop:config>
```

测试

```java
ClassPathXmlApplicationContext context =
                new ClassPathXmlApplicationContext("applicationContext1.xml");

Animal cat = (Animal) context.getBean("cat");
cat.study();
```

输出

```bash
beforeAdvice
aroundAdvice:-----前
目标方法执行
aroundAdvice:-----后
AfterReturning:i have learned spring today!++++
afterAdvice
```

