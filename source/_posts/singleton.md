---
title: Java 单例类实现及其原理
body:
  - article
  - related_posts
  - comments
date: 2019-08-26 17:17:30
tags:
---
Java 如何写一个单例类，它的实现原理是什么，为什么要这么写？
<!-- more -->
### 单例类是什么？
- **单例类就是那个类只会存在一个实例，就像 static 修饰的变量一样，一个类独一份。**
### 那怎么写一个单例类呢？
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
        **看上去好像没啥问题嘛。**