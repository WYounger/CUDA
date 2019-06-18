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

GenericFactory<A> factoryA = new GenericFactory<>(A.class);
A a = factoryA.createInstance();

GenericFactory<B> factoryB = new GenericFactory<>(B.class);
B b = factoryB.createInstance();

```

总结

> 1.用Class<T>来申明一个泛型类，在类中可以使用Ｔ来限制参数和返回结果，但不能创建Ｔ的实例对象
>
> 2.在创建泛型类的实例的时候，传入一个确定的类型作为参数

#### 3.泛型方法

```java
public static <T> T addAndReturn(T element, Collection<T> collection){
        collection.add(element);
        return element;
    }
//测试
			 List<String> listStr = new ArrayList<>();
        String resultStr = addAndReturn("abc",listStr);
        System.out.println(resultStr);

        List<Integer> listInteger  = new ArrayList<>();
        int resultInteger = addAndReturn(123,listInteger);
        System.out.println(resultInteger);
/*输出
abc
123
*/
```

总结:

1. 泛型方法可以依据传入参数的类型来感知T的类型
2. 若两种不同的类型的参数则会产生error

#### 4.泛型类的继承

```java
public class MyCollection<E> implements Iterable<E>{

    public Iterator<E> iterator() {
        return new MyIterator<E>();
    }
  
   public boolean hasNext() {  
        //implement...
    }

    public T next() {
        //implement...;
    }

    public void remove() {
        //implement... if supported.
    }
}
//测试
  MyCollection<String> stringCollection = new MyCollection<String>();

    for(String string : stringCollection){
      //...
    }
```

#### 5.通配符

引入:

```java
class A{}
class B extends A{}

List<A> listA = new ArrayList<>();
List<B> listB = new ArrayList<>();
listA = listB;//error List<A>不是List<B>的父类
```

##### 无敌通配符<?>

```java
public void processElements(List<?> elements){
  for(Object o:elements){//这里只能使用Object类型
    //do some thing with o
  }
}

//传入的参数可以为:List<Type>,Type没有任何限制
```

##### <? extends ConcreteClass>（只读，不存）

```java
public void processElements(List<? extends A> elements){
  for(A a:elements){//这里可以使用A类型
    //do some thing with o
  }
}
//参数为List<Type>,Type为A或者A的子类
//需要注意的是只能从elements中取出元素，不能存进元素。因为不能确定List中传入的参数的是A的哪个子类
```

##### <? super ConcreteClass>（只存，不读）

```java
public static void insertElements(List<? super A> list){
    list.add(new A());//当你知道?为A的父类，存入的元素的就可以为A或A的子类
    list.add(new B());
    list.add(new C());
}

List<A> listA = new ArrayList<A>();
insertElements(listA);

List<Object> listObject = new ArrayList<Object>();
insertElements(listObject);

```

