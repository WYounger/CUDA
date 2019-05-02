#### 1.Class

##### 1.获取Class对象

```java
Class clazz = ClassName.class;
Class clazz = object.getClass();
Class clazz = Class.forName("class全限定名");
```

##### 2.获取内容

```java
clazz.getName();//类全限定名
clazz.getSimpleName();//类名
Class[] interfaces = clazz.getInterfaces();
Contructor[] contructors = clazz.getContrutors();
Method[] methods = clazz.getMethods();
Field[] fields  =  clazz.getFields();
```

#### 3.Contructor

##### 1.获取构造函数对象

```java
Constructor[] constructors = clazz.getConstructors();
//通过构造函数的参数来获取确切的构造函数
Contructor contructor = clazz.getContructor(new Class[]{Parameter.class...});
```

##### 2.获取构造函数参数

```java
int count = constructor.getParameterCount();
Class[] parameterTypes = constructor.getParameterTypes();
Parameter[] parameters  = constructor.getParameters();
```

##### 3.实例化对象

```java
Constructor constructor = MyObject.class.getConstructor(new Class[]{...});
MyObject myObject = (MyObject)constructor.newInstance(parameters);
//或者
Constructor<MyObject> constructor = MyObejct.class.getConstructor(				  																													new Class[]{Parameter.class...});
MyObject myObject = constructor.newInstance(parameters);
```

#### 4.Field

##### 1.获取Field

```java
Field[] fields = clazz.getFields();
Field field = clazz.getField("fieldName");
```

##### 2.获取内容对象

```java
String fieldName = field..getName();
Class<?> type = field.getType();

//存取对象的成员field值
Class  aClass = MyObject.class
Field field = aClass.getField("someField");
MyObject objectInstance = new MyObject();
Object value = field.get(objectInstance);//instance
field.set(objetInstance, value);//instance
//若field为static变量
Field field = clazz.getField("staticField");
field.set(null,newValue);//instance为null
Object o = field.get(null);//instance为null
```

#### 5.Method

##### 1.获取Method对象

```java
Method[] methods = clazz.getMethods();
Method method = clazz.getMethod("methodName",new Class[]{Parameter.class...});
Method method = clazz.getMethod("methodMame",null);//当method没有参数必须要传入null
```

##### 2.获取内容

```java
Parameter[] parameters = method.getParameters();
Class[] parameterTypes = method.getParameterTypes();
Class returnType = method.getReturnType();

//方法调用
method.invoke(instance,paramters);
//调用staticMethod，instance=null;method没有参数,parameters=null
```

#### 6.获取私有Field和Method

对私有的东西进行反射，颇受争议。因为将属性或方法设置为`private`就是不让外界知道，然而通过反射可以获取的，并可以设置为可访问

```java
class A{
    private String name;
    public A(){
        name = "Tom";
    }
    private String getName(){
        return name;
    }
}
				
				Class clazz = A.class;
        Field field = clazz.getDeclaredField("name");
        field.setAccessible(true);//设置访问权限
        A a = new A();
        field.set(a,"Bob");//设值
        String newName = (String) field.get(a);//取值
        System.out.println(newName);

        Method method = clazz.getDeclaredMethod("getName",null);
        method.setAccessible(true);//设置访问权限
        String resultName =(String) method.invoke(a,null);//调用方法
        System.out.println(resultName);

```

