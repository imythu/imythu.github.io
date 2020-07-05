---
title: Jpa 联合主键配置
body:
  - article
  - related_posts
  - comments
date: 2020-03-26 20:57:55
tags: [spring, spring data, spring jpa, jpa, hibernate, 后端, 后台]
---
### jpa 联合主键方法

- 1、主键的几个字段单独出来一个类，这个类实现 ``Serializable`` 接口，类上添加注解 ``javax.persistence.Embeddable``，类具备基本的 ``getter、setter``方法
- 2、实体类中用``步骤1``中的那个类作为实例变量替换原来的联合主键涉及到的所有字段。并且在这个主键类的变量上用``javax.persistence.EmbeddedId``注解

##### 注意事项

- 1、不管是主键类还是实体类，都必须重写 ``equals``方法，因此不管主键类还是实体类也必须重写``hashCode``方法。重写的两种实现方法如下：
  - a、使用``lombok``的相应注解编译时自动生成
  - b、对于``equals``方法，所有字段比较结果相与（即``(&&)``）。对于``hashCodo``方法，使用``Objects.hash(Object... values)``方法，并传入类中的所有实例变量。
    - 注：对于``JDK``版本较低的可以自己写``hashCode``方法，方法参加``Effective Java第三版第十一条``，[电子版链接](http://sjsdfg.gitee.io/effective-java-3rd-chinese/#/notes/11.%20%E9%87%8D%E5%86%99equals%E6%96%B9%E6%B3%95%E6%97%B6%E5%90%8C%E6%97%B6%E4%B9%9F%E8%A6%81%E9%87%8D%E5%86%99hashcode%E6%96%B9%E6%B3%95)