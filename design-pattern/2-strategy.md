> 定义一系列算法，把它们一个个封装起来，并且使它们可以相互替换(变化)。该模式使得算法科独立于使用它的客户程序(稳定)二变化(扩展,子类化)。

```java
public class Test {
    public static void main(String[] args) {
        //创建一个具体的策略对象；可以通过工厂来创建
        ConcreteStrategyA concreteStrategyA = new ConcreteStrategyA();
        new User(concreteStrategyA,new Object()).involeAlgorithm();
    }
}

//定义策略接口
interface Strategy{
    void algorithm(Object context);
}

//具体实现(变化)
class ConcreteStrategyA implements  Strategy{
    @Override
    public void algorithm(Object context) {
        //...
    }
}


class ConcreteStrategyB implements  Strategy{
    @Override
    public void algorithm(Object context) {
        //...
    }
}

class ConcreteStrategyC implements  Strategy{
    @Override
    public void algorithm(Object context) {
        //...
    }
}

//使用
class  User{
    private Strategy strategy;
    private Object context;
    public User(Strategy strategy,Object context){
        this.strategy = strategy;
        this.context = context;
    }

    public void involeAlgorithm(){
        strategy.algorithm(context);
    }
}
```

