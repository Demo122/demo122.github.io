# javaSE

## 3.23 单元测试、反射、注解、工厂模式、装饰模式

**单元测试**
单元测试就是针对最小的功能单元编写测试代码，Java程序最小的功能单元是方法，因此，单元测试就是针对Java方法的测试，进而检查方法的正确性。
- Junit单元测试框架
    - JUnit是使用Java语言实现的单元测试框架，它是开源的，Java开发者都应当学习并使用JUnit编写单元测试
    - 此外，几乎所有的IDE工具都集成了JUnit，这样我们就可以直接在IDE中编写并运行JUnit测试，JUnit目前最新版本是5。
- JUnit优点
    - JUnit可以灵活的选择执行哪些测试方法，可以一键执行全部测试方法。
    - Junit可以生成全部方法的测试报告。
    - 单元测试中的某个方法测试失败了，不会影响其他测试方法的测试。
- 单元测试快速入门
    1. 将JUnit的jar包导入到项目中
    2. 编写测试方法：该测试方法必须是公共的无参数无返回值的非静态方法。
    3. 在测试方法上使用 **@Test** 注解：标注该方法是一个测试方法
    4. 在测试方法中完成被测试方法的预期正确性测试。 ```Assert.assertEquals();```
    5. 选中测试方法，选择“JUnit运行” ，如果测试良好则是绿色；如果测试失败，则是红色
- Junit常用注解(Junit 4.xxxx版本)
    - @Test         : 测试方法。
    - @Before       : 用来修饰 ***实例方法***，该方法会在每一个测试方法执行之前执行一次。 junit5为@BeforeEach
    - @After        : 用来修饰 ***实例方法***，该方法会在每一个测试方法执行之后执行一次。 junit5为@AftereEach
    - @BeforeClass  : 用来 **静态修饰方法**，该方法会在所有测试方法之前只执行一次。      junit5为@BeforeAll
    - @AfterClass   : 用来 **静态修饰方法**，该方法会在所有测试方法之后只执行一次。      junit5为@AfterAll  
    - 开始执行的方法:初始化资源
    - 执行完之后的方法:释放资源

**反射**
![反射概述](note_img\学习记录\3.23\反射概述.png)
**获取class对象**
```java
//1. Class.forName()
Class c = Class.forName("com.day7.People");
System.out.println(c);

//2. 类名.class
Class c1= Studnet.class;
System.out.println(c1);

//3. getClass()
Studnet s=new Studnet();
Class c2=s.getClass();
System.out.println(c2);
```
- ![反射拿构造器](note_img\学习记录\3.23\反射那构造器.png)
- ![反射构造对象](note_img\学习记录\3.23\反射构造对象.png)
- ![反射获取成员变量](note_img\学习记录\3.23\反射获取成员变量.png)
- ![使用反射技术获取成员变量对象并使用](note_img\学习记录\3.23\使用反射技术获取成员变量对象并使用.png)
- ![使用反射技术获取方法对象并使用](note_img\学习记录\3.23\使用反射技术获取方法对象并使用.png)
- ![使用反射技术获取方法对象并使用2](note_img\学习记录\3.23\使用反射技术获取方法对象并使用2.png)

- ![编译成Class文件进入运行阶段的时候，泛型会自动擦除](note_img\学习记录\3.23\编译成Class文件进入运行阶段的时候，泛型会自动擦除。.png)
- ![反射的作用](note_img\学习记录\3.23\反射的作用.png)

**注解**
- 注解概述、作用
    - Java 注解（Annotation）又称 Java 标注，是 JDK5.0 引入的一种注释机制
    - Java 语言中的类、构造器、方法、成员变量、参数等都可以被注解进行标注。
- 注解的作用是什么呢
    - 对Java中类、方法、成员变量做标记，然后进行特殊处理，至于到底做何种处理由业务需求来决定。
    - 例如：JUnit框架中，标记了注解@Test的方法就可以被当成测试方法执行，而没有标记的就不能当成测试方法执行。
- **自定义注解 --- 格式**
    ```
    public @interface 注解名称 {
        public 属性类型 属性名() default 默认值 ;
    }
    ```
- **特殊属性**
    - value属性，如果只有一个value属性的情况下，使用value属性的时候可以省略value名称不写!!
    - 但是如果有多个属性,  且多个属性没有默认值，那么value名称是不能省略的。

- 元注解：就是注解 注解的注解
    - 元注解有两个：
        - **@Target: 约束自定义注解只能在哪些地方使用**
        - **@Retention：申明注解的生命周期**
        ![元注解](note_img\学习记录\3.23\元注解.png)
    - ![注解的解析](note_img\学习记录\3.23\注解的解析.png)
    - 解析注解的技巧
        - 注解在哪个成分上，我们就先拿哪个成分对象。
        - 比如注解作用成员方法，则要获得该成员方法对应的Method对象，再来拿上面的注解
        - 比如注解作用在类上，则要该类的Class对象，再来拿上面的注解
        - 比如注解作用在成员变量上，则要获得该成员变量对应的Field对象，再来拿上面的注解

**工厂模式**
- 什么是工厂模式
    - 之前我们创建类对象时, 都是使用new 对象的形式创建,在很多业务场景下也提供了不直接new的方式 。
    - 工厂模式（Factory Pattern）是 Java 中最常用的设计模式之一，  这种类型的设计模式属于创建型模式，它提供了一种获取对象的方式。
- 工厂设计模式的作用：
    - 工厂的方法可以封装对象的创建细节，比如：为该对象进行加工和数据注入。
    - 可以实现类与类之间的解耦操作（核心思想）。

**装饰模式**
![装饰模式](note_img\学习记录\3.23\装饰者模式.png)

​     


***
## 3.18 多线程

