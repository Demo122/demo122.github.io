---
title: "Java基础之多线程"
date: 2022-06-17T17:24:19+08:00
draft: false
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
