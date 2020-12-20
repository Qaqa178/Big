# 1、juc是什么？

- java.util.concurrent在并发编程中使用的工具类

```java
class Ticket{// 资源类

    private int number = 30;
    private Lock lock = new ReentrantLock();

    public void saleTicket(){

        lock.lock();

        try {
            if(number > 0){
                System.out.println(Thread.currentThread().getName() + "\t卖出了第" + (number--) + "张票\t还剩"+number+"张票");
            }
        } finally {
            lock.unlock();
        }
    }

}

/**
 * 三个售票员        麦       30张票
 *
 * 线程    操作     资源类
 */
public class SaleTicket {

    public static void main(String[] args) {
        Ticket ticket = new Ticket();

        new Thread(() -> {for (int i = 0; i <= 40; i++) ticket.saleTicket();},"A").start();
        new Thread(() -> {for (int i = 0; i <= 40; i++) ticket.saleTicket();},"B").start();
        new Thread(() -> {for (int i = 0; i <= 40; i++) ticket.saleTicket();},"C").start();


    }
}

```

lambda表达式

```java

interface Foo{
    public void sayHello();
}

/**
 * Lambda表达式口诀
 *  拷贝小括号，写死右箭头，落地大括号
 */

public class LambdaExpressDemo {

    public static void main(String[] args) {
        Foo foo = () -> {System.out.println("hello");};
    }
}

```

```java
class Demo{
    private int number = 0;
//    Lock lock = new ReentrantLock();


    public synchronized void add() throws InterruptedException {

        // 判断
        while(number != 0){
            this.wait();
        }

        // 干活
        number ++;
        System.out.println(Thread.currentThread().getName() + "\t" + number);

        // 通知
        this.notifyAll();

    }

    public synchronized void dec() throws InterruptedException {
        // 判断
        while(number == 0){
            this.wait();
        }

        // 干活
        number --;
        System.out.println(Thread.currentThread().getName() + "\t" + number);

        // 通知
        this.notifyAll();

    }



}


/**
 * 1.线程操作资源类
 * 2.判断/干活/通知
 * 3.多线程交互中，必须要放值多线程的虚假唤醒，也即（判断只用while，不能用if）
 */
public class ThreadWaitNotifyDemo {

    public static void main(String[] args) {

        Demo demo = new Demo();

        new Thread(() -> {
            for (int i = 1; i <= 10 ; i++) {
                try {
                    demo.add();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();


        new Thread(() -> {
            for (int i = 1; i <= 10 ; i++) {
                try {
                    demo.dec();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();

        new Thread(() -> {
            for (int i = 1; i <= 10 ; i++) {
                try {
                    demo.add();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();


        new Thread(() -> {
            for (int i = 1; i <= 10 ; i++) {
                try {
                    demo.dec();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"D").start();

    }
}

```

![1605683801401](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605683801401.png)

![1605684507851](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605684507851.png)

# 2.list不安全

在并发情况下，list集合会出现`ConcurrentModificationExecption`异常

```java
/**
 * 请举例说明集合类是不安全的
 *
 * 1.故障现象
 * java.util.ConcurrentModificationException并发修改异常
 *
 * 2.导致原因
 *
 * 3.解决方案
 *  1.使用vector
 *  2.使用collections工具类中的Collections.synchronizedList(new ArrayList<String>()
 *  3.使用juc的CopyOnWriteArrayList方法
 *
 *
 * 4.优化建议
 *
 */
public class NotSafeDemo {

    public static void main(String[] args) {
        List<String> list = new CopyOnWriteArrayList<>();//Collections.synchronizedList(new ArrayList<String>());//new Vector<String>();//new ArrayList<String>();

        for (int i = 0; i < 30; i++) {
            new Thread(() -> {
                list.add(UUID.randomUUID().toString().substring(0,8));
                System.out.println(list);
            },String.valueOf(i)).start();
        }
    }
}

```



![1605687629226](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605687629226.png)

# 3.JUC辅助类