**多线程**
- 多线程是指从软硬件上实现多条执行流程的技术。
- **多线程的创建**
    - 方式一：继承Thread类
        * Thread类,Java是通过java.lang.Thread 类来代表线程的。 
        * 多线程的实现方案一：继承Thread类
            1. 定义一个子类MyThread继承线程类java.lang.Thread，重写run()方法
            2. 创建MyThread类的对象
            3. 调用线程对象的start()方法启动线程（启动后还是执行run方法的）
        * 优点：编码简单
        * 缺点：线程类已经继承Thread，无法继承其他类，不利于扩展
        * *问答*
            1. 为什么不直接调用了run方法，而是调用start启动线程。
                * 直接调用run方法会当成普通方法执行，此时相当于还是单线程执行。
                * 只有调用start方法才是启动一个新的线程执行。
            2. 把主线程任务放在子线程之前了。
                * 这样主线程一直是先跑完的，相当于是一个单线程的效果了

    - 方式二：实现Runnable接口
        * 多线程的实现方案二：实现Runnable接口
            1. 定义一个线程任务类MyRunnable实现Runnable接口，重写run()方法
            2. 创建MyRunnable任务对象
            3. 把MyRunnable任务对象交给Thread处理。
            4. 调用线程对象的start()方法启动线程
            ![Runnable构造器](note_img\学习记录\3.18\runnable.png)
            - 优点：线程任务类只是实现接口，可以继续继承类和实现接口，扩展性强。
            - 缺点：编程多一层对象包装，如果线程有执行结果是不可以直接返回的。
        * 实现Runnable接口(匿名内部类形式)
            1. 可以创建Runnable的匿名内部类对象。
            2. 交给Thread处理。
            3. 调用线程对象的start()启动线程。

    - 前两种方式的问题
    ![前两种方式的问题](note_img\学习记录\3.18\问题.png)

    - 方式三：JDK 5.0新增：利用Callable、FutureTask接口实现
        1. 得到任务对象
            - 定义类实现Callable接口，重写call方法，封装要做的事情。
            - 用FutureTask把Callable对象封装成线程任务对象
        2. 把线程任务对象交给Thread处理
        3. 调用Thread的start方法启动线程，执行任务
        4. 线程执行完毕后、通过FutureTask的get方法去获取任务执行的结果。
        ![Callable](note_img\学习记录\3.18\Callable.png)

    - 三种方式比较
    ![三种方式比较](note_img\学习记录\3.18\方式比较.png)

    - 线程安全
        * 什么是线程安全
            * **多个线程同时操作同一个共享资源的时候可能会出现业务安全问题，称为线程安全问题**
        * 线程安全问题出现的原因？
            * 存在多线程并发
            * 同时访问共享资源
            * 存在修改共享资源
    - 线程同步
        - 让多个线程实现先后依次访问共享资源，这样就解决了安全问题
        - 线程同步的核心思想
            **加锁，把共享资源进行上锁，每次只能一个线程进入访问完毕以后解锁，然后其他线程才能进来。**
        - **方式一：同步代码块**
            * **作用：把出现线程安全问题的核心代码给上锁。**
            * **原理：每次只能一个线程进入，执行完毕后自动解锁，其他线程才可以进来执行。**
            * ```synchronized(同步锁对象) {
                    操作共享资源的代码(核心代码)
                } ```
            * 锁对象要求, 理论上：锁对象只要对于当前同时执行的线程来说是同一个对象即可。
            ![锁对象](note_img\学习记录\3.18\锁对象.png)
        - **方式二：同步方法**
            * **作用：把出现线程安全问题的核心方法给上锁。**
            * **原理：每次只能一个线程进入，执行完毕以后自动解锁，其他线程才可以进来执行。**
            * 格式 
            ```修饰符 synchronized 返回值类型 方法名称(形参列表) { 操作共享资源的代码 } ```
            * 同步方法底层原理
                * 同步方法其实底层也是有隐式锁对象的，只是锁的范围是整个方法代码。
                * 如果方法是实例方法：同步方法默认用this作为的锁对象。但是代码要高度面向对象
                * 如果方法是静态方法：同步方法默认用类名.class作为的锁对象。
            * 同步代码块锁的范围更小，同步方法锁的范围更大。
        - **方式三：Lock锁**
            * 为了更清晰的表达如何加锁和释放锁，JDK5以后提供了一个新的锁对象Lock，更加灵活、方便。
            * Lock实现提供比使用synchronized方法和语句可以获得更广泛的锁定操作。
            * Lock是接口不能直接实例化，这里采用它的实现类ReentrantLock来构建Lock锁对象。
            * public ReentrantLock(),获得Lock锁的实现类对象
            * Lock的API,lock()获得锁，unlock()释放锁








    - 线程通信
        所谓线程通信就是线程间相互发送数据，线程间共享一个资源即可实现线程通信。
        通过共享一个数据的方式实现。
        根据共享数据的情况决定自己该怎么做，以及通知其他线程怎么做。
        - 线程通信实际应用场景
            - 生产者与消费者模型：生产者线程负责生产数据，消费者线程负责消费生产者产生的数据
            - 要求：生产者线程生产完数据后唤醒消费者，然后等待自己，消费者消费完该数据后唤醒生产者，然后等待自己。
        - 线程通信的前提：线程通信通常是在多个线程操作同一个共享资源的时候需要进行通信，且要保证线程安全。
        - Object类的等待和唤醒方法：
            ![线程通信](note_img\学习记录\3.18\线程通信.png)

**线程池[重点]**
- 线程池就是一个可以复用线程的技术。如果用户每发起一个请求，后台就创建一个新线程来处理，下次新任务来了又要创建新线程，而创建新线程的开销是很大的，这样会严重影响系统的性能。
- JDK 5.0起提供了代表线程池的接口：ExecutorService
- 如何得到线程池对象
    - **方式一：使用ExecutorService的实现类ThreadPoolExecutor自创建一个线程池对象**
        ![线程池](note_img\学习记录\3.18\线程池.jpg)
    - **线程池常见面试题**
        1. **临时线程什么时候创建啊？**
            **新任务提交时发现核心线程都在忙，任务队列也满了，并且还可以创建临时线程，此时才会创建临时线程。**
        2. **什么时候会开始拒绝任务？**
            **核心线程和临时线程都在忙，任务队列也满了，新的任务过来的时候才会开始任务拒绝。**
    - ![线程池示例](note_img\学习记录\3.18\线程池示例.jpg)
    - ![新任务拒绝策略](note_img\学习记录\3.18\新任务拒绝策略.jpg)
    - 线程池如何处理Callable任务，并得到任务执行完后返回的结果。
        - 使用ExecutorService的方法：
        - Future<T> submit(Callable<T> command)
    - 方式二：使用Executors（线程池的工具类）调用方法返回不同特点的线程池对象（不推荐）
        public static ExecutorService newFixedThreadPool​(int nThreads)
        创建固定线程数量的线程池，如果某个线程因为执行异常而结束，那么线程池会补充一个新线程替代它。

