---
layout: post
title:  "SpringBoot-访问MySQL数据库"
date:   2017-08-03 21:10:31 +0800
categories: SpringBoot
---
### 1.开发工具:Intellij Idea,
通过JPA(Java Persistence API,Java 数据持久化接口)来操作数据库
### 2.开始
#### 2.1新建Spring Initializr 项目，勾选web,mysql,jpa依赖
项目结构如下:
![这里写图片描述](http://img.blog.csdn.net/20170803205743129?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
####2.2代码清单:
(1)pom.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.6.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<scope>runtime</scope>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>


</project>

```
(2)User类代码：
```
package com.example.demo;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity //告诉Hibernate  用这个类在数据库中创建表
public class User {
    @javax.persistence.Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Integer Id;
    private String name;
    private String email;

    public void setId(Integer id) {
        Id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Integer getId() {

        return Id;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }
}

```
(3)UserRepositroy接口代码：
```
package com.example.demo;

import org.springframework.data.repository.CrudRepository;
//继承这个接口通过spring自动把userRepository这个对象实现为Bean
//用来对user表（对应user类自动创建的表）进行create,read,update,delete
public interface UserRepositroy extends CrudRepository<User,Long>{
}

```
(4)MainController类代码
```
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller //标明此类是个控制器类
@RequestMapping(path = "/demo")// 请求地址映射为*****（服务器地址)/demo
public class MainController {
    @Autowired
    private UserRepositroy userRepositroy;//UserRepositroy继承了CrudRepositroy接口,所以他的对象userRepositroy会自动被pring设置为Bean，然后自动装配
    @GetMapping(path = "/add")//请求地址映射 为*******/demo/add
    @ResponseBody //标明 方法返回的不是视图(view)而是字符串
    public String addNewUser(@RequestParam String name,@RequestParam String email){
        //@RequestParam 接受来get 或者post请求参数
        User n=new User();
        n.setName(name);
        n.setEmail(email);
        userRepositroy.save(n);
        return "Saved";
    }
    @GetMapping(path="/all")//请求地址映射 为*******/demo/all
    @ResponseBody
    public Iterable<User> getAllUsers(){
        return  userRepositroy.findAll();
    }
    @RequestMapping(path="/hello")//请求地址映射 为*******/demo/hello
    @ResponseBody
    public String helllo(){
        return "hello";
    }
}

```
(5)DemoApplication代码：
```
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {

		SpringApplication.run(DemoApplication.class, args);
	}
}

```
(7)application.properties代码：
```
#此为项目属性配置文件
spring.jpa.hibernate.ddl-auto=create
spring.datasource.url=jdbc:mysql://localhost:3306/practiceDB
spring.datasource.username=数据库账号
spring.datasource.password=数据库账号的密码
```
### 3.测试
访问:localhost:8080/demo/hello
浏览器显示：hello
浏览器输入:localhost:8080/demo/add?name=Tom&email=666666@qq.com
浏览器显示:saved，此时数据库已存入数据，查看数据库
+----+---------------+------+
| id | email                   | name |
+----+---------------+------+
|  1 | 666666@qq.com | Tom  |
+----+---------------+------+
浏览器访问:localhost:8080/demo/all
显示：[{"name":"Tom","email":"666666@qq.com","id":1}]