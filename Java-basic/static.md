#### 1.代码的执行顺序

```java
class A{
  static{
    System.out.println("A static block");
  }
  {
    System.out.println("A constructor block");
  }
  public A(){
    System.out.println("A constructor method");
  }
}
//测试
A a1 = new A();
A a2 = new A();
/*输出
A static block
A constructor block
A constructor method
A constructor block
A constructor method
*/
```

结论:

> 1.多次创建对象，静态代码块只执行一次
>
> 2.构造代码块和构造函数在每次创建对象都会调用
>
> 3.执行顺序:首次执行 静态代码块>构造代码块>构造函数
>
> 4.用法:静态代码块用来初始化类对象的信息，所有实例对象公用
>
> ​	    构造代码块用来初始化不同对象实例的相同的属性值的属性
>
> ​	    构造函数用来初始化不同对象实例的不同属性值的属性

那么继承又是怎么执行的呢?

```java
class B extends A{
  static{
    System.out.println("B static block");
  }
  {
    System.out.println("B constructor block");
  }
  public B(){
    System.out.println("B constructor method");
  }
}
//测试
B b1 = new B();//(1)
B b2 = new B();//(2)
/*输出
(1)执行的结果
A static block
B static block
A constructor block
A constructor method
B constructor block
B constructor method
//执行(1)后执行(2)的结果
A constructor block
A constructor method
B constructor block
B constructor method
*/
```

结论

> 1.执行顺序  
>
> ​       父类静态代码块>子类静态代码块>父类构造代码块>父类构造函数>子类构造代码块>子类构造函数
>
> 2.静态代码块执行一次

2.静态变量

> 1.所有类实例对象的共用，在代码块加载时存入方法区，且只有一份
>
> 3.类直接调用，不同需要创建实例对象

3.静态函数

> 1.在加载时进入方法区，不依赖实例对象
>
> 2.方法体内不能有成员变量和成员函数，因为他们是隐式this.调用
>
> 3.反过来，成员函数可以调用静态函数
>
> 4.类是直接调用，不用创建实例对象