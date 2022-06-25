# Java基础之多线程


## 线程的六种状态

- new新建态
- runnable可运行(就绪)
- blocked锁阻塞
- waitting无限等待
- timed waiting计时等待
- teminated终止态

> sleep()和wait都可以计时等待，**其中sleep()不会释放锁**

## 多线程的创建

### 方式一 继承Thread类

- 创建Thread的子类，并重写run（）方法，run()中的代码为执行体
- 创建该类的实例，调用它的start（）方法

> 是调用start方法，不是run方法，如果直接调用run方法只会当作普通方法执行，依然是单线程

```java
public class MyThread {
    public static void main(String[] args) {
        /**
         * 2.创建thread子类的对象，此时线程为新建态
         */
        Thread t=new ThreadDemo();
        // 3.start()启动，进入就绪态，由cpu调度执行
        t.start();

        for (int i = 0; i < 10; i++) {
            System.out.println("这是主线程："+i);
        }
    }
}


/**
 * 1.定义thread的子类，并重写run方法
 */
class ThreadDemo extends Thread{
    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("这是子线程："+i);
        }
    }
}
```



### 方式二 实现Runnable接口

- 定义类实现Runnable接口的run方法，run方法为执行体

- 创建Runnable接口实现类的对象，并将其作为target传入Thread的构造函数获取线程对象

- 启动start()方法

```java
public class RunnableThreadDemo {
    public static void main(String[] args) {
        //2.创建Runnable接口实现类的实例，将其传入到Thread的构造方法中
        RunnableThread runnableThread=new RunnableThread();
        Thread t=new Thread(runnableThread);
        t.start();
        for (int i = 0; i < 10; i++) {
            System.out.println("这是主线程："+i);
        }
    }
}

/**
 * 1.定义类实现Runnable接口的run方法，run方法为执行体
 */
class RunnableThread implements Runnable{

    @Override
    public void run() {
        for (int i = 0; i < 10; i++) {
            System.out.println("这是子线程："+i);
        }
    }
}
```

  

### 方式三 实现callable接口

- 创建callable接口的实现类

- 实现call方法

- 创建callable接口实现类的实例

- 使用FutureTask类封装callable对象，并创建futuretask对象

- 把futuretask对象传入Thread构造函数创建线程对象

- 启动线程

- 使用futuretask对象的get()方法获取返回值

```java
public class CallableThreadDemo {
    public static void main(String[] args) {
        //3.创建callable接口实现类的实例
        Callable<Integer> callable = new CallableThread(10);
        // 4.使用FutureTask类封装callable对象，并创建futuretask对象
        FutureTask<Integer> futureTask = new FutureTask<>(callable);
        //5.把futuretask对象传入Thread构造函数创建线程对象
        Thread t = new Thread(futureTask);
        //6.启动线程
        t.start();

        Callable<Integer> callable2 = new CallableThread(20);
        FutureTask<Integer> futureTask2 = new FutureTask<>(callable2);
        Thread t2 = new Thread(futureTask2);
        t2.start();

        //7.使用futuretask对象获取返回值
        try {
            //如果此时futurtask的线程没有执行完，这里代码会等待它执行完再取结果
            Integer res = futureTask.get();
            System.out.println(res);
        } catch (Exception e) {
            e.printStackTrace();
        }

        try {
            Integer res2 = futureTask2.get();
            System.out.println(res2);
        } catch (InterruptedException e) {
            e.printStackTrace();
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
    }
}


// 1.创建callable接口的实现类
class CallableThread implements Callable<Integer> {
    private int n;

    public CallableThread(int n) {
        this.n = n;
    }

    // 2、实现call方法
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i = 0; i < n; i++) {
            sum += i;
            System.out.println(Thread.currentThread().getName()+"---"+sum);
        }
        return sum;
    }
}
```

### 方式四 线程池（重点）

#### 线程池获得方式

**JDK5起提供了代表线程池的接口：`ExecutorService`**

- **方式一：使用ExecutorService的实现类ThreadPoolExecutor创建线程池对象（推荐）**
- 方式二：使用Executors（线程池的工具类）调用静态方法返回不同特点的线程池对象

> 在一些高并发的场景下，方式二容易造成资源浪费，占用太多系统资源

#### 线程池参数介绍

```java
/**
* public ThreadPoolExecutor(int corePoolSize,
*                           int maximumPoolSize,
*                           long keepAliveTime,
*                           TimeUnit unit,
*                           BlockingQueue<Runnable> workQueue,
*                           ThreadFactory threadFactory,
*                           RejectedExecutionHandler handler)
*/
```

- **`corePoolSize`: 指定线程池的核心线程数量**
- **`maximumPoolSize`：指定线程池可支持的最大线程数量**
-  **`keepAliveTime`：指定临时线程的最大存活时间**
-  **`unit`：指定存活时间的单位**
- **`workQueue`：指定任务队列，配置任务队列的长度**
- **`threadFactory`：指定用哪个线程工厂创建线程**
- **`handler`：指定拒绝策略，当任务队列满，线程忙时该怎么处理**

##### 新任务拒绝策略

- `ThreadPoolExecutor.AbortPolicy:` **默认策略，丢弃任务并抛出RejectedExecutionException异常**
- `ThreadPoolExecutor.CallerRunsPolicy`: 由主线程负责调用任务的run()方法从而绕过线程池直接执行

#### ExecutorService常用方法

