##### 1.springcloud核心

- 基于HTTP协议

##### 2.springcloud组件

- 注册中心：Eureka
- 客户端负载均衡：Ribbon
- 声明式远程方法调用：Fergn
- 服务降级、熔断：Hystrix
- 网关：Zuul

![1602921262453](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921262453.png)3.准备测试环境

![1602921297507](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921297507.png)

![1602921532595](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921532595.png)

![1602921311347](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921311347.png)

- 添加依赖

  ```xml
  <dependencyManagement>
  <dependencies>
  <!-- 导入 SpringCloud 需要使用的依赖信息 -->
  <dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-dependencies</artifactId>
  <version>Greenwich.SR2</version>
  <type>pom</type>
  <!-- import 依赖范围表示将 spring-cloud-dependencies 包中的依赖信息导入 -->
  <scope>import</scope>
  </dependency>
  <!-- 导入 SpringBoot 需要使用的依赖信息 -->
  <dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-dependencies</artifactId>
  <version>2.1.6.RELEASE</version>
  <type>pom</type>
  <scope>import</scope>
  </dependency>
  </dependencies>
  </dependencyManagement>
  ```

  ![1602921343971](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921343971.png)

![1602921351899](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921351899.png)

![1602921375349](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921375349.png)

![1602921386216](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921386216.png)

![1602921395005](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921395005.png)

##### 4.创建Eureka注册中心工程

![1602921436945](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921436945.png)

![1602921447664](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921447664.png)

![1602921463826](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602921463826.png)

##### 5.Feign用法

![1602922925281](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602922925281.png)