**定时器**
- 定时器是一种控制任务延时调用，或者周期调用的技术。
- 作用：闹钟、定时邮件发送。
- 定时器的实现方式
    - 方式一：Timer
        ![timer定时器](note_img\学习记录\3.18\timer定时器.png)
    - 方式二： **ScheduledExecutorService**
        ![ScheduledExecutorService定时器](note_img\学习记录\3.18\ScheduledExecutorService定时器.png)

***
## 3.18 缓冲流、序列化、Properties、commons-io

**缓冲流**
* 缓冲流也称为高效流、或者高级流。之前学习的字节流可以称为原始流
* 作用：缓冲流自带缓冲区、可以提高原始字节流、字符流读写数据的性能
* **建议使用字节缓冲输入流、字节缓冲输出流，结合字节数组的方式，目前来看是性能最优的组合**

**对象序列化**
- 作用：以内存为基准，把内存中的对象存储到磁盘文件中去，称为对象序列化
- 使用到的流是对象字节输出流：ObjectOutputStream
- **对象必须实现序列化接口Serializable**
**对象反序列化**
- 使用到的流是对象字节输入流：ObjectInputStream
- 作用：以内存为基准，把存储到磁盘文件中去的对象数据恢复成内存中的对象，称为对象反序列化。

**打印流**
- 打印流一般是指：PrintStream，PrintWriter两个类。
- 打印功能2者是一样的使用方式
- PrintStream继承自字节输出流OutputStream，支持写字节
- PrintWrite继承自字符输出流Writer，支持写字符
- 两者在打印功能上都是使用方便，性能高效（核心优势）

**Properties**
- Properties属性集对象
    * 其实就是一个Map集合，但是我们一般不会当集合使用，因为HashMap更好用。
- Properties核心作用：
    * Properties代表的是一个属性文件，可以把自己对象中的键值对信息存入到一个属性文件中去。
    * 属性文件：后缀是.properties结尾的文件,里面的内容都是 key=value，后续做系统配置信息的。
- properties的API
    ![properties——api](note_img\学习记录\3.18\properties.png)

**commons-io**
- commons-io是apache开源基金组织提供的一组有关IO操作的类库，可以提高IO功能开发的效率。
- commons-io工具包提供了很多有关io操作的类。有两个主要的类FileUtils, IOUtils
- **FileUtils主要有如下方法:**
    - ```String readFileToString(File file, String encoding)```, 读取文件中的数据, 返回字符串
    - ```void copyFile(File srcFile, File destFile)```, 复制文件。
    - ```void copyDirectoryToDirectory(File srcDir, File destDir)```, 复制文件夹。

***
## 3.17 不可变集合、Stream、异常体系、日志框架、File、IO流
**不可变集合**
集合的数据项在创建的时候提供，并且在整个生命周期中都不可改变。否则报错
- 为什么要创建不可变集合？
    - 如果某个数据不能被修改，把它防御性地拷贝到不可变集合中是个很好的实践
    - 或者当集合对象被不可信的库调用时，不可变形式是安全的。
    ![不可变集合](note_img\学习记录\3.17\不可变集合.png)
    **List、Set、Map接口中，都存在of方法可以创建不可变集合，jdk9开始才有**

**Stream流**
暂无、跳过

**异常体系**
![异常体系](note_img\学习记录\3.17\异常体系.png)
- 运行时异常
    直接继承自RuntimeException或者其子类，编译阶段不会报错，运行时可能出现的错误。
- 运行时异常示例
    * 数组索引越界异常: ArrayIndexOutOfBoundsException
    * 空指针异常 : NullPointerException，直接输出没有问题，但是调用空指针的变量的功能就会报错。
    * 数学操作异常：ArithmeticException
    * 类型转换异常：ClassCastException
    * 数字转换异常： NumberFormatException
    **运行时异常：一般是程序员业务没有考虑好或者是编程逻辑不严谨引起的程序错误，自己的水平有问题！**
- 编译时异常
    不是RuntimeException或者其子类的异常，编译阶就报错，必须处理，否则代码不通过

***编译时异常的处理形式有三种：***
-  出现异常直接抛出去给调用者，调用者也继续抛出去。
    * throws：用在方法上，可以将方法内部出现的异常抛出去给本方法的调用者处理。
    * 这种方式并不好，发生异常的方法自己不处理异常，如果异常最终抛出去给虚拟机将引起程序死亡。
    * 抛出异常格式：
        ```
        方法 throws 异常1 ，异常2 ，异常3 ..{
        
        }
        ```
        ```
        //代表可以抛出一切异常
        方法 throws Exception{
        
        }
        ```

-  出现异常自己捕获处理，不麻烦别人。
    * try…catch…
    * 监视捕获异常，用在方法内部，可以将方法内部出现的异常直接捕获处理。
    * 这种方式还可以，发生异常的方法自己独立完成异常的处理，程序可以继续往下执行。
    * 格式
        ```
        try{
            // 监视可能出现异常的代码！
        }catch(异常类型1 变量){
            // 处理异常
       }catch(异常类型2 变量){
            // 处理异常
       }
       ```
        **建议格式：**
        ```
        try{
            // 可能出现异常的代码！
        }catch (Exception e){
            e.printStackTrace(); 
            // 直接打印异常栈信息
        }
        //Exception可以捕获处理一切异常类型！
        ```
    * **throws/throw 关键字：**
        * 如果一个方法没有捕获到一个检查性异常，那么该方法必须使用 throws 关键字来声明。throws 关键字放在方法签名的尾部。
        * 也可以使用 throw 关键字抛出一个异常，无论它是新实例化的还是刚捕获到的。
        ```
        public class className
        {
            public void deposit(double amount) throws RemoteException
            {
                // Method implementation
                throw new RemoteException();
            }
            //Remainder of class definition
        }
        ```
    * **finally关键字**
        * finally 关键字用来创建在 try 代码块后面执行的代码块。
        * **无论是否发生异常，finally 代码块中的代码总会被执行。**
        * 在 finally 代码块中，可以运行清理类型等收尾善后性质的语句。 
        * finally 代码块出现在 catch 代码块最后，语法如下：
        ```
        try{
            // 程序代码
        }catch(异常类型1 异常的变量名1){
            // 程序代码
        }catch(异常类型2 异常的变量名2){
            // 程序代码
        }finally{
            // 程序代码
        }
        ```
-  前两者结合，出现异常直接抛出去给调用者，调用者捕获处理。
    * 方法直接将异通过throws抛出去给调用者
    * 调用者收到异常后直接捕获处理。
- **异常处理的总结**
    * **在开发中按照规范来说第三种方式是最好的：底层的异常抛出去给最外层，最外层集中捕获处理**
    * 实际应用中，只要代码能够编译通过，并且功能能完成，那么每一种异常处理方式似乎也都是可以的

