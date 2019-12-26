---
title: 安装 MySQL 8
body:
  - article
  - related_posts
  - comments
date: 2019-12-26 16:32:38
tags: [mysql, MySQL, 后端, 后台, 数据库]
---
##  MySQL 8.0.18 安装
<fancybox>
    {% asset_img pentakill.jpg 五杀 %}
</fancybox>
<!-- more -->

---

-   1.下载解压
-   2.管理员身份运行``cmd``
    -   进入管理员``cmd``方法：按一下``Windows``键，然后按英文输入法``cmd``键，在显示的搜索结果处右键``cmd``那一行，点击以管理员身份运行，后面如果弹出安全警告，选择是
-   3.初始化``data``文件夹。
    -   进入解压后的``MySQL``根目录
    -   执行命令 
        ```shell script
        bin\mysqld --initialize --user=mysql --console
        ```
        -   注意上述命令最后的``--console``必须加上，这样才能在初始化的时候把密码打印到控制台，如下图，我的初始化密码就是``p7mGd3C1Dy_D``。**这个密码是``root``用户的初始密码，后面要用**
        -   <fancybox>
                {% asset_img initPwd.png 初始化密码 %}
            </fancybox>
-   4.启动``MySQL``
    -   第一种，命令行运行。在``MySQL``根目录下运行``bin\mysqld --console``。
    -   第二种，作为服务运行。
        -   进入解压后的``MySQL``根目录
        -   安装服务，执行命令
            ```shell script
            bin\mysqld --install
            ```
        -   启动``MySQL``，执行命令
            ```shell script
            net start mysql
            ```
-   5.修改``root``密码
    -   新开一个 ``cmd``窗口，执行命令``` mysql -uroot -p密码```，比如我的密码是``p7mGd3C1Dy_D``，所以我执行的命令是```mysql -uroot -pp7mGd3C1Dy_D```
    -   修改密码，执行命令``` ALTER USER 'root'@'localhost' IDENTIFIED BY '1234'```，这样密码就变成``1234``了，当然也可以改成其他复杂一点的密码
-   **注意，除了步骤``5``以外的所有命令都需要在具有管理员权限的``cmd``窗口中执行。**