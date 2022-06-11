# ssm-program

#### 介绍
在我跟着项目作者的视频教学搭建项目的过程中，踩了很多坑，会分享到这个文档当中，供自己记录学习，也供后来者参考。

本项目原po的视频讲解地址：https://www.bilibili.com/video/BV1VA411i7xX

## 1. 项目部署

### 1.1 系统环境配置

**软件的安装配置可以查看b站java攀登网001的相关视频**

* 系统开发平台：Tomcat8.0+JDK1.8+Windows7

* 开发语言：JavaEE

* 框架：SSM-Spring+SpringMVC+Mybatis

* 前端：JSP

* 数据库:MySql5.7

* 开发环境：Intelij Idea

* 浏览器：Chrome

### 1.2 导入数据库（Navicat）

* 右键点击连接，新建数据库(MySql的工具可以使用navicat)

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/175144_369e84e2_7604956.png "屏幕截图.png")

* 右键点击刚建立的数据库，选择运行sql文件，选择项目文件中的shop.sql文件，点击开始

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/175257_31ef2986_7604956.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/175314_c100adbd_7604956.png "屏幕截图.png")

### 1.3 导入项目

* 这里项目源码并没有包含testMaven这个project，我们直接使用水果商城的项目来调试，方法类似，参考up主教学视频
* 打开idea-open，选择项目文件，点击ok

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/175426_2afd1577_7604956.png "屏幕截图.png")

* 设置jdk版本为1.8

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/191303_d458c0ea_7604956.png "屏幕截图.png")

* 设置maven的版本的位置信息，点击ok
  这里maven的路径记得修改成与自己的安装路径一致（随意，但是记得路径不要带中文，最好不要有空格）
  maven目录中的 settings.xml 文件要配置 \<mirrors\> 的仓库地址，中国大陆用户改成华为或者阿里的镜像库（参考视频），提高下载速度

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/191352_2c218e1a_7604956.png "屏幕截图.png")

* 添加tomcat

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/191547_84533623_7604956.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/191730_afbc4395_7604956.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/191752_65aec195_7604956.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/191845_20c93ed6_7604956.png "屏幕截图.png")

* 这里考察pom.xml文件，会发现有很多依赖包无法安装

  搜索之后尝试在项目目录（也就是与 pom.xml 同级的目录）下打开命令行，输入指令 

  ```
  mvn clean install -U
  ```

  这个指令会检索 pom.xml 中缺失的依赖文件，自动下载（也就是俗称“打包”）

  （请确保环境变量中已经配置了 JAVA_HOME="你的jdk目录路径" 语句，以及 maven 的环境变量配置，否则会报错）
  对于 \[Maven package error: org.apache.commons-lang does not exist (Java)]\(https://stackoverflow.com/questions/7339264/maven-package-error-org-apache-commons-lang-does-not-exist-java\) 错误，考虑以如下命令解决

  ```
  mvn dependency:tree
  mvn help:effective-pom
  mvn dependency:copy-dependencies
  ```
  
  对于 Plugin 'maven-war-plugin:' not found 错误，考虑在 pom.xml 中添加如下依赖
  
  ```
  <dependency>
  	<groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-war-plugin</artifactId>
      <version>3.3.1</version>
      <type>maven-plugin</type>
  </dependency>
  ```
  
  对于 commons-lang 相关的报错，可添加如下依赖
  ```
  <dependency>
      <groupId>commons-lang</groupId>
      <artifactId>commons-lang</artifactId>
      <version>2.4</version>
  </dependency>
  ```
  
  对于 fastjson 相关包的错误，可以添加如下依赖
  ```
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>fastjson</artifactId>
              <version>1.2.76</version>
          </dependency>
  ```
  
  对于 Bad class file 的报错，可以考虑将对应报错的库所在的依赖包，版本回退到上一个版本，如 java: cannot access org.apache.solr.client.solrj.beans.Field
   bad class 
  我们可以将依赖包中 solr 的版本修改为 8.11.1
 
* 点击运行

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/192240_b8f275da_7604956.png "屏幕截图.png")

  如果运行tomcat时出现 springframework, commons-dbcp 相关的 beans 报错，可以添加如下依赖
  ```
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-elasticsearch</artifactId>
    <version>3.2.5.RELEASE</version>
  </dependency>
  <dependency>
    <groupId>commons-dbcp</groupId>
    <artifactId>commons-dbcp</artifactId>
    <version>1.2.2</version>
  </dependency>
  ```
  如果出现 PageHelper \(分页插件\) 报错，考虑使用4.0.0以后版本的接口 PageIntercepter \(在\\src\\mybatis\\sqlMapConfig.xml 文件中配置 \)
  找到此配置
  ```
  	    <plugin interceptor="com.github.pagehelper.PageHelper">
	        <!-- 4.0.0以后版本可以不设置该参数 -->
            <property name="dialect" value="mysql"/>
  ```
  第一个 \<property\> 的功能是指定数据库，由于 PageIntercepter 接口可以自动识别数据库，所以可以关掉该参数，即修改为
  ```
  	    <plugin interceptor="com.github.pagehelper.PageInterceptor">
	        <!-- 4.0.0以后版本可以不设置该参数 -->
            <!--<property name="dialect" value="mysql"/>-->
  ```