- **自定义异常类**
    * Java无法为这个世界上全部的问题提供异常类。如果企业想通过异常的方式来管理自己的某个业务问题，就需要自定义异常类了。
    * 可以使用异常的机制管理业务问题，如提醒程序员注意。同时一旦出现bug，可以用异常的形式清晰的指出出错的地方。
    * 自定义异常的分类
    1. 自定义编译时异常       
        *  定义一个异常类继承Exception.
        *  重写构造器。
        *  在出现异常的地方用throw new 自定义对象抛出，
        * 作用：编译时异常是编译阶段就报错，提醒更加强烈，一定需要处理！！
    2. 自定义运行时异常
        * 定义一个异常类继承RuntimeException.
        * 重写构造器。
        * 在出现异常的地方用throw new 自定义对象抛出!
        * 作用：提醒不强烈，编译阶段不报错！！运行时才可能出现！！
    ```
    //自定义异常
    public class myException extends Exception{
        public myException() {
            System.out.println("自定义异常");
        }
    }
    //抛出异常
    public class Demo {
        public void test() throws myException {
            System.out.println("开始抛出异常");
            throw new myException();
        }
    }
    //捕获异常
    Demo d=new Demo();
    try {
        d.test();
    } catch (myException e) {
        e.printStackTrace();
    }
    System.out.println("异常抛出");
    
    ```

**日志框架**
- 日志技术具备的优势
    * 可以将系统执行的信息选择性的记录到指定的位置（控制台、文件中、数据库中）。
    * 可以随时以开关的形式控制是否记录日志，无需修改源代码。
    ![日志技术](note_img\学习记录\3.17\日志技术.png)
    **log4j很流行，但是logback更好，是他的升级版**

**Logback**
- 介绍
    * Logback是由log4j创始人设计的另一个开源日志组件，性能比log4j要好
    * Logback是基于slf4j的日志规范实现的框架。
- Logback主要分为三个技术模块：
    *  logback-core： logback-core 模块为其他两个模块奠定了基础，必须有。
    *  logback-classic：它是log4j的一个改良版本，同时它完整实现了slf4j API。
    *  logback-access 模块与 Tomcat 和 Jetty 等 Servlet 容器集成，以提供 HTTP 访问日志功能
- **Logback快速入门**
    ![Logback快速入门](note_img\学习记录\3.17\logback快速入门.png)
    ![Logback1](note_img\学习记录\3.17\logback1.png)
- ***日志级别***
    ![日志级别](note_img\学习记录\3.17\日志级别.png)

**File类使用**
- File类创建对象
    * ```public File​(String pathname)```,根据文件路径创建文件对象
    * ```public File​(String parent, String child)```,从父路径名字符串和子路径名字符串创建文件对象
    * ```public File​(File  parent, String child)```,根据父路径对应文件对象和子路径名字符串创建文件对象
    * File对象可以定位文件和文件夹
    * File封装的对象仅仅是一个路径名，这个路径可以是存在的，也可以是不存在的。
    ![file_api](note_img\学习记录\3.17\file_api.png)
    ![file_api2](note_img\学习记录\3.17\file_api2.png)
    ![fiel_api3](note_img\学习记录\3.17\file_api3.png)

![字符编码](note_img\学习记录\3.17\字符编码.png)
![String编码](note_img\学习记录\3.17\String编码.png)

**IO流**
- ![IO流](note_img\学习记录\3.17\IO流.png)
- 总结流的四大类:
    * 字节输入流：以内存为基准，来自磁盘文件/网络中的数据**以字节的形式读入到内存中**去的流称为字节输入流。
    * 字节输出流：以内存为基准，把内存中的数据**以字节写出到磁盘文件或者网络中**去的流称为字节输出流。
    * 字符输入流：以内存为基准，来自磁盘文件/网络中的数据**以字符的形式读入到内存中**去的流称为字符输入流。
    * 字符输出流：以内存为基准，把内存中的数据**以字符写出到磁盘文件或者网络介质中**去的流称为字符输出流。
- **字节流**
    - ![fileinputstram](note_img\学习记录\3.17\fileinputstram.png)
    - ![fileoutputstram](note_img\学习记录\3.17\fileoutputstram.png)
    - ![fileoutputstram_api](note_img\学习记录\3.17\fileoutputstram_api.png)
- **资源释放**
![资源释放](note_img\学习记录\3.17\资源释放.png)
![简化资源释放](note_img\学习记录\3.17\简化资源释放.png)
![资源释放注意](note_img\学习记录\3.17\资源释放注意.png)


***
## 3.16 Map集合
**Map集合**
- Map集合概述和使用
    * Map集合是一种双列集合，每个元素包含两个数据。
    * Map集合的每个元素的格式：key=value(键值对元素)。
    * Map集合也被称为“键值对集合”。
    * Map集合的完整格式：{key1=value1 , key2=value2 , key3=value3 , ...}
- Map集合体系
    ![Map集合体系](note_img\学习记录\3.16\Map集合体系.png)
    * Map集合体系特点
        1. Map集合的特点都是由键决定的。
        2. Map集合的键是无序,不重复的，无索引的，值不做要求（可以重复）。
        3. Map集合后面重复的键对应的值会覆盖前面重复键的值。
        4. Map集合的键值对都可以为null。
    * Map集合实现类特点
        1. HashMap:元素按照键是无序，不重复，无索引，值不做要求。（与Map体系一致）
        2. LinkedHashMap:元素按照键是有序，不重复，无索引，值不做要求。
        3. TreeMap：元素按照键是排序，不重复，无索引的，值不做要求。
- Map集合的遍历方式有：3种。
    * 方式一：键找值的方式遍历：先获取Map集合全部的键，再根据遍历键找值。
    * 方式二：键值对的方式遍历，把“键值对“看成一个整体，难度较大。
    * 方式三：JDK 1.8开始之后的新技术：Lambda表达式。
        得益于JDK 8开始的新技术Lambda表达式，提供了一种更简单、更直接的遍历集合的方式。
        ``` maps.forEach((k , v) -> {
               System.out.println(k +"----->" + v);
            });
        ```
- HashMap
    * HashMap跟HashSet底层原理是一模一样的，都是哈希表结构，只是HashMap的每个元素包含两个值而已
    * 实际上：Set系列集合的底层就是Map实现的，只是Set集合中的元素只要键数据，不要值数据而已。
    *   ```public HashSet() {  
                map = new HashMap<>();
            }
        ```
