##### 1.BlockingQueue

![](http://tutorials.jenkov.com/images/java-concurrency-utils/blocking-queue.png)

当队列满的时候，生产者线程会阻塞；

当队列空的时候，消费者线程会阻塞；

接口的实现类:

**ArrayBlockingQueue**
**DelayQueue**
**LinkedBlockingQueue**
**PriorityBlockingQueue**
**SynchronousQueue**

```java
class Producer implements Runnable{
    BlockingQueue blockingQueue;
    public Producer(BlockingQueue blockingQueue){
        this.blockingQueue = blockingQueue;
    }
    @Override
    public void run() {

        try {
            blockingQueue.add("1");
            Thread.sleep(1000);
            blockingQueue.add("2");
            Thread.sleep(1000);
            blockingQueue.add("3");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

    }
}

class Consumer implements Runnable{
    BlockingQueue blockingQueue;
    public Consumer(BlockingQueue blockingQueue){
        this.blockingQueue = blockingQueue;
    }
    @Override
    public void run() {
        try {
            System.out.println(blockingQueue.take());
            System.out.println(blockingQueue.take());
            System.out.println(blockingQueue.take());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

//测试
 				BlockingQueue blockingQueue = new ArrayBlockingQueue(1024);
        new Thread(new Producer(blockingQueue)).start();
        new Thread(new Consumer(blockingQueue)).start();
```

##### 2.BlockDeque

![](http://tutorials.jenkov.com/images/java-concurrency-utils/blocking-deque.png)

```java
 				BlockingDeque<String> blockingDeque = new LinkedBlockingDeque<>();
        blockingDeque.addFirst("123");
        blockingDeque.addLast("456");
        String first = blockingDeque.getFirst();
        String last = blockingDeque.getLast();
        System.out.println("first: "+first);//123
        System.out.println("last: "+last);//456
```

##### 3.ConcurrentMap

```java
				ConcurrentMap<String,Object> map = new ConcurrentHashMap<>();
        map.put("key","value");
        Object o = map.get("key");
```

##### 4.CountDownLatch

```java
class Waiter implements Runnable{

    CountDownLatch latch = null;

    public Waiter(CountDownLatch latch) {
        this.latch = latch;
    }

    public void run() {
        try {
            latch.await();//阻塞到count减为0
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Waiter Released");
    }
}

class Decrementer implements Runnable {

    CountDownLatch latch = null;

    public Decrementer(CountDownLatch latch) {
        this.latch = latch;
    }

    public void run() {

        try {
            Thread.sleep(1000);
            this.latch.countDown();//
            System.out.println(latch.getCount());
            Thread.sleep(1000);
            this.latch.countDown();
            System.out.println(latch.getCount());
            Thread.sleep(1000);
            this.latch.countDown();
            System.out.println(latch.getCount());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}


//测试
 				CountDownLatch latch = new CountDownLatch(3);
        Waiter      waiter      = new Waiter(latch);
        Decrementer decrementer = new Decrementer(latch);
        new Thread(waiter)     .start();
        new Thread(decrementer).start();
        Thread.sleep(4000);
```

##### 5.ExecutorService

实现类:

`ThreadPoolEexcutor`                `ScheduledThreadPoolExecutor`

创建对象

```java
ExecutorService executorService1 = Executors.newSingleThreadExecutor();

ExecutorService executorService2 = Executors.newFixedThreadPool(10);

ExecutorService executorService3 = Executors.newScheduledThreadPool(10);
```

执行任务

```java
execute(Runnable)
submit(Runnable)
submit(Callable)
```

##### 6.Callable

```java
public interface Callable<V>{
  V call() throws Exception;
}
```

```java
class MyCallable implements Callable<String>{
  @Override
  public String call() throws Exception{
    return String.valueOf(System.currentTimeMillis);
  }
}
```

##### 7.Future

```java
public interface Future<V>{
  boolean cancel(boolean mayInterruptIfRunning);
  v get();//阻塞等待
  v get(long timeout,TimeUnit unit);//设定的时间内没有从task获取到返回值，抛出异常
  boolean isCancelled();//检测task是否取消了
  boolean isDone();//检测task是否执行完了
}
```

##### 8.ThreadPoolExecutor

![](http://tutorials.jenkov.com/images/java-concurrency-utils/thread-pool-executor.png)

```java
corePoolSize//线程数量的下线
maximumPoolSize//线程数量的下线
首先会创建corePoolSize数量的线程，当任务队列满了，单此线程池corePoolSize<=threadCount<maxPoolSize，则会创建新的线程来执行任务
```

```java
int  corePoolSize  =    5;
int  maxPoolSize   =   10;
long keepAliveTime = 5000;

ExecutorService threadPoolExecutor =
        new ThreadPoolExecutor(
                corePoolSize,
                maxPoolSize,
                keepAliveTime,
                TimeUnit.MILLISECONDS,
                new LinkedBlockingQueue<Runnable>()
                );
```