- **`void execute(Runnable command)`: 执行任务，没有返回值，一般用来执行Runnable任务**
- **`Future<T> submit(Callable<T> task)`: 执行任务，返回未来任务对象获取线程执行结果，一般拿来执行Callable任务**
- **`void shutdown()`:等任务执行完毕后关闭线程**
- **`List<Runnable> shutdownNow()`:立刻关闭、停止正在执行的任务，并返回队列中未执行的任务**

#### 使用ThreadPoolExecutor创建线程池

```java
//ExecutorService的实现类ThreadPoolExecutor创建线程池对象
ExecutorService threadPool = new ThreadPoolExecutor(3, 5,8,TimeUnit.SECONDS,
                new ArrayBlockingQueue<(5), Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy());
//把任务传给线程池处理
threadPool.execute(new RunnableThread());
threadPool.execute(new RunnableThread());
threadPool.execute(new RunnableThread());
```

#### 使用Executors获取不同特点的线程池对象

**Executors底层也是使用ThreadPoolExecutor创建线程池**

```java
//创建三个固定线程
ExecutorService executorService=Executors.newFixedThreadPool(3);
executorService.execute(new RunnableThread());
executorService.execute(new RunnableThread());
executorService.execute(new RunnableThread());
```



#### 线程池常见面试题

> **临时线程什么时候创建？**

- **当新任务提交时，发现核心线程都在忙，任务队列也满了，并且还可以创建临时线程，此时才会创建临时线程**

> **什么时候会开始拒绝任务？**

- **核心线程、临时线程都在忙，任务队列也满了，此时新来的任务才会开始拒绝**

## 线程常用方法

### Thread类常用构造方法

- `Thread()`
- `Thread(String name)`
- `Thread(Runnable target)`
- `Thread(Runnable target, String name)`

*其中，参数 `name`为线程名，参数 `target`为包含线程体的目标对象。*

### Thread类常用静态方法

- `currentThread()`：**返回当前正在执行的线程；**
- `interrupted()`：**返回当前执行的线程是否已经被中断；**
- `sleep(long millis)`：**使当前执行的线程睡眠多少毫秒数；**
- `yield()`：**使当前执行的线程自愿暂时放弃对处理器的使用权并允许其他线程执行；**

### Thread类常用实例方法

- **`getId()`：返回该线程的id；**
- **`getName()`：返回该线程的名字；**
- **`getPriority()`：返回该线程的优先级；**
- **`interrupt()`：使该线程中断；**
- **`isInterrupted()`：返回该线程是否被中断；**
- **`isAlive()`：返回该线程是否处于活动状态；**
- **`isDaemon()`：返回该线程是否是守护线程；**
- **`setDaemon(boolean on)`：将该线程标记为守护线程或用户线程，如果不标记默认是非守护线程；**
- **`setName(String name)`：设置该线程的名字；**
- **setPriority(int newPriority)：改变该线程的优先级；**
- **`join()`：等待该线程终止；**
- **`join(long millis)`：等待该线程终止,至多等待多少毫秒数。**

## 线程同步

### 同步代码块

**使用```synchronized```关键字对代码进行上锁**

**示例：**

```java
 public void drawMoney(double draw_money){

        //获取当前线程名字
        String name=Thread.currentThread().getName();
		//this作为锁对象
        synchronized (this){
            if (this.money>=draw_money){

                System.out.println(name+"进来了，取走了"+draw_money);

                this.money-=draw_money;

                System.out.println("余额："+this.money);
            }else {
                System.out.println(name+"进来了,钱不够了");
            }
        }

    }
```

> 锁对象用任意唯一对象好不好？**不好，会影响无关线程的执行**

***锁对象的规范要求***

- **建议使用`共享资源`作为锁对象**
- **对于实例方法建议使用`this`作为锁对象**
- **对于静态方法建议使用`字节码（类名.class）`对象作为锁对象**

### 同步方法

**对方法加上`synchronized`关键字上锁**

**示例：**

```java
public synchronized void drawMoney(double draw_money) {
    //获取当前线程名字
    String name = Thread.currentThread().getName();
    if (this.money >= draw_money) {
        System.out.println(name + "进来了，取走了" + draw_money);
        this.money -= draw_money;
        System.out.println("余额：" + this.money);
    } else {
        System.out.println(name + "进来了,钱不够了");
    }
}
```

**底层原理：**

- 底层也是有隐式锁对象，锁住整个方法
- 如果是实例方法，默认使用`this`作为锁对象
- 如果是静态方法，默认使用`类名.class`作为锁对象

### ReetrantLock

- 为了更清晰的表达加锁和解锁，就jdk5以后提供了一个新的锁对象，lock,更加灵活方便
- **Lock是接口，我们使用它的实现类ReetrantLock，它提供了很多锁操作**
- **`void lock 获得锁`**
- **`void unlock 解锁`**

**示例：**

```java
//加上final修饰，变成唯一不可替换的锁
private final Lock lock = new ReentrantLock();

public void drawMoney(double draw_money) {

    //获取当前线程名字
    String name = Thread.currentThread().getName();

    //上锁
    lock.lock();
    try {
        if (this.money >= draw_money) {

            System.out.println(name + "进来了，取走了" + draw_money);

            this.money -= draw_money;

            System.out.println("余额：" + this.money);
        } else {
            System.out.println(name + "进来了,钱不够了");
        }
    } finally {
        //解锁
        lock.unlock();
    }
}
```

## 线程通信

- **wait():  当前线程等待，直到另一个线程调用notify()或者notifyAll()唤醒自己**
- **notify()：唤醒正在等待对象监视器（锁对象）的单个线程**
- **notifyAll()： 唤醒正在等到对象监视器（锁对象）的所以线程**

> 上述方法应该使用当前同步锁对象进行调用，例如使用`this,this.wait()`


