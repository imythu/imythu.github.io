---
title: spring-boot 源码 - 1
body:
  - article
  - related_posts
  - comments
date: 2020-07-05 19:12:53
tags: [Java, 单例, 原理]
top: 单例原理
---
## 开始看 spring-boot 源码前需要准备的东西
<!-- more -->
### 前提
- 1、熟悉 Java
- 2、知道 spring 怎么用 
### 下载并导入源码（第一种方法，导入失败网上搜原因或者其他的教程）
- 本机下载 git，然后找个喜欢的目录 cmd 执行 ``git clone https://github.com/spring-projects/spring-boot.git``，当然也可以下载其他版本
- 下载好之后先不着急用 IDEA 打开，先编辑项目根目录的 build.gradle 文件中的 `allproject`下的 `repositories`，添加阿里云仓库，不然导入会很 `dan teng`，修改方式如下图
<fancybox> ![](https://img-read.oss-cn-hangzhou.aliyuncs.com/img/repo.png) </fancybox>


- 然后用 IDEA 打开项目，编译器会自动导入并构建，这个过程比较漫长，等待即可

### 源码有了，从何看起？
#### 源码下好了，也自动编译了，但是不知道从哪儿开始看。有几个思路：
- 第一个，如果是可以直接启动的 Java 程序都是有 main 方法的，可以用编译器找找 main 方法在哪儿
- 第二个，想想平时使用此框架的时候是怎么使用的，比如 spring boot 会有一个 main 方法 ，ssm 框架会在 web.xml 中配置一个 spring 的类，一般这种就是启动类
- ps: 这两个思路也可以用在其他地方，比如 Tomcat 源码肯定就是去找 main 方法，因为 Tomcat 是个可以直接启动的 Java 程序嘛 
#### 所以可以根据以上思路来找开头
- 用过 spring boot 项目的都知道至少开发时spring boot是从 main 方法启动的。
查看启动类可以知道启动spring 环境调用的方法是``org.springframework.boot.SpringApplication#run(java.lang.Class<?>, java.lang.String...)``，
这个类就是开始看的第一个类。
- 通过方法 ``org.springframework.boot.SpringApplication#run(java.lang.Class<?>, java.lang.String...)`` ，去查看类 ```` ，然后查看类结构，查看启动方法的调用链
- 调用链有以下几步：
    - 第一步
    <fancybox> ![](https://img-read.oss-cn-hangzhou.aliyuncs.com/img/invoke-1.png) </fancybox>
    - 第二步
    <fancybox> ![](https://img-read.oss-cn-hangzhou.aliyuncs.com/img/invoke-2.png) </fancybox>
    - 第三步
    <fancybox> ![](https://img-read.oss-cn-hangzhou.aliyuncs.com/img/invoke-3.png) </fancybox>
    - 第四步
    <fancybox> ![](https://img-read.oss-cn-hangzhou.aliyuncs.com/img/invoke-4.png) </fancybox>
- 剩下的就是后续的步骤了，下个文章在写

#### [参考： spring boot readme](https://github.com/spring-projects/spring-boot#modules)