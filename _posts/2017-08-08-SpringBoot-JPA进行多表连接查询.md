---
layout: post
title:  "SpringBoot-JPA进行多表连接查询"
date:   2017-08-08 22:56:31 +0800
categories: SpringBoot
---


#通过JPA进行简单的(内)连接查询
## 1.准备
### 1.1开发工具Intellij Idea
### 1.2数据库mysql
### 1.3新建Spring Initializr项目，勾选web,mysql,rest,jpa依赖
## 2.开始
### 2.1项目结构
![这里写图片描述](http://img.blog.csdn.net/20170808224321719?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcjhsOHE4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
### 2.2pom.xml内容
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
### 2.3Author类内容；
```
package com.example.demo;

import javax.persistence.*;
import java.util.List;

@Entity
public class Author {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int Id;
    private String name;
    private int age;
    @OneToMany( mappedBy = "author" )//作者与书 是一对多的关系，mappedBy 指的是通过  book表中的author字段来映射，mappedBy一般用来设置为关联表的外键字段，这里Book实体中的外键字段为author，则设置为author,设置为别的字段没法进行连接查询
    private List<Book> books;

    public void setId(int id) {
        Id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setBooks(List<Book> books) {
        this.books = books;
    }

    public int getId() {

        return Id;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public List<Book> getBooks() {
        return books;
    }
}

```
### 2.4Book类内容：
```
package com.example.demo;

import javax.persistence.*;

@Entity
public class Book {
    @javax.persistence.Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int Id;
    private String name;
    private float price;
    @JoinColumn
    @ManyToOne(cascade = CascadeType.ALL)
    //book与作者是多对一的关系，JoinColumn用来加入外键，Cacade 用来级联，CascadeType.Remove 指级联删除，被关联表元祖删除，关联表的对应元祖也会被删除，这里book是关联表，通过外键字段author(在数据库中为author_id外键）关联author表，author是被关联表
    // 同样 CascadeType.Persist则是级联存储，其他几个 对应他们的本身单词意思
    //CascadType.ALL 则包含了所有级联操作
    private Author author;

    public void setId(int id) {
        Id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setPrice(float price) {
        this.price = price;
    }

    public void setAuthor(Author author) {
        this.author = author;
    }

    public int getId() {

        return Id;
    }

    public String getName() {
        return name;
    }

    public float getPrice() {
        return price;
    }

    public Author getAuthor() {
        return author;
    }
}
```
### 2.5BookRepository接口内容：
```
package com.example.demo;

import org.springframework.data.repository.CrudRepository;
public interface AuthorRepository extends CrudRepository<Author,Integer> {
}

```
### 2.6AuthorRepository接口内容：
```
package com.example.demo;

import org.springframework.data.repository.CrudRepository;

public interface BookRepository extends CrudRepository<Book,Integer> {

}

```
### 2.7ManiController类内容：
```
package com.example.demo;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.condition.ConditionalOnBean;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
@Controller
//这个只是为了方便导入测试数据的控制器
public class MainController {
    @Autowired
    private BookRepository bookRepository;
    @RequestMapping("/initData")
    @ResponseBody
    public Iterable<Book> index(){
    //导入测试数据
        for(int i=1;i<10;++i){
            Book book=new Book();
            Author author=new Author();
            author.setName("ar "+i);
            author.setAge(i*20);

            book.setName("bk "+i);
            book.setPrice(i*15);
            book.setAuthor(author);

            bookRepository.save(book);
        }
        return bookRepository.findAll();
    }
}

```
### 2.8DemoApplication类内容：
```
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}

}

```
### 2.9application.properties内容：
```
spring.jpa.hibernate.ddl-auto=create
spring.datasource.url=jdbc:mysql://localhost:3306/你的数据库
spring.datasource.username=你的数据库账号
spring.datasource.password=你的数据库密码
```
## 3.测试
###3.1先访问localhost:8080/initData初始化下测试数据，
###3.2访问localhost:8080/books 和localhost:8080/authors进行测试，由于前面我们勾选了rest依赖，所以spring自动帮我们生成好了restful 风格的http接口，通过这2个网址可以进行get查询，可以看到查询book的时候book里的author字段也会展示出来.