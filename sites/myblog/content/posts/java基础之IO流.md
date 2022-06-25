---
title: "Java基础之IO流"
date: 2022-06-16T17:24:19+08:00
draft: false
categories: ["java基础"]
tags: ["IO流","基础"]
---

## 字符编解码

* 字符集 GBK UTF-8
* GBK: 汉字占两个字节
* UTF-8: 汉字占三个字节
* 任何字符集中英文和数字都占一个字节

```java
/**
 * 字符集 GBK UTF-8
 * GBK: 汉字占两个字节
 * UTF-8: 汉字占三个字节
 * 任何字符集中英文和数字都占一个字节
 */
public class CharsetTest {
    public static void main(String[] args) throws UnsupportedEncodingException {
        //1.编码
        String name = "123明里的博客";
        byte[] bytes = name.getBytes("GBK");
        System.out.println(bytes.length);
        System.out.println(Arrays.toString(bytes));

        String nstr=new String(bytes,"GBK");
        System.out.println(nstr);
    }
}
```

## IO流

### 字节流：InputStream、OutputStream

- 字节输入流：一般用```FileInputStream```
```java
public class IOStream {
    public static void main(String[] args) throws Exception {
        //1.传入文件路径，创建一个文件字节输入流
        InputStream fileInputStream = new FileInputStream("java_study/src/main/resources/IOStream.txt");

        //2.读取一个字节,每次读一个字节
        int read = fileInputStream.read();
        System.out.println((char)read);

        //使用字节数组读
//        byte[] buffer = new byte[9];

//        fileInputStream.read(buffer);
//        System.out.println(new String(buffer));

          //读取全部字节,jdk9
//        byte[] all=fileInputStream.readAllBytes();
//        System.out.println(new String(all));

    }
}

```
- 字节输出流：一般用```FileOutputStream```

```java
     //1.传入文件路径，创建一个文件字节输出流
        OutputStream os = new FileOutputStream("java_study/src/main/resources/IOStreamOutput.txt");
        try {
            //2.写数据
            os.write(98);
            os.write("你好".getBytes());
            os.flush(); //刷新
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            os.close();
        }
```

> 无法避免读取中文乱码的问题,**适合用于文件数据的拷贝**

### 字节流实例--复制文件

```java
public class CopyFile {
    public static void main(String[] args) {
        InputStream ins = null;
        OutputStream os = null;
        try {
            //1.创建字节输入流
            ins = new FileInputStream("java_study/src/main/resources/12.jpeg");

            //2.创建字节输出流
            os = new FileOutputStream("java_study/src/main/resources/1222.jpeg");

            //3.定义字节数组
            byte[] buffer = new byte[1024];
            int len = 0; //使用len来记录读取了多少字节，方便读多少写多少

            //4.循环遍历读取
            while ((len = ins.read(buffer)) != -1) {
                os.write(buffer, 0, len);
                //使用len来记录读取了多少字节，方便读多少写多少，不会多写重复的
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //释放资源
            try {
                if(ins!=null) ins.close();
                if(os!=null) os.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }
    }
}

```

### 字符流：Reader、Writer

> 读取中文的文本，推荐使用字符流

- 字符输入流：```FileReader```

```java
public static void main(String[] args) throws Exception {

    //字符流
    Reader reader=new FileReader("java_study/src/main/resources/IOStream.txt");

    //读一个
    //        int read = reader.read();
    //        System.out.println((char)read);

    //使用字符数组，读一堆
    char[] buffer =new char[10];
    int len=0;
    while((len=reader.read(buffer))!=-1){
        String content=new String(buffer,0,len);
        System.out.print(content);
    }
}
```

- 字符输出流：```FileWriter```

```java
public static void main(String[] args) throws Exception {

    Writer writer=new FileWriter("java_study/src/main/resources/IOStream22.txt");
	//append设置为true,则为追加数据
    //Writer writer=new FileWriter("java_study/src/main/resources/IOStream22.txt"，true);
    
    //直接写字符串
    writer.write("这是字符流写数据");
    writer.write(98);
    writer.write("\n");

    char [] chars={'你','d','2'};
    writer.write(chars,0,2);

    //刷新内存，将内容写入到硬盘中
    //刷新后可以继续写
    writer.flush();

    //close会触发刷新，但是close后无法再继续写
    writer.close();

}

```



