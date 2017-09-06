---
layout: post
title:  "SpringBoot-创建RESTful风格的 http接口访问jpa操作数据库"
date:   2017-08-04 2:03:31 +0800
categories: SpringBoot
---
### 1.开发工具IDE:Intellij Idea，数据库mysql
（SpringBoot，创建RESTful 接口很方便，只要建立好数据模型，然后创建一个模型仓库接口就ok了）
### 2.开始开发
### 2.1 项目目录清单：
![这里写图片描述](http://img.blog.csdn.net/20170804202159794?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
新建SpringBoot Initializr 项目 选择jpa 和rest repository依赖，自己添加mysql-connector 类 用来连接mysql数据库
#### 2.2 pom.xml内容：

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
			<artifactId>spring-boot-starter-data-rest</artifactId>
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
#### 2.3 Person类代码：
```
package com.example.demo;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Person {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private long Id;
    private String firstName;
    private String lastName;

    public void setId(long id) {
        Id = id;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    public long getId() {

        return Id;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }
}

```
#### 2.4 PersonRepository接口代码：
```
package com.example.demo;

import org.springframework.data.repository.PagingAndSortingRepository;
import org.springframework.data.repository.query.Param;
import org.springframework.data.rest.core.annotation.RepositoryRestResource;
import org.springframework.stereotype.Repository;

import java.util.List;

@RepositoryRestResource(collectionResourceRel = "people",path = "people")//这个注解不是必须的，只是用来改变默认路径，如果去掉这行注解，默认路径就成*****/persons了
public interface PersonRepositroy extends PagingAndSortingRepository<Person,Long> {
    List<Person> findByLastName(@Param("name")String name);

}

```
#### 2.5 application.properties 配置文件代码:
```
spring.datasource.url=jdbc:mysql://localhost:3306/你的数据库
spring.datasource.username=账号
spring.datasource.password=密码
spring.jpa.hibernate.ddl-auto=create

```
### 3.测试
### 3.1访问htpp://locaohost:8080/  和http://localhost:8080/people  看一下
```
//http:localhost:8080 的返回
{
  "_links" : {
    "persons" : {
      "href" : "http://localhost:8080/persons{?page,size,sort}",
      "templated" : true
    },
    "profile" : {
      "href" : "http://localhost:8080/profile"
    }
  }
}
//http://localhost:8080/people的返回
{
  "_embedded" : {
    "people" : [ ]
  },
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/people{?page,size,sort}",
      "templated" : true
    },
    "profile" : {
      "href" : "http://localhost:8080/profile/people"
    },
    "search" : {
      "href" : "http://localhost:8080/people/search"
    }
  },
  "page" : {
    "size" : 20,
    "totalElements" : 0,
    "totalPages" : 0,
    "number" : 0
  }
}
```
#### 3.2
控制台输入一下命令 想数据库添加数据(我是linux系统，windows的可以用postman工具进行post测试
```
$ curl -i -X POST -H "Content-Type:application/json" -d "{  \"firstName\" : \"Frodo\",  \"lastName\" : \"Baggins\" }" http://localhost:8080/people
```
-i  显示响应的头文件信息
-X POST 表明发送的是post请求，用来 添加数据
-H "Content-Type:application/json" 设置请求内容为json格式的数据
-d "{ \"firstName\" : \"Frodo\", \"lastName\" : \"Baggins\" }"  要发送的Json数据 ,\  为转义字符表明要保留引号 
#### 3.3 其他的自己测试吧，比如findByLastName等等