- CountDownLatch,做减法

  - 主要有两个方法，当一个或多个线程调用await的时候，这些线程会阻塞，其他线程调用countdown方法回将计数器减1（调用countDown方法的线程不会阻塞），当计数器的值变为0时，因await方法阻塞的线程就会被唤醒，继续执行

  ```java
  public class CountDownLatchDemo {
      public static void main(String[] args) throws InterruptedException {
          //参数是标志位，执行一个线程减1，当标志位为0时，执行countDownLatch.await()后的语句
          CountDownLatch countDownLatch = new CountDownLatch(6);
  
          for (int i = 1; i <= 6; i++) {
              new Thread(() -> {
                  System.out.println(Thread.currentThread().getName() +"\t" + "\t离开教室");
                  countDownLatch.countDown();
              },String.valueOf(i)).start();
  
          }
          countDownLatch.await();
          System.out.println("\t班长关门");
      }
  }
  
  
  
  
  
  
  
  public class CylicBarrierDemo {
  
      public static void main(String[] args) {
          ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(
                  2,
                  5,
                  2L,
                  TimeUnit.SECONDS,
                  new LinkedBlockingDeque<>(4),
                  Executors.defaultThreadFactory(),
                  new ThreadPoolExecutor.AbortPolicy()
          );
  
          CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
              System.out.println("召唤神龙");
          });
  
          try {
              for (int i = 0; i < 8; i++) {
                  int num = i;
                  threadPoolExecutor.execute(()->{
                      System.out.println(Thread.currentThread().getName() + "收集到了第" +num + "颗龙珠" );
                      try {
                          cyclicBarrier.await();
                      } catch (InterruptedException e) {
                          e.printStackTrace();
                      } catch (BrokenBarrierException e) {
                          e.printStackTrace();
                      }
                  });
  
              }
          } catch (Exception e) {
              e.printStackTrace();
          } finally {
              threadPoolExecutor.shutdown();
          }
  
      }
  }
  
  ```

- cyclicBarrier

  ```java
  public class CyclicBarrierDemo {
  
      public static void main(String[] args) {
          
          // 做加法，第一个参数为标志位，每执行一个线程，标志位+1，当执行七个线程后，执行第二个参数里面的方法，第二个参数为Runnable接口
          CyclicBarrier cyclicBarrier = new CyclicBarrier(7,() -> {
              System.out.println("召唤神龙");
          });
  
          for (int i = 0; i < 7; i++) {
              final int num = i;
              new Thread(() -> {
                  System.out.println(Thread.currentThread().getName() +"收集到了第\t"+ num +"\t可龙珠");
                  try {
                      cyclicBarrier.await();
                  } catch (InterruptedException e) {
                      e.printStackTrace();
                  } catch (BrokenBarrierException e) {
                      e.printStackTrace();
                  }
              },String.valueOf(i)).start();
  
          }
      }
  }
  ```

- semaphore

![1605746949501](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605746949501.png)

```java
/**
 * 控制多线程的并发数
 */
public class SemaphoreDemo {

    public static void main(String[] args) {

        Semaphore semaphore = new Semaphore(3);// 模拟资源类有3个空车位

        for (int i = 0; i < 6; i++) {
            new Thread(() -> {
                try {
                    semaphore.acquire();
                    System.out.println(Thread.currentThread().getName()+"抢占到了车位");
                    TimeUnit.SECONDS.sleep(3);
                    System.out.println(Thread.currentThread().getName()+"离开车位");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();
                }

            },String.valueOf(i)).start();
        }
    }
```



# 4.读写锁

- ReentrantReadWriteLock

```java
class MyCache{

    private volatile Map<String,Object> map = new HashMap<>();
    private ReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    public void put(String key,Object vlaue ) throws InterruptedException {
        readWriteLock.writeLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() +"\t写入数据" +key);
            TimeUnit.SECONDS.sleep(1);

            map.put(key,vlaue);
            System.out.println(Thread.currentThread().getName() + "\t写入完成");
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }

    public void get(String key) throws InterruptedException {
        readWriteLock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName() + "\t读取数据" + key);
            Thread.sleep(300);
            Object o = map.get(key);
            System.out.println(Thread.currentThread().getName() + "\t读取数据" + o);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }

    }
}




public class ReadWriteLockDemo {

    public static void main(String[] args) {
        MyCache myCache = new MyCache();

        for (int i = 1; i <=5; i++) {
             int num = i;
            new Thread(() -> {
                try {
                    myCache.put(num + "", num + "");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }

        for (int i = 1; i <=5; i++) {
            final int num = i;
            new Thread(() -> {
                try {
                    myCache.get(num + "");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }
    }
}

```