- LinkedHashMap
    * 原理：底层数据结构是依然哈希表，只是每个键值对元素又额外的多了一个双链表的机制记录存储的顺序。
- TreeMap
    * 可排序：按照键数据的大小默认升序（有小到大）排序。只能对键排序。
    * 注意：TreeMap集合是一定要排序的，可以默认排序，也可以将键按照指定的规则进行排序
    * TreeMap跟TreeSet一样底层原理是一样的。


***
## 3.15 数据结构、List系列集合、泛型、Set系列集合
**数据结构**
- 栈：后进先出，先进后出
- 队列：先进先出，后进后出
- 数组：数组是一种查询快，增删慢的模型
    1. 查询速度快：查询数据通过地址值和索引定位，查询任意数据耗时相同。（元素在内存中是连续存储的）
    2. 删除效率低：要将原始数据删除，同时后面每个数据前移。
    3. 添加效率极低：添加位置后的每个数据后移，再添加元素
- 链表： 链表中的元素是在内存中不连续存储的，每个元素节点包含数据值和下一个元素的地址
    1. 链表查询慢。无论查询哪个数据都要从头开始找
    2. 链表增删相对快
- 二叉树
    1. 只能有一个根节点，每个节点最多支持2个直接子节点
    2. 节点的度： 节点拥有的子树的个数，二叉树的度不大于2 叶子节点 度为0的节点，也称之为终端结点
    3. 高度：叶子结点的高度为1，叶子结点的父节点高度为2，以此类推，根节点的高度最高。
    4. 层：根节点在第一层，以此类推
    5. 兄弟节点 ：拥有共同父节点的节点互称为兄弟节点
- 平衡二叉树：平衡二叉树是在满足查找二叉树的大小规则下，让树尽可能矮小，以此提高查数据的性能
- **红黑树**
![红黑树](note_img\学习记录\3.15\红黑树.png)
![总结](note_img\学习记录\3.15\总结.png)

**List系列集合**
- 特点：
    1. ArrayList、LinekdList ：有序，可重复，有索引。
    2. 有序：存储和取出的元素顺序一致
    3. 有索引：可以通过索引操作元素
    4. 可重复：存储的元素可以重复
- List的实现类的底层原理
    1. ArrayList底层是基于数组实现的，根据查询元素快，增删相对慢。
    2. LinkedList底层基于双链表实现的，查询元素慢，增删首尾元素是非常快的。
- List集合的遍历方式有几种: **迭代器**(推荐，更安全) 、增强for循环、Lambda表达式、for循环（因为List集合存在索引）
- LinkedList的特点：底层数据结构是双链表，查询慢，首尾操作的速度是极快的，所以多了很多首尾操作的特有API。
![LinkedList特点](note_img\学习记录\3.15\linkedlist.png)

```
//linkedlist可以完成队列、栈结构，因为它是双链表结构
//栈
LinkedList<String> stack=new LinkedList<>();
    //入栈
//  stack.addFirst("1");
//  stack.addFirst("2");
//  stack.addFirst("3");
stack.push("1"); //push就是addFirst

System.out.println(stack);  //[3, 2, 1]
    //出栈
//  System.out.println(stack.removeFirst()); //3
//  System.out.println(stack.removeFirst()); //2
//  System.out.println(stack.removeFirst()); //1
System.out.println(stack.pop()); //pop就是removeFirst()

//队列
LinkedList<String> queue=new LinkedList<>();
    //入队
queue.addLast("A");
queue.addLast("B");
queue.addLast("C");
System.out.println(queue); //[A, B, C]
    //出队
System.out.println(queue.removeFirst());
System.out.println(queue.removeFirst());
System.out.println(queue.removeFirst());
```

**集合的并发修改异常问题**

![集合的并发修改异常](note_img\学习记录\3.15\集合的并发修改异常.png)

**Iterator迭代器中的remove的方法需要在next()方法调用后才能调用，且只能使用一次**


**泛型**
1. 泛型概述
    - 泛型：是JDK5中引入的特性，可以在编译阶段约束操作的数据类型，并进行检查
    - **泛型类的原理：把出现泛型变量的地方全部替换成传输的真实数据类型**
    - 泛型的格式：<数据类型>; 注意：泛型只能支持引用数据类型
    - 泛型类的格式：修饰符 class 类名<泛型变量>{  }
    - 泛型方法的格式：修饰符 <泛型变量> 方法返回值 方法名称(形参列表){}
    - 泛型接口的格式：修饰符 interface 接口名称<泛型变量>{}
    - 集合体系的全部接口和实现类都是支持泛型的使用的
2. 泛型的好处
    - 统一数据类型
    - 把运行时期的问题提前到了编译期间，避免了强制类型转换可能出现的异常，因为编译阶段类型就能确定下来
3. 通配符:?
    - ? 可以在“使用泛型”的时候代表一切类型。
    - E T K V 是在定义泛型的时候使用的
    - 泛型的上下限：
        - ? extends Car: ?必须是Car或者其子类   泛型上限
        - ? super Car ： ?必须是Car或者其父类   泛型下限


**Set系列集合**
- Set系列集合特点
    1. 无序：存取顺序不一致
    2. 不重复：可以去除重复
    3. 无索引：没有带索引的方法，所以不能使用普通for循环遍历，也不能通过索引来获取元素
- Set集合实现类特点
    1. HashSet : **无序**、不重复、无索引
    2. LinkedHashSet：**有序**、不重复、无索引
    3. TreeSet：**排序**、不重复、无索引
- **HashSet底层原理**
    HashSet集合底层采取哈希表存储的数据,哈希表是一种对于增删改查数据性能都较好的结构。
    1. **哈希表的组成**
        * JDK8之前的，底层使用**数组+链表**组成
        * JDK8开始后，底层采用**数组+链表+红黑树**组成。
    2. jdk8的hashset原理
        * 哈希表（数组、链表、红黑树的结合体）
        * 当挂在元素下面的数据过多时，查询性能降低，**从JDK8开始后，当链表长度超过8的时候，自动转换为红黑树**
        * HashSet去重复原理解析
        ![HashSet去重复](note_img\学习记录\3.15\Hashset去重复.png)
- **LinkedHashSet**
    * 有序、不重复、无索引。
    * 这里的有序指的是保证存储和取出的元素顺序一致
    * 原理：底层数据结构是依然哈希表，只是每个元素又额外的多了一个双链表的机制记录存储的顺序。