* 输入localhost:83/test1_war_exploded进入首页，启动项目成功！

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/192420_487403e8_7604956.png "屏幕截图.png")

## 2. 系统介绍

### 2.1 前端功能

* 启动项目成功后，输入localhost:83/test_war_exploded/login/index进入商城首页

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/192554_d0cebdde_7604956.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/192728_c629e350_7604956.png "屏幕截图.png")

* 点击某一个商品即可查看商品的详情页面

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/192757_b5371a59_7604956.png "屏幕截图.png")

* 收藏与加入购物车功能，需要登录才可以使用此功能

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193415_fefd8170_7604956.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193452_e2fde2f2_7604956.png "屏幕截图.png")

* 留言功能

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193520_91311fd1_7604956.png "屏幕截图.png")

* 个人中心

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193544_6036ddaf_7604956.png "屏幕截图.png")

### 2.2 后端功能

* 启动项目成功后，输入localhost:83/test_war_exploded/login/login.html进入后端登录页面

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193720_8aaf6fd4_7604956.png "屏幕截图.png")

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193734_0ded67d6_7604956.png "屏幕截图.png")

* 类目管理功能：对应前端页面商品的分类，可以在这里增加和删除类目

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193823_88b6d52b_7604956.png "屏幕截图.png")

* 用户管理

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193918_e8c6b876_7604956.png "屏幕截图.png")

* 商品管理

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/193936_f6d98cb2_7604956.png "屏幕截图.png")

* 订单管理

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194000_d55c8dae_7604956.png "屏幕截图.png")

* 公告管理

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194017_6f85cda9_7604956.png "屏幕截图.png")** 

* 留言管理

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194045_51a611c5_7604956.png "屏幕截图.png")

## 3. 项目部分内容详细讲解

### 3.1 pom.xml文件

用maven来管理jar包的话，项目目录中会有一个pom.xml文件

项目也可以通过maven-package来打包

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194349_5f3f6ffa_7604956.png "屏幕截图.png")

pom.xml中也声明了需要使用的软件的版本号

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194500_10b7412a_7604956.png "屏幕截图.png")

spring核心包的引入

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194553_0b62343e_7604956.png "屏幕截图.png")

### 3.2 web.xml

所有javaweb项目的入口文件

定义默认页面

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194732_fbd5a648_7604956.png "屏幕截图.png")

定义servlet中的拦截是用springmvc来实现的

![输入图片说明](https://images.gitee.com/uploads/images/2020/1224/194933_8f9b20e3_7604956.png "屏幕截图.png")

**更多的详细内容可以参考视频！**

**欢迎大家一起交流学习，共同进步。技术交流群，请加小孟微信：**

<div align=center><img width="250" height="250" src="https://images.gitee.com/uploads/images/2020/1219/203754_ef21dcd8_7604956.png"/></div>