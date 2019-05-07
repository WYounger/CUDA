##### 1.CountDownLatch--等待多线程完成

```java
//构造方法
CountDonwLatch(int n);
//成员方法
await();//阻塞调用该方法的线程,直至n=0
countDown();//n--
```

由于`countDown()方法可以用在任何地方，所以这里n可以是n个线程或一个线程n个执行步骤。用在多个线程时，只需要将该countDownLatch的引用传递到线程即可

```java
public class ThreadPool_ {
		//初始化countDownLatch为
    static CountDownLatch latch = new CountDownLatch(2);
  
    public static void main(String[] args) throws Exception{
        Thread thread1= new Thread(new MyThread(1,latch));
        Thread thread2 = new Thread(new MyThread(2,latch));
        thread1.start();
        thread2.start();
        latch.await();//当n=0时，当前线程从此处开始执行
        System.out.println("---");
        System.out.println(latch.getCount());
    }
  
    private static class MyThread implements Runnable{
        private int num;
        CountDownLatch latch;
        public MyThread(int num,CountDownLatch latch){
            this.num = num;
            this.latch = latch;
        }
        @Override
        public void run() {
            System.out.println(num);
            //模拟完成任务时间
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            //运行完成,通知:我已经完成任务
            latch.countDown();
        }
    }
}
```

##### 2.CyclicBarrier---同步屏障

字面意思是可循环使用的屏障。它要做的事情是，让一组线程到达一个屏障(也可叫同步点)时被阻塞，直到最后一个线程到达屏障时，屏障才会开门，所有被屏障拦截的线程才会继续运行。

```java
//构造方法
CyclicBarrier(int parties);//屏障拦截的数量
CyclicBarried(int partiesm,Runnable fistAction);//后一个参数是当屏障打开时，首先执行它
await();//通知已近到达屏障，然后当前线程被阻塞
```

当最后到达屏障的线程小于初始化时`parties`的值，那么这些已经到达屏障的线程将永远不会被执行下面的代码

```java
import java.util.concurrent.BrokenBarrierException;
import java.util.concurrent.CyclicBarrier;

public class ThreadPool_ {

    public static void main(String[] args) throws Exception{
        final CyclicBarrier barrier = new CyclicBarrier(2,new Runnable(){
            @Override
            public void run(){
                System.out.println("我是屏障打开后的第一个运行的动作!");
            }
        });
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    //执行任务
                    Thread.sleep(1000);
                    //执行完成后通知并等待
                    barrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
                System.out.println(1);
            }
        }).start();
        //主线程等待
        barrier.await();
        System.out.println(2);
    }
}
```

一个银行查流水的例子

```java
import java.util.Iterator;
import java.util.Map;
import java.util.concurrent.*;

public class Test{
    public static void main(String[] args) {
        //存放一个账户的每一个sheet的总流水
        ConcurrentMap<String,Integer> map = new ConcurrentHashMap<>();
        //构造含有5个线程的线程池
        ExecutorService executor = Executors.newFixedThreadPool(5);
        //让5个线程去查流水
        CyclicBarrier barrier = new CyclicBarrier(5,new Runnable(){
            public void run(){
                //计算最后的结果
                int sum = 0;
                Iterator<Map.Entry<String,Integer>> it = map.entrySet().iterator();
                while(it.hasNext()){
                    Map.Entry<String,Integer> entry = it.next();
                    sum +=entry.getValue();
                }
                System.out.println("总额:"+sum);
            }
        });
        //执行任务
        for (int i = 1;i<=5;i++){
            executor.execute(new CheckSheetWater(map,barrier));
        }
      	executor.shutdown();
    }
}

class CheckSheetWater implements Runnable{
    private ConcurrentMap map;
    private CyclicBarrier barrier;
    public CheckSheetWater(ConcurrentMap map, CyclicBarrier barrier){
        this.map  = map;
        this.barrier =barrier;
    }
    @Override
    public void run() {
        try {
            //完成查账工作
            Thread.sleep(1000);
            //得到结果
            System.out.println();
            Thread thread = Thread.currentThread();
            map.put(thread.getName(),1);//1为结果
            System.out.println(thread.getName()+":"+ 1);
            //发出通知
            barrier.await();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (BrokenBarrierException e) {
            e.printStackTrace();
        }
    }
}
```

##### 3.Semaphore---控制并发线程数量

Semaphore(信号量)用来控制同时访问特定资源的线程数量，让可用协调各个线程，以保证合理的使用公共资源

```java
acquire();//获取许可
release();//释放许可
```

```java

import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Semaphore;

public class Test{
    public static final int POOLSIZE = 20;
    public static void main(String[] args) {
        ExecutorService pool = Executors.newFixedThreadPool(POOLSIZE);
        //定义只有5个线程可以并发访问资源
        final Semaphore semaphore = new Semaphore(5);
        for (int i = 1;i<=POOLSIZE;i++){
            pool.execute(new Runnable(){
                @Override
                public void run() {
                    try {
                        //获取许可
                        semaphore.acquire();
                        System.out.println("do something");
                        //释放许可
                        semaphore.release();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            });
        }
        //关闭线程池
        pool.shutdown();
    }
}
```

##### 4.Exechanger---线程间数据交换

它提供一个同步点，在这个同步点，两个线程可以交换彼此的的数据。

如果第一个线程先执行exechange()方法，他会一直等待第二个线程也执行exechange()方法，当两个线程都到达同步点时，这俩个就可以交换数据，将本线程生产出来的数据传递给对方

```java

import java.util.concurrent.Exchanger;

public class Test{
    public static void main(String[] args) {
        Exchanger<String> exchanger = new Exchanger<>();
        new Thread(new ThreadA(exchanger)).start();
        new Thread(new ThreadB(exchanger)).start();
    }
}

class  ThreadA implements Runnable{
    Exchanger<String> exchanger;
    public ThreadA(Exchanger<String> exchanger){
        this.exchanger = exchanger;
    }
    @Override
    public void run() {
        try {
            Thread.sleep(500);
            //交换结果
            String dataB = exchanger.exchange("dataA");
            System.out.println("threadA:"+dataB);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class  ThreadB implements Runnable{
    Exchanger<String> exchanger;
    public ThreadB(Exchanger<String> exchanger){
        this.exchanger = exchanger;
    }
    @Override
    public void run() {
        try {
            Thread.sleep(1000);
            //交换结果
            String dataA = exchanger.exchange("dataB");
            System.out.println("threadB:"+dataA);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