- **TreeSet**
    1. 不重复、无索引、可排序
    2. 可排序：按照元素的大小默认升序（有小到大）排序。
    3. TreeSet集合底层是基于**红黑树的数据结构实现排序**的，增删改查性能都较好。
    4. **注意：TreeSet集合是一定要排序的，可以将元素按照指定的规则进行排序。**
    **想要使用TreeSet存储自定义类型，需要制定排序规则**
    自定义排序规则,TreeSet集合存储对象的的时候有2种方式可以设计自定义比较规则
    -  让自定义的类（如学生类）实现Comparable接口重写里面的compareTo方法来定制比较规则。
    - **TreeSet集合有参数构造器，可以设置Comparator接口对应的比较器对象，来定制比较规则。**
    ```
     public TreeSet(Comparator<? super E> comparator) {
        this(new TreeMap<>(comparator));
    }
    ```
    ![集合使用场景](note_img\学习记录\3.15\集合使用场景.png)


可变参数
* 可变参数用在形参中可以接收多个数据。可变参数的格式：数据类型...参数名称
* 传输参数非常灵活，方便。可以不传输参数，可以传输1个或者多个，也可以传输一个数组
* **可变参数在方法内部本质上就是一个数组**
* 可变参数的注意事项
    1. 一个形参列表中可变参数只能有一个
    2. 可变参数必须放在形参列表的最后面
    

**Collections集合工具类**
    java.utils.Collections:是集合工具类
    **作用：Collections并不属于集合，是用来操作集合的工具类。**


***
## 3.14 Arrays、二分查找、lambda表达式

**Arrays**
![arrays1](note_img\学习记录\3.14\arrays1.png)
![arrays2](note_img\学习记录\3.14\arrays2.png)
![comparatoe](note_img\学习记录\3.14\Arrays_comparator.png)

**二分查找**
```
/**
 * 二分查找法
 */
public class BinarySearch {
    // 二分查找法,在有序数组arr中,查找target
    // 如果找到target,返回相应的索引index
    // 如果没有找到target,返回-1
    public static int find(Comparable[] arr, Comparable target) {

        // 在arr[l...r]之中查找target
        int l = 0, r = arr.length-1;
        while( l <= r ){

            //int mid = (l + r)/2;
            // 防止极端情况下的整形溢出，使用下面的逻辑求出mid
            int mid = l + (r-l)/2;

            if( arr[mid].compareTo(target) == 0 )
                return mid;

            if( arr[mid].compareTo(target) > 0 )
                r = mid - 1;
            else
                l = mid + 1;
        }

        return -1;
    }
}
```
![binarySearch](note_img\学习记录\3.14\二分查找.png)