# 5.阻塞队列

- BlockingQueue
  - 在多线程领域：所谓阻塞，在某些情况下会挂起线程（阻塞），一旦条件满足，被挂起的线程就会被自动唤醒
- 问什么需要blockingqueue?
  - 好处是我们不需要关心什么时候需要阻塞线程，什么时候需要唤醒线程 

![1605750837092](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605750837092.png)

![1605751032221](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605751032221.png)

# 6.线程池

- 为什么要用线程池？
  - ![1605755945596](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605755945596.png)

```java
public class MyThreadPoolDemo {
    public static void main(String[] args) {
        // 一个线程池5个受理线程
        //ExecutorService threadPool = Executors.newFixedThreadPool(5);
        //一个线程池工作
        //ExecutorService threadPool = Executors.newSingleThreadExecutor();
        // 一池多线程
        ExecutorService threadPool = Executors.newCachedThreadPool();

        try {
            // 模拟有是个线程
            for (int i = 0; i < 10; i++) {
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "\t办理业务");
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            threadPool.shutdown();
        }
    }




}

```

##### 一、线程池原理

- newThreadExecutor

![1605764546394](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605764546394.png)

##### 二、线程池的七个参数

- corePoolSize：线程池中的常驻核心线程数。
  - 只要线程池创建出来，里面至少需要有的线程数
- maximumPoolSize：线程池中同时容纳的最大线程数，此值必须大于1
- unit：KeepAliveTime的时间单位，TimeUtil

- keepAliveTime：当业务高峰时，线程池当中的常驻线程不够用了，就会扩容到最大线程数，当业务高峰过了，且当线程空闲时间到达keepAliveTime时，就会把线程池中超过常驻核心线程数的线程销毁，只剩下常驻的核心线程数
- workQueue：任务队列，被提交但尚未被执行的任务
- threadFactory：生成线程池的线程工厂
- headler：线程池的拒绝策略。当队列慢了，并且工作线程大于最大线程数时拒接

![1605767503188](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605767503188.png)

![1605767514023](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605767514023.png)

![1605767641649](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605767641649.png)

![1605767741183](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605767741183.png)

![1605767966801](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605767966801.png)

##### 三、在工作中不使用系统提供的三种线程池，需要我们自己手动创建

##### 四、自定义线程池

```java
public class MyThreadPool {

    public static void main(String[] args) {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(

                2,
                5,
                2L,
                TimeUnit.SECONDS,
                new LinkedBlockingQueue<>(3),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());


        // / 模拟有9个线程
        try {
            for (int i = 0; i < 9; i++) {
                executor.execute(() -> {
                    System.out.println(Thread.currentThread().getName() + "\t办理业务");
                });

                Thread.sleep(1);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {

            executor.shutdown();
        }
    }




}

```

##### 五、线程池的拒绝策略

- 等待队列已经满了，再也塞不下任务了，同时，线程池中的max线程也达到了，无法继续为新任务服务，这个时候我们就需要拒绝策略处理这个问题
  - AbortPolicy（默认）：直接抛出RejectedExecutionException异常阻止程序运行
  - CallRunsPolicy：将这个任务回退回去，既不会抛弃任务，也不报异常

![1605770509987](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605770509987.png)

# 7.四大函数式接口

![1605773658263](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605773658263.png)

# 8.Stream流

是什么？

- 用于操作数据源所生成的元素序列，集合讲的是数据，流讲的是计算



特点

- stream自己不会存储
- 不会改变源对象，会返回一个新的stream
- stream操作是延迟执行，会等到需要结果的时候执行



怎么用

- 创建stream：一个数据源（集合、数组）
- 中间操作：一个中间操作，处理数据源数据
- 终止操作：一个终止操作，执行中间操作链，产生结果

