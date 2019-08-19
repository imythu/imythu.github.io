---
title: 编译并运行Tomcat9 源码
body:
  - article
  - related_posts
  - comments
date: 2019-08-16 17:30:08
tags:
---
如何做
<!-- more -->

#### 记录下Tomcat 9 源码如何编译成想管我二进制 zip 或者 tar.gz 包那种文件结构
##### 运行系统：Windows 10

# 一、源码编译
- 1、下载 Tomcat 9 源码，贴个链接[Tomcat9 zip 包](http://mirror.ox.ac.uk/sites/rsync.apache.org/tomcat/tomcat-9/v9.0.22/src/apache-tomcat-9.0.22-src.zip)。**然后解压到任意目录**
- 2、下载 ant 1.9.8 及以上版本，贴个链接[ant 1.9.14 zip 包](http://mirrors.ukfast.co.uk/sites/ftp.apache.org//ant/binaries/apache-ant-1.9.14-bin.zip)。解压到任意文件夹。
    - 环境变量配置。
        - 2.1、系统变量出新建一个 名为 ANT_HOME 的环境变量，值为上面解压后的 ant 根目录
        
        <fancybox>
            {% asset_img 1.png %}
        </fancybox>
        - 2.2、系统变量中的 Path 添加一个值，值为%ANT_HOME%\bin
        
        <fancybox>
            {% asset_img 2.png %}
        </fancybox>
- 3、进入到上面第一步解压的 Tomcat9 目录中 cmd 或者 git bash 工具执行 ant，等个七八分钟应该就编译好了，我编译了 7 分 28 秒。
    <fancybox>
        {% asset_img 3.png %}
    </fancybox>
    - 编译的时候会下载依赖 jar 包，下载到这儿的。111 换成电脑用户名。
        - 这个下载路径可以自定义的，在 解压后的 Tomcat 根目录下新建文件 build.properties，加入内容 base.path=lib，路径可以随便填。
        <fancybox>
            {% asset_img 4.png %}
        </fancybox>
    - 下载完的 jar 是这样的
        <fancybox>
            {% asset_img 5.png %}
        </fancybox>
# 二、idea 运行（需要先执行上面编译后得到相关 jar 包才能运行）
- 1、idea 打开上面解压的 Tomcat9 根目录。
    - 1.1、设置项目 SDK、Java Level、编译后文件目录分别为 jdk1.8、8、任意目录(我这儿就是项目目录下的output，这个目录也是上面 ant 编译后文件的存放目录)
        
        <fancybox>
            {% asset_img 6.png %}
        </fancybox>
- 2、设置 Modules 下的 Tomcat 的文件夹情况如下
    <fancybox>
        {% asset_img 7.png %}
    </fancybox>
- 3、引入依赖
    - 引入 ant 相关依赖，ant.jar、ant-commons-logging.jar、ant-apache-log4j.jar 三个包。这三个包**可以在上面解压的 ant 工具目录下的 lib 文件夹下找到**。引入方式，点Libraries 右边第一栏那个 +  号，然后选择包就可以了，一次可以按住 Ctrl 选择多个。
        <fancybox>
            {% asset_img 8.png %}
        </fancybox>
    - 引入其他依赖，这些依赖在 **源码编译** 时下载的文件里有，把下面的 jar 包引入进来
        
        <fancybox>
            {% asset_img 9.png  %}
        </fancybox>
- 4、在运行时需要 VM 参数 catalina.home，值是output/bulid(也就是上面源码 ant 编译后的目录)，即 VM Options 框内填入 -Dcatalina.home=output/build，运行项目根目录下 java\org\apache\catalina\startup\Bootstrap.java 类 Tomcat 就跑起来了。