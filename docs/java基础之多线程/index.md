# Java基础之多线程


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

1. 创建callable接口的实现类
1. 实现call方法
1. 创建callable接口实现类的实例
1. 使用FutureTask类封装callable对象，并创建futuretask对象
1. 把futuretask对象传入Thread构造函数创建线程对象
1. 启动线程
1. 使用futuretask对象的get()方法获取返回值

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


