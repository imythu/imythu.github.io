---
title: Java 单例类实现及其原理
body:
  - article
  - related_posts
  - comments
date: 2019-08-26 17:17:30
tags: [Java, 单例, 原理]
top: 单例原理
---
## Java 如何写一个单例类？它的实现原理是什么？为什么要这么写？看完这篇文章你就懂了
<!-- more -->
## 首先，单例类是什么？
- **单例类就是那个类只会存在一个实例，就像 static 修饰的变量一样，一个类独一份。**

## 那怎么写一个单例类呢？
- 第一种，上面不是提到一个 static 修饰符吗？第一种就用它了。
    ```java
    public class Singleton {
        // 直接在类加载的时候就实例化了
        private static Singleton singleton = new Singleton();
        private Singleton() {}
    }
    ```
- 第二种，在需要这个类的时候才实例化一个类。为了说清楚原理，所以就一步步慢慢来。
    - 首先看看下面这个有什么问题.
        ```java
        public class SingletonLazy {
            private static SingletonLazy singleton;
            private SingletonLazy() {}
            public static SingletonLazy getInstance() {
                if (singleton == null) {
                    singleton = new SingletonLazy();
                }
                return singleton;
            }
        }
        ```
        - 这个代码单线程是没问题的，那如果是多个线程呢？比如两个线程同时判断到 singleton 是 null，两个都去初始化这个实例，或者一个线程还在初始化中，另一个线程判断不为 null 直接拿去用了，那就出问题了呀。
    - 上面的代码问题知道了，那怎么解决呢？最简单粗暴的方法就是把 ``public static SingletonLazy getInstance() {}``这个
    方法用``synchronized``修饰一下。
        ```java
        /**
        * 就像这样
        */
        public class SingletonLazy {
          private static SingletonLazy singleton;
          private SingletonLazy() {}
          synchronized public static SingletonLazy getInstance() {
              if (singleton == null) {
                  singleton = new SingletonLazy();
              }
              return singleton;
          }
        }
        ```
        -  好的，现在知道了加 ``synchronized``可以解决单例的线程安全问题。那新问题又来了，每次其他的类要拿这个单例类的实例时，不管需不需要初始化这个单例对象都要经过``synchronized``，
        要是获取单例对象次数一多，对性能的影响肯定是极大的。那又该怎么解决呢？
        - 去掉 ``synchronized``肯定是不行的。既然不能去掉，那就想办法减少加锁的次数嘛。
        - 那现在的问题就转化成了怎么把 ``synchronized``的加锁次数降到最低。从上面的多线程问题可知，问题是出在初始化单例对象的时候，那我们能不能只对这一部分加锁呢？
        答案肯定是可以的。不多 bb，直接上代码，这里用到了两次 if 判断，没看到过这种用法的可能一时半会儿还真想不出来。
            ```java
            public class SingletonLazy {
              private static SingletonLazy singleton;
              private SingletonLazy() {}
              public static SingletonLazy getInstance() {
                  if (singleton == null) {
                      synchronized (SingletonLazy.class) {
                          if (singleton == null) {
                              singleton = new SingletonLazy();
                          }
                      }
                  }
                  return singleton;
              }
            }
            ```
        - 现在你是不是以为问题就解决了？不可能，假的，都是假的，还是不行。

        - 现在来分析下为什么上面两个 if 会出问题。

            ```text
            先来看看new SingletonLazy();时做了些什么呢？
            memory = allocate();  // 1:分配对象的内存空间
            ctorInstance(memory); // 2：初始化对象
            instance = memory;    // 3：设置 instance 指向刚分配的内存地址
            ```
            - 2 和 3 这两步是可能发生重排序的(重排序指的是编译器和处理器为了优化程序性能而对指令进行重新排序的一种手段)，也就是 2 可能在 3 执行后再执行，
            由于**as-if-serial(as-if-serial 的语义是：不管怎么重排序，单线程程序的执行结果不能被改变)**的存在，
            这对单线程没什么影响，但是他并不限制多线程。
            - 回到上面的问题，由于这个**as-if-serial**并不限制多线程，所以上面的代码在多线程下，就有可能由于
            上述实例化对象步骤的 2 和 3 的重排序而导致 3 执行了，**对象已经不为 null 了，
            而这个时候对象还是不可用的，但是恰巧这时候这个对象被其他对象拿去用了**，这会发生什么呢？不知道。有兴趣可以多开点线程试试。
        - 现在知道问题的根源了。解决方法就出来了嘛。
            - 1.禁止 2 和 3 两个步骤发生重排序。
            - 2.可以让他们重排序，但是不能让其他的线程感知到这个重排序。这种方法就是直接在类加载的时候 new 一个，也就是文章开始时的那种方法。
        - **懒加载最终方法**，加个``volatile``嘛。
            ```java
            public class SingletonLazy {
              private static volatile SingletonLazy singleton;
              private SingletonLazy() {}
              public static SingletonLazy getInstance() {
                  if (singleton == null) {
                      synchronized (SingletonLazy.class) {
                          if (singleton == null) {
                              singleton = new SingletonLazy();
                          }
                      }
                  }
                  return singleton;
              }
            }
            ```
            - 最后，关于上面用的 ``volatile``什么意思，为什么能禁止重排序。
                - 定义：
                    - 1.当写一个 volatile 变量时，Java 内存模型(后面简称 JMM)会把该线程对应的本地内存中的共享变量值刷新到主内存。与锁的释放具有相同的内存语义。
                    - 2.当读一个 volatile 变量时，JMM 会把该线程对应的本地内存置为无效。线程接下来将会从主内存中读取共享变量。与锁的获取具有相同的内存语义。
                - 总结：
                    - 1.线程 A 写一个 volatile 变量，实质上是线程 A 向接下来将要读这个 volatile 变量的某个线程发出了(我要修改这个共享变量了)的消息。
                    - 2.线程 B 读一个 volatile 变量，实质上是线程 B 接收了之前某个线程发出的(在写这个 volatile 变量之前对共享变量所做修改的)消息。
                    - 3.线程 A 写一个 volatile 变量，随后线程 B 读这个 volatile 变量，这个过程实质上是线程 A 通过主内存向线程 B 发送消息。
                - 也就是说，对一个 volatile 变量的写操作对于 读这个 volatile 变量的操作是可见的，而这就保证了获取(即 **读**) singleton 变量时 singleton 已经实例化完成了。
                **而这里 volatile 作用实质上是禁止了实例化时步骤 2 和 3 的重排序**。

### 如有错误，请发邮箱 imythu@foxmail.com 指正。

- 转载请注明出处
- 作者：myth
- [博客地址](https://blog.inusha.cn/2019/08/26/singleton/)：https://blog.inusha.cn/2019/08/26/singleton/