**Lambda表达式**
![lambda](note_img\学习记录\3.14\lambda表达式.png)
![lambda2](note_img\学习记录\3.14\lambda2.png)
[附录](https://www.runoob.com/java/java8-lambda-expressions.html)

**集合**
![collection体系](note_img\学习记录\3.14\collection集合体系.png)
```
ArrayList<String > arrayList=new ArrayList<>();
    arrayList.add("google");
    arrayList.add("baidu");
    arrayList.add("tencent");
    arrayList.add("bytedance");
    arrayList.add("jd");
    
    //forEach
    arrayList.forEach(new Consumer<String>() {
        @Override
        public void accept(String s) {
            System.out.println(s);
        }
    });
```


***
## 3.13 内部类
![内部类](note_img\学习记录\3.13\内部类.png)
![静态内部类](note_img\学习记录\3.13\静态内部类.png)
静态内部类中可以直接访问外部类的静态成员
![成员内部类](note_img\学习记录\3.13\成员内部类.png)

**匿名内部类**
![匿名内部类](note_img\学习记录\3.13\匿名内部类.png)

**常用API**
* Object类
![object_equals](note_img\学习记录\3.13\object_equals.png)
* Objects类，jdk1.7后才有
![objects概述](note_img\学习记录\3.13\objects.png)
**在比较前会做一个是否为null的判断，避免空指针异常，更加安全**

**StringBuilder**
当对字符串进行修改的时候，需要使用 StringBuffer 和 StringBuilder 类。
和 String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。

在使用 StringBuffer 类时，每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象，所以如果需要对字符串进行修改推荐使用 StringBuffer。

StringBuilder 类在 Java 5 中被提出，**它和 StringBuffer 之间的最大不同在于 StringBuilder 的方法不是线程安全的（不能同步访问）。**

由于 StringBuilder 相较于 StringBuffer 有速度优势，所以多数情况下建议使用 StringBuilder 类。
![StringBuilder](note_img\学习记录\3.13\StringBuilder.png)

**Math**
![math](note_img\学习记录\3.13\math.png)

**System**
![system](note_img\学习记录\3.13\system.jpg)

**BigDecimal**
![bigdecimal](note_img\学习记录\3.13\BigDecimal_1.jpg)
![bigdecimal](note_img\学习记录\3.13\BigDecimal_2.jpg)
![bigdecimal](note_img\学习记录\3.13\bigdecimal_3.jpg)
```
System.out.println(0.09+0.01);
double a=0.09,b=0.01;
System.out.println(a+b);
BigDecimal ba=BigDecimal.valueOf(a);
BigDecimal bb=BigDecimal.valueOf(b);
System.out.println(ba.add(bb));

//输出
0.09999999999999999
0.09999999999999999
0.10
```
**注意：BigDecimal是一定要做精度运算的**
```
BigDecimal a = BigDecimal.valueOf(10.0);
BigDecimal b = BigDecimal.valueOf(3.0);
//BigDecimal c=a.divide(b); //报错，因为10/3是无限循环，得不到精确值
BigDecimal c = a.divide(b, 2, RoundingMode.HALF_UP); //四舍五入，精确2位
System.out.println(c);
```
* 日期格式化和解析
![日期格式化和解析](note_img\学习记录\3.13\日期格式化和解析.jpg)
![日历对象](note_img\学习记录\3.13\日历对象.png)

**包装类**
![包装类](note_img\学习记录\3.13\包装类.png)
![自动装箱——拆箱](note_img\学习记录\3.13\自动拆箱装箱.png)

**正则表达式**
![正则表达式](note_img\学习记录\3.13\正则表达式.png)

***
## 3.11 多态
***多态***
![多态](note_img\学习记录\3.11\多态.png)
![多态中的成员访问](note_img\学习记录\3.11\多态中的成员访问.png)
![注意事项](note_img\学习记录\3.11\注意事项.png)
![总结](note_img\学习记录\3.11\总结.png)

***
## 3.10 final关键字、常量、枚举、抽象类和抽象方法、接口
- 包：package,相同包下的口语直接访问，否则需要import导入，加入一个类中需要用到不同类，这两个类名又是一样的，那么默认只能导入一个类，另一个需要通过带包名访问
![权限修饰符](note_img\学习记录\3.10\20220310权限修饰符.png)

- **final关键字**
![final关键字](note_img\学习记录\3.10\20220310final.png)

- **常量**
![常量](note_img\学习记录\3.10\20220310常量.png)

- **枚举**
![枚举特点](note_img\学习记录\3.10\20220310枚举特点.png)

- **抽象类和抽象方法**
![抽象类和抽象方法](note_img\学习记录\3.10\20220310抽象类和抽象方法.png)
    1. 抽象类是用来被继承的，抽象方法是交给子类重写实现的
    2. 一个类如果继承了抽象类，那么这个类必须重写抽象类的全部抽象方法，除非这个类也定义为抽象类
![抽象类特点](note_img\学习记录\3.10\20220310抽象类特点.png)

- **面试题**
![final和abstract](note_img\学习记录\3.10\20220310final和abstract.png)


- ***接口***
![接口](note_img\学习记录\3.10\接口.png)
![接口的多实现](note_img\学习记录\3.10\接口的多实现.png)
![接口多继承](note_img\学习记录\3.10\接口多继承.png)

**JDK8接口新增方法**
![JDK8接口1](note_img\学习记录\3.10\jdk8接口.png)
![JDK8接口2](note_img\学习记录\3.10\jdk8接口2.png)
**JDK9接口新增方法**
![JDK9接口](note_img\学习记录\3.10\jdk9接口.png)
**![总结](note_img\学习记录\3.10\20220310接口新增方法总结.png)**

***注意事项***
1. 接口不能创建对象
2. 一个类实现多个接口，多个接口中有同样的静态方法不冲突，**因为接口的静态方法只能通过接口名来调用，子类无法调用**
3. 一个类继承了父类，同时又实现了接口，父类中和接口中有同名方法，默认用父类的
```
public class Test {
    public static void main(String[] args) {
        ErHa er = new ErHa();
        er.eat();
    }
}
interface Animal {
    default void eat() {
        System.out.println("接口eat");
    }
}
class Dog {
    public void eat() {
        System.out.println("class eat");
    }
}
class ErHa extends Dog implements Animal {
}
```
4. 一个类实现了多个接口，多个接口中存在同名的默认方法，不冲突，整个类重写该方法即可
5. 一个接口继承多个接口是可以的，如果多个接口存在规范冲突则不能多继承。 

***
## 3.9 static关键字使用、代码块、单例模式、继承
***java进阶***

**static关键字使用**
- 静态成员变量，有static修饰,属于类，只在类第一次加载时创建，可以被共享访问，通过```类名.变量名``` 或者```对象名.变量名```
- 静态成员方法，有static修饰，归属类，用类名访问
- static 访问注意事项：
    1. 静态方法只能访问静态的成员，不可以直接访问实列成员
    2. 实例方法可以访问静态的成员，也可以访问实列成员
    3. 静态方法中是不可以出现this关键字的
- **工具类不用创建对象，都是静态成员方法，可以写一个private的构造函数**
**代码块**
![代码块](note_img\学习记录\3.9\20220309staticCode.png)
```
public class StaticCode {
    /**
     * 静态代码块，static修饰,与类一起加载，自动触发执行
     * 作用：可以用于初始化静态资源
     */
    public static int num;

    static {
        System.out.println("这是静态代码块");
        num = 0;
    }

    public static void main(String[] args) {

    }
}
```

**单例模式**
- 可以保证系统中，应用该模式的这个类永远只有一个实例，即一个类永远只能创建一个对象
- 例如任务管理器对象哦我们只需要一个就可以解决问题了，这样口语节省内存空间
- 实现方式很多：
    1. 饿汉单例：在用类获取对象的时候，对象以及提前为你创建好了
    ```
    public class SingleInstance {
        //饿汉单例设计模式
        /**
        * 饿汉单例设计模式：在用类获取对象的时候，对象以及提前为你创建好了
        * 2.这个对象只能是一个，所以定义为静态成员变量！
        */
        public static SingleInstance instance=new SingleInstance();
    
        /**
        * 1.必须把构造器私有化
        */
        private SingleInstance(){
        }
    }
    ```
    2. 懒汉单例：真正需要该对象的是，才去创建一个对象（延迟加载对象）
    ```
    public class LanSingleInstance {
        //设置成私有的，防止通过类名直接取null的对象
        private static LanSingleInstance instance; //null
    
        //提供静态成员方法返回对象
        public static LanSingleInstance getInstance() {
            //只需第一次创建
            if (instance == null) {
                instance = new LanSingleInstance();
            }
            return instance;
        }
    
        //构造函数私有
        private LanSingleInstance() {
        }
    }
    ```

**继承**
继承就是子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。
```
class 父类 {
}
 
class 子类 extends 父类 {
}
```
![继承特点](note_img\学习记录\3.9\20220309继承.png)
![继承特点](note_img\学习记录\3.9\20220309继承特点2.png)
![继承特点](note_img\学习记录\3.9\20220309继承特点3.png)

- 重写 

![方法重写](note_img\学习记录\3.9\20220309重写.png)

- 构造器：子类构造器一定要调用父类构造器，无论是有参还是无参构造器，一定要调用
**super(),this()构造器必须在构造器的第一行**
![this,super](note_img\学习记录\3.9\20220309this-super.png)



***
## 3.8 String、ArrayList集合
**String类**
java.lang.String ,对象不可更改类型，创建的对象不可更改，String变量每次的修改其实都是产生并指向了新的字符串对象，原来的字符串对象都市没有改变的，所以称不可变字符串
```
//创建字符串，
//方式一
String str1 = "方式一 this is a string";
System.out.println(str1);

//方式二
//1.public String() :创建一个空白字符串，不含任何内容，几乎不用
String str2=new String();
System.out.println(str2);

//2.public string(string) 根据传入的字符串内容来创建字符串对象，几乎不用
String str3=new String("i am chinese");
System.out.println(str3);

//3.public string (char [] c) : 根据字符数组的内容来创建字符串对象
char c[]={'a','b','v','i','中','国'};
String str4=new String(c);
System.out.println(str4);

//4. public string(byte []):根据字节数组的内容来创建字符串对象
byte [] b={97,98,99,100,101,102,103};
String str5=new String(b);
System.out.println(str5);
```
***面试题***

**Q** 
有什么区别
**A**
- 以""方式给出的字符串对象，在字符串常量池中存储 ，而且相同内容只会在其中存储一份
- 通过构造器的new对象，每new一次都会产生一个新的对象，放在堆内存中

**Q**
```
//这行代码创建了两个对象，new一个，常量"abc"一个
String s2 = new String("abc"); 
//这行代码创建了0个对象
String s1 = "abc";
//地址不同，false
System.out.println(s1 == s2);

String str1="abc";
string str2="ab";
String str3=s2+"c"; //只要没明确写出是字符串，都会new新对象放在堆内存中
```
![string面试题](note_img\学习记录\3.8\20220308String面试题.png)

***ArrayList集合***
![ArrayList](note_img\学习记录\3.8\20220308Arraylist.png)
```
//泛型
//jdk17开始，泛型后面的类型声明可以不写
ArrayList<String> list=new ArrayList<>(); 
```
```
ArrayList<String> list=new ArrayList<String >();
    list.add("java");
    list.add("c");
    list.add("c++");
    list.add("python");
    list.add("js");
    list.add("html");

    //1.public E get(int index)：获取某个位置出的元素值
    System.out.println(list.get(0));

    //2.public int size() 获取集合的大小
    System.out.println(list.size());

    //3.集合遍历
    for (String s :
            list) {
        System.out.println(s);
    }

    //4.public E remove(int index) :删除某个索引，并返回被删除的元素值
    String str2=list.remove(0);
    System.out.println(str2);
    System.out.println(list);
    list.remove("java");
    // remove(object o )只会删除第一次出现的元素，后面出现的不会删除

    //5. public E set(int index,E element): 修改某个索引位置的元素值
    list.set(0,"c++");
    System.out.println(list);
```
![ArrayListAPI](note_img\学习记录\3.8\20220308ArraylistAPI.png)

## 3.7
摸鱼

## 3.6
摸鱼

***
## 3.5 方法重载、面向对象
方法
```
修饰符 返回值类型 方法名(参数类型 参数名){
    ...
    方法体
    ...
    return 返回值;
}
```
**递归求1-n的和**
```
//求1-n的值
public static int nSum(int n){
    if(n!=0){
        return n+nSum(n-1);
    }else {
        return n;
    }
}
```
java中的参数传递机制：
* 基本类型的参数传递，是值传递，传的是数据值，并不是传地址
* **引用类型的参数传递，传的是地址**

**方法重载**：同一个类中，出现多个方法名称相同，但是**形参列表不同**，那么这些方法就是重载方法
形参列表不同是指，**形参个数、类型、顺序不同**

return可以单独使用，立即跳出，结束当前方法

***面向对象*** 三大特征：**封装、继承、多态**
* 构造器 
    - 无参数构造器（默认存在的），写不写都有，**一旦定义了有参构造器，那么无参构造器就没有了，还想用无参构造器就必须自己写一个**
    - 有参数构造器
* this关键字
    - 可以出现再构造器和方法中、代表当前对象的地址
    - 用于指定访问当前对象的成员变量、成员方法


***
## 3.4 三种控制结构，顺序、分支、循环 冒泡排序
三种控制结构，顺序、分支、循环
switch注意事项：
* 表达式类型只能是byte、short、int、char，jdk5开始支持枚举，jkd7支持String,不支持double、float，因为double、float底层计算不精确，不支持long,因为太大了
* case给出得值不允许重复，且只能是字面量，不能是变量
* 不要忘记写break，否则会出现穿透现象

**for循环** 
```
for (初始化; 循环语句; 迭代语句){
}
for (int i = 0; i++ < 10; System.out.println(i)){
    //输出1-10
}
```
**冒泡排序**
* 比较轮数为 数组长度-1
* 每轮次数为 数组长度-i-1
```
int [] arr={6,5,4,3,2,1};
for (int i = 0; i < arr.length-1; i++) {
    for (int j = 0; j < arr.length-i-1 ; j++) {
        if(arr[j]>arr[j+1]){
            int temp=arr[j+1];
            arr[j+1]=arr[j];
            arr[j]=temp;
        }
        System.out.println("第"+i+"轮"+Arrays.toString(arr));
    }
}
System.out.println("排序完成："+Arrays.toString(arr));
```



***
## 3.3 基本数据类型：四大类8种、自动类型转换

JAVA中的数据类型，引用数据类型（除基本数据类型外都是,例如`String`）和基本数据类型

**基本数据类型：四大类8种**
1. 整数
    * `byte`   1字节
    * `short`  2字节
    * `int`    4字节
    * `long`   8字节  `long l=1234165464L` *不加L默认是int*
2. 浮点数
    * `float`  4字节  `float score=98.5F`  *不加F默认是double*
    * `double` 8字节
3. 字符
    * `char`   2字节
4. 布尔
    * `boolean`1字节

**自动类型转换**
*类型范围小的变量，考研直接赋值给类型范围大的变量*
```
byte a=12;    原理:   a 00001100 （8位）     
int b=a;              b 00000000 00000000 00000000 00001100 (32位)
System.out.println(b); // b=12 
```
**byte short char 是直接转换成int类型参与运算的**
类型转换面试题，byte i=10; byte j=120; byte k=i+j;(*不行，byte在运算中当做int,所有k要声明为int*)  **因为byte太小，运算容易越界，所有干脆直接当作int运算**

*&& 短路与，左边为false，右边不执行
*|| 短路或，左边为true,右边不执行
*三元运行符 ```条件？a:b  int max=a>b ？ a:b ```
三元运算符嵌套，```int max = i>j? (i>k? i: k) : (j>k? j: K) ```


## 3.2 git入门
学习了git使用，搭建了docsify个人博客，主要用于记录学习过程，Java笔记
其中git使用常用命令为：
```
git init  初始化仓库 
git add xxx.xx 添加文件
git add -A 添加所有修改的文件
git commit -m "xxxx"
git reset --hard HEAD^ 返回上一版本，HEAD时当前版本 
git branch xxx 创建分支
git cheackout xx 切换分支
git merge xx 合并分支
git push origin xx 更新到github
git status 查看当前状态
....
```
附上参考资料，[不会再查](https://mp.weixin.qq.com/s/swnwBiuyVmhs5iPqv3H6BQ)

***


