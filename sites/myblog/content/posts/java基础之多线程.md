---
title: "Java基础之多线程"
date: 2022-06-17T17:24:19+08:00
draft: false
categories: ["java基础"]
tags: ["多线程"]
---

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

## 线程常用方法

### Thread类常用构造方法

- Thread()
- Thread(String name)
- Thread(Runnable target)
- Thread(Runnable target, String name)

*其中，参数 name为线程名，参数 target为包含线程体的目标对象。*

### Thread类常用静态方法

- currentThread()：返回当前正在执行的线程；
- interrupted()：返回当前执行的线程是否已经被中断；
- sleep(long millis)：使当前执行的线程睡眠多少毫秒数；
- yield()：使当前执行的线程自愿暂时放弃对处理器的使用权并允许其他线程执行；

### Thread类常用实例方法

- getId()：返回该线程的id；
- getName()：返回该线程的名字；
- getPriority()：返回该线程的优先级；
- interrupt()：使该线程中断；
- isInterrupted()：返回该线程是否被中断；
- isAlive()：返回该线程是否处于活动状态；
- isDaemon()：返回该线程是否是守护线程；
- setDaemon(boolean on)：将该线程标记为守护线程或用户线程，如果不标记默认是非守护线程；
- setName(String name)：设置该线程的名字；
- setPriority(int newPriority)：改变该线程的优先级；
- join()：等待该线程终止；
- join(long millis)：等待该线程终止,至多等待多少毫秒数。

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
- `void lock 获得锁`
- `void unlock 解锁`

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

