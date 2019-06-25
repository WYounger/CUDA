> 定义一个操作中的算法的骨架(稳定),而将一些步骤延迟(变化)到子类。Temolate Method是的子类可以在不改变(复用)一个算法的结构即可重定义该算法的某些特定步骤

==早期代码调用晚期代码==

```java
public class Test {
    public static void main(String[] args) {
        B b = new B();
        b.templateMethod();
    }
}

abstract class A{
    //稳定方法
    private void operate1(){
        //...
    }
    //稳定方法
    private void operate2(){
        //...
    }
    //不稳定方法
    public abstract  void operate3();

    //模板方法，逻辑流程稳定，但要调用一个不稳定的方法
    public void templateMethod(){
        //例如内部逻辑如下
        operate1();
        operate2();
        operate3();
    }
}

class B extends A{
    @Override
    public void operate3() {
        //...
        System.out.println("operate3");
    }
}
```

稳定代码调用不稳定代码，将不稳定代码延迟到子类实现