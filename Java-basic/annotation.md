### 自定义注解

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD,ElementType.FIELD})
public @interface Message {
    String value() default "";
    int num() default 0;
}
```

上面是一个简单的自定义注解例子。

#### 元注解

##### @Documented

用于标记在生成javadoc时是否将注解包含进去

##### @Target:

定义注解用在什么地方

eg:

```java
//使用该元注解的注解可以使用在类的方法和域上
@Target({ElementType.METHOD,ElementType.FIELD})
```

更多取值，如下

```java
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,

    /** Field declaration (includes enum constants) */
    FIELD,

    /** Method declaration */
    METHOD,

    /** Formal parameter declaration */
    PARAMETER,

    /** Constructor declaration */
    CONSTRUCTOR,

    /** Local variable declaration */
    LOCAL_VARIABLE,

    /** Annotation type declaration */
    ANNOTATION_TYPE,

    /** Package declaration */
    PACKAGE,

    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

##### @Inherited

允许子类继承父类的中的注解，可以通过反射获得父类的注解

##### @Retention

定义注解的声明周期

eg: 

```java
//保持到运行时
@Retention(RetentionPolicy.RUNTIME)
```

所有取值

```java
public enum RetentionPolicy {
    /**
     * Annotations are to be discarded by the compiler.
     */
    SOURCE, //源码阶段

    /**
     * Annotations are to be recorded in the class file by the compiler
     * but need not be retained by the VM at run time.  This is the default
     * behavior.
     */
    CLASS, //编译阶段

    /**
     * Annotations are to be recorded in the class file by the compiler and
     * retained by the VM at run time, so they may be read reflectively.
     *
     * @see java.lang.reflect.AnnotatedElement
     */
    RUNTIME  //运行阶段
}
```

##### 例子

```java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD,ElementType.FIELD})
public @interface Message {
    String value() default "";
    int num() default 0;
}

public class MyTest {
    @Test
    public void test() throws ClassNotFoundException {
        //通过反射获取A的class对象
        Class clazz = Class.forName("annotation.A");
        //获取所有方法
        Method[] methods = clazz.getDeclaredMethods();
        for(Method method : methods){
            //判断方法是否被指定的注解所注解
            if(method.isAnnotationPresent(Message.class)){
                //获取注解对象
                Message annotation = method.getAnnotation(Message.class)
                //使用注解对象的信息
                System.out.println(annotation.value() + " " + annotation.num());
            }
        }
    }
}
class A{
    @Message("methodA-空函数")
    public void methodA(){
    }

    @Message(value="methodB-有内容的函数",num = 1)
    public void methodB(){
    }
}
```

举一个实际一点的例子

定义一个注解来实现controller method的否需要登陆校验

````java
//自定义注解
@Retention(RetentionPolicy.RUNTIME)
@Target(value = {ElementType.METHOD})
public @interface IgnoreLogin {
}

//自定义拦截器
public class WebLoginInterceptor implements HandlerInterceptor {
     @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        //..............
        // 获取注解
        HandlerMethod handlerMethod = (HandlerMethod) handler;
        Method method = handlerMethod.getMethod();
        
        IgnoreLogin ignoreLogin = method.getAnnotation(IgnoreLogin.class);
        if (!Objects.isNull(ignoreLogin)) {
            // 无需验证
            return true;
        }
        //..............
    }
}
````

