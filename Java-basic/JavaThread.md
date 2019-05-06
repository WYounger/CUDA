##### 

一、线程实现的方式

##### 1.继承Thread类

##### 2.实现Runnable接口

##### 3.实现Callable接口

二、继承Thread类

```java
class MyThread extends Thread{
    @Override
    public void run() {
        System.out.println("由Thread创建");
    }
}

//调用
Thread thread = new MyThread();
thread.start();
```

三、实现Runnable接口

```java
				Runnable run = new Runnable() {
            @Override
            public void run() {
                System.out.println("我是由run创建");
            }
        };
        Thread thread = new Thread(run);
        thread.start();
```

四、分析继承Thread类和实现Runnable接口的区别

1. 源码查看

```java
//Runnale只有一个方法
public interface Runnable {
    public abstract void run();
}

public class Thread implements Runnable {
    /**...*/
    /* What will be run. */
    private Runnable target;
  
    private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize, AccessControlContext acc,
                      boolean inheritThreadLocals) {
       /**...*/
       this.target = target;
        
    }
  	@Override
    public void run() {
        if (target != null) {
            target.run();
        }
    }
}
```

由上可知，Thread实现了Runnable接口。

当我们直接实现Thread类并重写run()时，调用start()会调用我们编写的run()方法。

当我们直接实现Runnable接口重写run()，并将实例传递给Thread构造函数,完成了`this.target = target;`,当thread对象调用start()时，会调用target.run()。

两种方法的比较:
继承Thread的class，显然不能再继承其他类，不利于扩展

实现Runnable接口的class，依旧可以继承其他类