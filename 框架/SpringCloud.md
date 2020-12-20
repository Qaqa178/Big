#     1.版本选择

### `1.SpringBoot`和`SpringCloud`版本依赖选择

### ![image-20201220080435077](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220080435077.png)

### 2.版本对应查看方法

```java
http://start.spring.io/actuator/info
返回一个json串，需要打开一个json处理网站进行格式化
    tool.lu
```

### 3.学习使用的各个技术版本

![image-20201220081054881](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220081054881.png)

# 2.`cloud`组件的停更/替换/升级

### 1.以前

![image-20201220081733488](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220081733488.png)

### 2.现在

![image-20201220082842849](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220082842849.png)

# 3.项目构建

### 1.约定 > 配置 > 编码

### 2.`IDEA`新建Project工作空间

1.new Project

2.聚合总父工程名字

3.Maven选版本

4.工程名字

5.字符编码

![image-20201220093249312](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220093249312.png)

6.注解生效激活

![image-20201220093528452](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220093528452.png)

7.Java版本选择8

![image-20201220093508259](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220093508259.png)

8.File Type过滤

![image-20201220093820913](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220093820913.png)

3.父工程的pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.cheng.cloud</groupId>
    <artifactId>cloud2020</artifactId>
    <version>1.0-SNAPSHOT</version>

    <packaging>pom</packaging>

    <!--统一管理jar包版本-->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>12</maven.compiler.source>
        <maven.compiler.target>12</maven.compiler.target>
        <junit.version>4.12</junit.version>
        <lombok.version>1.18.10</lombok.version>
        <log4j.version>1.2.17</log4j.version>
        <mysql.version>8.0.18</mysql.version>
        <druid.version>1.1.16</druid.version>
        <mybatis.spring.boot.version>2.1.1</mybatis.spring.boot.version>
    </properties>

    <!--子模块继承之后，提供作用：锁定版本+子module不用谢groupId和version-->
    <dependencyManagement>
        <dependencies>
            <!--mybatis-->
            <dependency>
                <groupId>org.mybatis.spring.boot</groupId>
                <artifactId>mybatis-spring-boot-starter</artifactId>
                <version>${mybatis.spring.boot.version}</version>
            </dependency>
            <!--junit-->
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <version>${junit.version}</version>
            </dependency>
            <!--log4j-->
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <version>${log4j.version}</version>
            </dependency>

            <dependency>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-project-info-reports-plugin</artifactId>
                <version>3.0.0</version>
            </dependency>
            <!--spring boot 2.2.2-->
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>2.2.2.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud Hoxton.SR1-->
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR1</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--spring cloud 阿里巴巴-->
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>2.1.0.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <!--mysql-->
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <version>${mysql.version}</version>
                <scope>runtime</scope>
            </dependency>
            <!-- druid-->
            <dependency>
                <groupId>com.alibaba</groupId>
                <artifactId>druid</artifactId>
                <version>${druid.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

![image-20201220101204719](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220101204719.png)

![image-20201220101517559](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220101517559.png)

### 3.`Rest`风格工程构建

![image-20201220101942796](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220101942796.png)

![image-20201220102245545](C:\Users\cheng\AppData\Roaming\Typora\typora-user-images\image-20201220102245545.png)

##### 1.构建步骤

1.支付模块

- 建cloud-provider-payment8001工程

- 改pom

  ```xml
  <dependencies>
          <!-- 包含了sleuth zipkin 数据链路追踪-->
  <!--        <dependency>-->
  <!--            <groupId>org.springframework.cloud</groupId>-->
  <!--            <artifactId>spring-cloud-starter-zipkin</artifactId>-->
  <!--        </dependency>-->
  <!--        <dependency>&lt;!&ndash; 引用自己定义的api通用包，可以使用Payment支付Entity &ndash;&gt;-->
  <!--            <groupId>com.eiletxie.springcloud</groupId>-->
  <!--            <artifactId>cloud-api-commons</artifactId>-->
  <!--            <version>${project.version}</version>-->
  <!--        </dependency>-->
  
  
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-web</artifactId>
          </dependency>
          <!--监控-->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-actuator</artifactId>
          </dependency>
          <dependency>
              <groupId>org.mybatis.spring.boot</groupId>
              <artifactId>mybatis-spring-boot-starter</artifactId>
          </dependency>
          <!--eureka client-->
          <dependency>
              <groupId>org.springframework.cloud</groupId>
              <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
          </dependency>
          <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>druid-spring-boot-starter</artifactId>
              <version>1.1.16</version>
          </dependency>
  
          <!--mysql-connector-java-->
          <dependency>
              <groupId>mysql</groupId>
              <artifactId>mysql-connector-java</artifactId>
          </dependency>
          <!--jdbc-->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-jdbc</artifactId>
          </dependency>
          <!--热部署-->
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-devtools</artifactId>
              <scope>runtime</scope>
              <optional>true</optional>
          </dependency>
          <dependency>
              <groupId>org.projectlombok</groupId>
              <artifactId>lombok</artifactId>
              <optional>true</optional>
          </dependency>
          <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-starter-test</artifactId>
              <scope>test</scope>
          </dependency>
      </dependencies>
  ```

  

- 写yml

  ```yml
  server:
    port: 8001
  
  spring:
    application:
      name: cloud-payment-service
    datasource:
      type: com.alibaba.druid.pool.DruidDataSource
      driver-class-name: com.mysql.jdbc.Driver
      url: jdbc:mysql//localhost:3306/db2019?useUnicode=true&characterEncoding=utf-8&useSSL=false
      username: root
      password: 123456
  
  mybatis:
    mapper-locations: classpath:mapper/*.xml
    type-aliases-package: com.cheng.springcloud.entitis
  ```

  

- 主启动

  ```java
  package com.cheng.springcloud;
  
  import org.springframework.boot.SpringApplication;
  import org.springframework.boot.autoconfigure.SpringBootApplication;
  
  @SpringBootApplication
  public class PaymentMain8001 {
  
      public static void main(String[] args) {
          SpringApplication.run(PaymentMain8001.class,args);
      }
  }
  
  ```

  

- 业务类

  - 建表SQL

  - entities

    - 主实体Payment

    ```java
    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    public class Payment implements Serializable {
    
        private Long id;
        private String serial;
    }
    
    ```

    - `Json`封装体`CommonResult`

  - dao

    - 接口

    ```java
    @Mapper
    public interface PaymentDao {
    
        public int create(Payment payment);
    
        public Payment getPaymentById(@Param("id") Long id);
    }
    
    ```

    - mybatis映射文件PaymentMapper

      

  - service

  - controller

- 测试

- 小结

2.热部署Devtools

3.订单模块

4.工程重构

##### 2.目前工程样图