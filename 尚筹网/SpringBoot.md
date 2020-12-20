##### 1.SpringBoot是什么？

- SpringBoot是对spring进一步的封装

![1602720855565](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602720855565.png)

- SpringBoot的优势
  - 配置简单
    - springBoot是对spring的进一步封装，基于注解开发，舍弃笨重的xml，确实需要配置的使用yml或properties进行简要配置即可
  - 产品级独立运行
    - 每一个工程都可以打成一个jar包，其中内置了Tomcat或其他的Servlet容器，可以独立运行，这是和微服务理念最为契合的点
  - 强大的场景启动器
    - 每一个特定场景下的需求都封装成一个starter，只要导入这个Starter就有了这个场景所需要的一切，其中包括针对这个场景的自动化配置、依赖信息

![1602721635402](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602721635402.png)

##### 2.创建工程

- 在pom中加入下列依赖

```xml

<!--    继承SpringBoot官方指定的父工程-->

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
    </parent>

    <dependencies>

<!--        加入web开发所需要的场景启动器-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
<!--                <version>2.1.6.RELEASE</version>-->
            </plugin>
        </plugins>
    </build>

```



```java
// 将当前类标记为一个SpringBoot应用
@SpringBootApplication
public class SpringBootMainType {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootMainType.class,args);
    }
}




@RestController
public class HelloHandler {

    @RequestMapping("/get/spring/boot/hello/message")
    public String getMessage(){
        return "first blood!!";
    }
}



```

- Helloword解读
  - @SpringBootApplication
    - 标记在一个主程序类上，说明这是一个SpringBoot应用，name这个类中的main()方法就是整个SpringBoot应用的入口，就是说要通过运行这个main（）方法启动SpringBoot应用
  - SpringApplication.run(……)
    - 启动柜SpringBoot应用，后续要加载很多内置配置文件，根据自动化配置加载很多组件到IOC容器
  - 包扫描方式：按照约定规则自动扫描
    - 在SpringBoot环境下，主启动类所在包、主启动类所在包的子包会被自动扫描
  - 包扫描方式：手动指定
    - 在主启动类上使用@ComponentScant注解可以指定扫描的包，但是此时约定规则会失效

##### 3.springBoot配置文件

- SpringBoot配置文件有两种，分别是properties和yml文件，
- SpringBoot配置文件需要注意以下几点：
  - 文件保存位置是main/resources目录
  - 文件名是application.properties或application.yml

![1602729006270](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602729006270.png)

![1602729107593](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602729107593.png)

![1602729445206](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602729445206.png)

![1602729543091](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602729543091.png)

![1602733174320](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602733174320.png)

##### 4.注解

![1602742726239](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602742726239.png)

![1602743114908](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602743114908.png)

![1602743179433](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602743179433.png)

![1602743198046](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602743198046.png)

![1602743276293](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602743276293.png)

##### 5.SpringBoot基本原理

![1602744493623](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602744493623.png)

##### 6.整合mybatis

- 加入依赖

  ```xml
   <dependency>
              <groupId>org.mybatis.spring.boot</groupId>
              <artifactId>mybatis-spring-boot-starter</artifactId>
              <version>2.1.0</version>
          </dependency>
  
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
          </dependency>
  
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid</artifactId>
              <version>1.0.5</version>
          </dependency>
  ```

- Mapper配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8" ?>
  <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
  <mapper namespace="com.cheng.spring.boot.mapper.EmpMapper" >
  
      <select id="selectAll" resultType="com.cheng.spring.boot.entity.Emp">
          select emp_id,emp_name empName,emp_age empAge
          from
          table_emp
      </select>
  
  </mapper>
  ```

- 创建实体类、Mapper接口

  ![1602755405095](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602755405095.png)

![1602755412163](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602755412163.png)

- yml配置文件

  ```yaml
  #spring:
  #  datasource:
  #    name: mydb
  #    type: com.alibaba.druid.pool.DruidDataSource
  #    url: jdbc:mysql://localhost:3307/springboot?serverTimezone=UTC
  ##    url: jdbc:mysql://127.0.0.1:3307/springboot?serverTimezone=UTC
  #    username: root
  #    password: 123456
  #    driver-class-name: com.mysql.cj.jdbc.Driver
  #
  #mybatis:
  #  mapper-locations: classpath*:/mapper/*Mapper.xml
  #
  #
  #logging:
  #  level:
  #    com.cheng.spring.boot.mapper: debug
  #    com.cheng.spring.boot.test: debug
  ```

  

##### 7.整合Redis

##### 8.整合Thymeleaf



![1602816993644](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602816993644.png)

![1602826790788](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602826790788.png)

![1602827152584](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602827152584.png)

![1602827293941](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602827293941.png)