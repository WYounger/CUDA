#### 1.初识泛型

```java
    List listBefore = new ArrayList();//<==>ArrayLsist<Object>
    listBefore.add("abc");
    listBefore.add(123);
    String str = (String)listBefore.get(0);//强制类型转化
    int num = (int)listBefore.get(1);
    System.out.println(str);
    System.out.println(num);

    List<String> list = new ArrayList<>();
    list.add("str1");
    list.add("str2");
    for (String s : list) {
      System.out.println(s);
    }
```

> 1.在使用泛型后，存入元素，在编译时有类型检查，因为在进行add()操作时，有String类型参数的提示
>
> 2.显然使用泛型的时候，在获取元素不用的显示的强制类型转换
>
> 3.可以限制集合中元素的类型

#### 2.泛型类

```java
class GenericFactory<T>{//泛型工厂
    Class theClass;
    public GenericFactory(Class theClass){
        this.theClass = theClass;
    }
    public T createInstance() throws NoSuchMethodException, IllegalAccessException, InvocationTargetException, InstantiationException {
        return (T)theClass.getConstructor().newInstance();
    }
}

class A{
    public A(){
        System.out.println("创建了一个A对象");
    }
}

class B{
    public B(){
        System.out.println("创建了一个B对象");
    }
}
//测试
/*输出
GenericFactory<A> factoryA = new GenericFactory<>(A.class);
        A a = factoryA.createInstance();

        GenericFactory<B> factoryB = new GenericFactory<>(B.class);
        B b = factoryB.createInstance();
*/

```

总结

> 1.用Class<T>来申明一个泛型类，在类中可以使用Ｔ来限制参数和返回结果，不能创建Ｔ的实例对象
>
> 2.在创建泛型类的实例的时候，传入一个确定的类型作为参数

#### 3.泛型方法

#### 4.通配符