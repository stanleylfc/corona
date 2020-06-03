+++
title="java sprint boot实战（一）| "
tags=["spring boot"]
categories=["spring boot"]
date="2020-05-20T21:00:00+08:00"
+++
## 1. spring boot 创建


## 2. maven spring boot helloworld
### 2.1 创建一个maven工程
- 启用maven 自动加载
### 2.2 导入spring 相关依赖
```
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.5.RELEASE</version>
    </parent>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
```
### 2.3 编写主程序,启动spring boot应用
```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldMainApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloWorldMainApplication.class, args);
    }
}

```
### 2.4 编写相关controller, service
```
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class HelloWorldMainApplication {
    public static void main(String[] args) {
        SpringApplication.run(HelloWorldMainApplication.class, args);
    }
}
```
### 2.5 运行主程序
### 2.6 简化部署
```
  <build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
  </build>
```

## 3. Hello World 探究
### 3.1 POM文件
### 父项目
### 导入的依赖