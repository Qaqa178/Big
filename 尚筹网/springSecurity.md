### `SpringSecurity`

##### 1.权限管理过程中的概念

- 主体
  - 谁使用系统，谁是主体
- 认证
  - 确认登录身份
- 授权
  - 给用户分配权限

![1602461639705](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602461639705.png)

##### 2.配置`SpringSecurity`

- 加入`SpringSecurity`的依赖

  ```xml
  <!-- SpringSecurity 对 Web 应用进行权限管理 -->
      <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-web</artifactId>
        <version>4.2.10.RELEASE</version>
      </dependency>
      <!-- SpringSecurity 配置 -->
      <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-config</artifactId>
        <version>4.2.10.RELEASE</version>
      </dependency>
      <!-- SpringSecurity 标签库 -->
      <dependency>
        <groupId>org.springframework.security</groupId>
        <artifactId>spring-security-taglibs</artifactId>
        <version>4.2.10.RELEASE</version>
      </dependency>
  ```

- 加入`SpringSecurity`的控制权限Filter

  ```xml
  <filter>
  <filter-name>springSecurityFilterChain</filter-name>
  <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
  </filter>
  <filter-mapping>
  <filter-name>springSecurityFilterChain</filter-name>
  <url-pattern>/*</url-pattern>
  </filter-mapping>
  ```

  特 别 注 意 ： `springSecurityFilterChain` 标 签 中 必 须 是
  `springSecurityFilterChain`。因为 `springSecurityFilterChain` 在 `IOC` 容器中对应真正执行权限
  控制的二十几个 Filter，只有叫这个名字才能够加载到这些 Filter。

  - 效果：所有的请求都被拦截了，要求登录才可以访问、
    - 静态资源也都被拦截，要求登录
    - 登录失败有错误提示

##### 3.实验1

- 放行首页和静态资源：范围小的放在前面，范围大的放在后面

  - 在配置类中重写父类的configure(HttpSecurity security)方法

    ```java
    // 注意！：这个类一定要放在自动扫描的包下，否则所有的配置都不会生效
    
    // 将当前类标记为配置类
    @Configuration
    // 启用web环境下权限控制功能
    @EnableWebSecurity
    public class WebAppSecurtyConfig extends WebSecurityConfigurerAdapter {
    
        @Override
        protected void configure(HttpSecurity security) throws Exception {
            security
                    .authorizeRequests()                            // 对请求进行授权
                    .antMatchers("/index.jsp")          // 针对路径进行授权
                    .permitAll()                                   // 可以无条件访问
                    .antMatchers("/layui/**")          // 针对/layui/**路径进行授权
                    .permitAll()                                  // 可以无条件访问
    
                    .and()
                    .authorizeRequests()                          // 对请求进行授权
                    .anyRequest()                                 // 任意请求
                    .authenticated();                             // 需要登录以后访问
        }
    }
    
    ```

##### 4.实验2：指定登录页面

```java
// 注意！：这个类一定要放在自动扫描的包下，否则所有的配置都不会生效

// 将当前类标记为配置类
@Configuration
// 启用web环境下权限控制功能
@EnableWebSecurity
public class WebAppSecurtyConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity security) throws Exception {
        security
                .authorizeRequests()                            // 对请求进行授权
                .antMatchers("/index.jsp")          // 针对路径进行授权
                .permitAll()                                   // 可以无条件访问
                .antMatchers("/layui/**")          // 针对/layui/**路径进行授权
                .permitAll()                                  // 可以无条件访问

                .and()
                .authorizeRequests()                          // 对请求进行授权
                .anyRequest()                                 // 任意请求
                .authenticated()                             // 需要登录以后访问

                .and()
                .formLogin()                                // 使用表单形式登录
                // 关于loginPage特殊说明
                // 指定登录页的同时，会影响到：“提交登录表单的地址”，“退出登录地址”，“登录失败地址”
                .loginPage("/index,jsp")                    // 指定登录页面，如果没有指定，会访问springsecurity自带的登录页
                // loginProcessingUrl方法指定了登录的地址，就会覆盖loginpage方法中设置的默认值
                .loginProcessingUrl("/do/login.html")
                ;


    }
```

##### 5.实验3：设置登录系统的账号、密码

- 思路

  ![1602469635431](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602469635431.png)

  ```java
                  // 关于loginPage特殊说明
                  // 指定登录页的同时，会影响到：“提交登录表单的地址”，“退出登录地址
  				.formLogin() //开启表单登录功能
                  .loginPage("/index,jsp")                    // 指定登录页面，如果没有指定，会访问springsecurity自带的登录页
                  // loginProcessingUrl方法指定了登录的地址，就会覆盖loginpage方法中设置的默认值
                  .loginProcessingUrl("/do/login.html")       // 指定提交登录表单的地址
  //                .permitAll()                                // 登录地址本身也需要permitAll() 放行
                  .usernameParameter("loginAcct")             // 定制登录账号的请求参数名
                  .passwordParameter("userPwd")               // 定制登录密码的请求参数名
                  .defaultSuccessUrl("/main.html")            //设置登录成功后默认前往的url地址
                  ;
  
  ```

- `CSRF`:跨站请求伪造





















##### 6.实验4：退出登录`

- logout():开启注销功能

- logoutUrl():自定义注销功能的url地址

  - 如果没有禁用CSRF功能，那么退出请求必须使用post

    ```java
                    .and()
                    .csrf()
                    .disable()  	//禁用csrf功能
                    .logout()		//开启退出功能
                    .logoutUrl("/do/logout.html") //指定处理退出请求的url
//退出成功后前往的地址                .logoutSuccessUrl("/index.jsp")
    ```
    
    ```html
    <form id="logoutForm" action="${pageContext.request.contextPath }/do/logout.html" method="post">
    			<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}"/>
    			</form>
    			<a id="logoutAnchor" href="">退出</a>
    			<script type="text/javascript">
    				window.onload = function(){
    					// 给超链接的demo对象绑定单击响应函数
    					var anchor = document.getElementById("logoutAnchor");
    
    
    					anchor.onclick = function(){
    						// 提交包含csrf参数的表单
    						document.getElementById("logoutForm").submit();
    
    						// 取消超链接的默认行为
    						return false;
    					};
    				};
    			</script>
    ```

##### 7.实验5：基于角色或权限进行访问控制

```java
@Configuration
// 启用web环境下权限控制功能
@EnableWebSecurity
public class WebAppSecurtyConfig extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(AuthenticationManagerBuilder builder) throws Exception {
        builder
                .inMemoryAuthentication() // 在内存中完成账号密码的检查
                .withUser("tom")    // 指定账号
                .password("123123")             // 指定密码
                .roles("ADMIN","学徒")                 // 指定当前用户的角色
                .and()
                .withUser("jerry")    // 指定账号
                .password("123123")             // 指定密码
                .authorities("UPDATE","内门弟子")          // 指定当前全权限
        ;
    }
    
    
    @Override
    protected void configure(HttpSecurity security) throws Exception {
        security
                .authorizeRequests()                            // 对请求进行授权
                .antMatchers("/index.jsp")          // 针对路径进行授权
                .permitAll()                                   // 可以无条件访问
                .antMatchers("/layui/**")          // 针对/layui/**路径进行授权
                .permitAll()                                  // 可以无条件访问
                .antMatchers("/level1/**")          // 针对level1路径设置访问要求，要求用户具有学徒的角色才能访问
                .hasAnyRole("学徒")                       // 要求用户具有学徒的角色才能访问
                .antMatchers("/level2/**")          // 针对level2路径设置访问要求，要求用户具有学徒的角色才能访问
                .hasAuthority("内门弟子")                       // 要求用户具备内门弟子的权限才能访问


```

- 注意：`springsecurity`会在角色字符串前面加`ROLE_`前缀，之所以要强调这个事情，是因为将来从数据库查询得到的信息，需要我们自己手动组装。手动组装时需要我们自己给角色字符串加`ROLE_`的前缀

##### 8.实验6：自定义403页面

![1602483935721](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602483935721.png)

![1602484000056](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602484000056.png)

##### 9.实验7：记住我-内存板

##### 10.实验9：查数据库完成认证

- 配置数据源

  ```xml
   <!-- 配置数据源 -->
      <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
          <property name="username" value="root"></property>
          <property name="password" value="123456"></property>
          <property name="url" value="jdbc:mysql://localhost:3307/security?useSSL=false"></property>
          <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
      </bean>
      <!-- jdbcTemplate-->
      <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
          <property name="dataSource" ref="dataSource"></property>
      </bean>
  ```

- 开启令牌仓库功能

  ![1602547501088](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602547501088.png)

  

![1602547516632](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602547516632.png)

- 自定义数据库查询方式

  ```java
  @Component
  public class MyUserDetailsService implements UserDetailsService {
  
      @Autowired
      private JdbcTemplate jdbcTemplate;
  
      // 根据表单提交的账号，查询user对象，并装配角色权限等信息
      @Override
      public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
  
          // 1.从数据库查询admin对象
          String sql = "select id,loginAcct,userpwd,username,email from t_admin where loginAcct = ?";
  
          List<Admin> list = jdbcTemplate.query(sql, new BeanPropertyRowMapper<>(Admin.class), username);
  
          Admin admin = list.get(0);
  
          // 2.给admin设置角色权限信息
          List<GrantedAuthority> authority = new ArrayList<>();
  
          authority.add(new SimpleGrantedAuthority("ROLE_ADMIN"));
          authority.add(new SimpleGrantedAuthority("UPDATE"));
  
          // 3.把admin对象和authorities封装到UserDetails中
          String userPswd = admin.getUserPswd();
  
          return new User(username,userPswd,authority);
      }
  }
  ```

- 完成登录

  ```java
  builder.userDetailsService(userDetailService);
  ```

##### 11.密码加密

- 实现`SpringSecurity`提供的接口`passwordEncoder`

  ```java
  @Component
  public class MyPasswordEconder implements PasswordEncoder {
      @Override
      public String encode(CharSequence charSequence) {
  
          return privateEncod(charSequence);
      }
  
      @Override
      public boolean matches(CharSequence charSequence, String encodedPasswrod) {
  
          // 1.对明文密码进行加密
          String formPassword = privateEncod(charSequence);
  
          // 2.声明数据库密码
          String databasePassword = encodedPasswrod;
  
          // 3.比较
          return Objects.equals(formPassword,databasePassword);
      }
  
      private String privateEncod(CharSequence charSequence){
  
          try {
              // 1.创建MessageDigest对象
  
              String algorithm = "MD5";
              MessageDigest messageDigest = MessageDigest.getInstance(algorithm);
  
              // 2.获取rawPassword的字节数组
              byte[] input = ((String) charSequence).getBytes();
  
              // 3.加密
              byte[] output = messageDigest.digest(input);
  
              // 4.转换为16进制对应的字符
              String encoded = new BigInteger(1, output).toString(16);
  
              return encoded;
  
  
          } catch (NoSuchAlgorithmException e) {
              e.printStackTrace();
          }
  
          return null;
      }
  }
  
  ```

- 在`SpringSecurity`的配置类中装配`MyPasswordEconder`

  ```java
  
  
      @Autowired
      private MyPasswordEconder myPasswordEconder;
  ```

- 使用

  ```java
          builder
                  .userDetailsService(myUserDetailsService).passwordEncoder(myPasswordEconder);
  ```

  ![1602549632054](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602549632054.png)

##### 12.加盐值的加密

- 概念：借用生活中烹饪是加盐值不同，菜肴的味道不同，在加密时每次使用一个随机生成的盐值，让加密结果不固定

- 用法创建`BCryptPasswordEncoder`对象传给`passwordEncoder`方法

  ```java
   @Autowired
      private BCryptPasswordEncoder myPasswordEconder;
  
      @Bean
      public BCryptPasswordEncoder getMyPasswordEconder(){
          return new BCryptPasswordEncoder();
      }
  
  
  
   builder
                  .userDetailsService(myUserDetailsService).passwordEncoder(myPasswordEconder);
  
  
  ```

##### 13.项目中加入`SpringSecurity`

- 加入`SpringSecurity`环境

  - 依赖，在原有的`ssm`整合环境基础上，计入`SpringSecurity`的依赖

- 在`web.xml`中配置`delegatingFilterProxy`

- 创建基于注解的配置类

  ```java
  // 表示当前类是一个配置类
  @Configuration
  
  // 启用web环境下权限控制功能
  @EnableWebSecurity
  public class WebAppSecurityConfig extends WebSecurityConfigurerAdapter {
  }
  
  ```

- 结论：为了让`SpringSecurity`能够针对浏览器请求进行权限控制，需要让`SpringMVC`来扫描`WebAppSecurityConfig`类

  ![1602552248291](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602552248291.png)

- 提出找不到bean的问题

  ![1602553252163](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602553252163.png)

- 分析找不到bean的问题

  - 明确三大组件启动顺序
    - 首先`ContextLoaderListener`初始化，创建spring的`IOC`容器
    - 其次`DelegatingFilterProxy`初始化，查找IOC容器、查找bean
    - 最后：`DispatcherServlet`初始化，创建SpringMVC的`IOC`容器
  - Filter查找IOC容器的工作机制

![1602554079817](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602554079817.png)

- 解决方案一：把两个`IOC`容器合二为一：

  - 不使用`ContextLoaderListener`，让`DispatchServlet`去加载所有的spring配置文件
  - 遗憾：破坏现有程序的结构。原本是`ContextLoaderListener`和`DispatcherServlet`两个组件创建两个`IOC`容器，现在只有一个

- 解决方案二：改源码:需要创建同包同名的类

  - 修改`DelegatingFilterProxy`的源码，修改两处：

    - 初始化时直接跳过查找`IOC`容器的环节

      ```java
          @Override
          protected void initFilterBean() throws ServletException {
              synchronized (this.delegateMonitor) {
                  if (this.delegate == null) {
                      // If no target bean name specified, use filter name.
                      if (this.targetBeanName == null) {
                          this.targetBeanName = getFilterName();
                      }
                     
      //                if (wac != null) {
      //                    this.delegate = initDelegate(wac);
      //                }
                  }
              }
          }
      ```

      

    - 第一次请求时直接找`SpringMVC`的`IOC`容器

      ```java
      @Override
          public void doFilter(ServletRequest request, ServletResponse response, FilterChain filterChain)
                  throws ServletException, IOException {
      
              // Lazily initialize the delegate if necessary.
              Filter delegateToUse = this.delegate;
              if (delegateToUse == null) {
                  synchronized (this.delegateMonitor) {
                      delegateToUse = this.delegate;
                      if (delegateToUse == null) {
      
                          // 把原来的查找IOC容器的代码注释掉
                         // WebApplicationContext wac = findWebApplicationContext();
      
                          // 按我们自己的需要重新编写
                          // 1.获取ServletContext对象
                          ServletContext sc = this.getServletContext();
      
                          // 拼接springMVC将IOC容器存入ServletContext域时使用的属性名
                          String servletName = "springDispatcherServlet";
      
                          String attrName = FrameworkServlet.SERVLET_CONTEXT_PREFIX + servletName;
      
                          // 3.根据attrName从servletContext域中获取IOC容器对象
                          WebApplicationContext wac = (WebApplicationContext)sc.getAttribute(attrName);
      
                          if (wac == null) {
                              throw new IllegalStateException("No WebApplicationContext found: " +
                                      "no ContextLoaderListener or DispatcherServlet registered?");
                          }
                          delegateToUse = initDelegate(wac);
                      }
                      this.delegate = delegateToUse;
                  }
              }
      
              // Let the delegate perform the actual doFilter operation.
              invokeDelegate(delegateToUse, request, response, filterChain);
          }
      
      ```

      ![1602556430556](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602556430556.png)

- 放心登录页和静态资源

  - 目标

    - 在`SpringSecurity`的配置类`webAppSecurityConfig`中重写configure（`HTTPSecurity`  security）方法并设置

      ```java
      // 表示当前类是一个配置类
      @Configuration
      
      // 启用web环境下权限控制功能
      @EnableWebSecurity
      public class WebAppSecurityConfig extends WebSecurityConfigurerAdapter {
      
      
      
          @Override
          protected void configure(HttpSecurity security) throws Exception {
      
              security
                      // 对请求进行授权
                      .authorizeRequests()
                      // 针对登录页进行授权
                      .antMatchers("/admin/to/login/page.html")
                      // 可以无条件访问
                      .permitAll()
                      .antMatchers("/crowd/**")
                      .permitAll()
                      .antMatchers("/css/**")
                      .permitAll()
                      .antMatchers("/fonts/**")
                      .permitAll()
                      .antMatchers("/img/**")
                      .permitAll()
                      .antMatchers("/jquery/**")
                      .permitAll()
                      .antMatchers("/layer/**")
                      .permitAll()
                      .antMatchers("/script/**")
                      .permitAll()
                      .antMatchers("/ztree/**")
                      .permitAll()
                      .antMatchers("/bootstrap/**")
                      .permitAll()
                      // 对任意请求进行授权
                      .anyRequest()
                      // 需要登录后访问
                      .authenticated();
          }
      }
      
      ```

  - 目标2：提交登录表单做内存认证

    - 思路

    ![1602570101263](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602570101263.png)

    - 操作1：设置表单

    ![1602570406569](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602570406569.png)

    - 操作2：配置`SpringSecurity`

    ```java
    @Override
        protected void configure(HttpSecurity security) throws Exception {
    
            security
                    // 对请求进行授权
                    .authorizeRequests()
                    // 针对登录页进行授权
                    .antMatchers("/admin/to/login/page.html")
                    // 可以无条件访问
                    .permitAll()
                    .antMatchers("/crowd/**")
                    .permitAll()
                    .antMatchers("/css/**")
                    .permitAll()
                    .antMatchers("/fonts/**")
                    .permitAll()
                    .antMatchers("/img/**")
                    .permitAll()
                    .antMatchers("/jquery/**")
                    .permitAll()
                    .antMatchers("/layer/**")
                    .permitAll()
                    .antMatchers("/script/**")
                    .permitAll()
                    .antMatchers("/ztree/**")
                    .permitAll()
                    .antMatchers("/bootstrap/**")
                    .permitAll()
                    .anyRequest()
                    // 需要登录后访问
                    .authenticated()
    
                    // 设置
                    .and()
                    // 防跨站请求伪造功能
                    .csrf()
                    // 禁用
                    .disable()
    
                    // 开启表单登录功能
                    .formLogin()
                    // 指定登录页面
                    .loginPage("/admin/to/login/page.html")
                    // 指定处理登录请求的地址
                    .loginProcessingUrl("/security/do/login.html")
                    // 指定登陆成功后前往的地址
                    .defaultSuccessUrl("/admin/to/main/page.html")
                    .permitAll()
                    // 账号的请求参数名称
                    .usernameParameter("loginAcct")
                    // 密码的请求参数名称
                    .passwordParameter("userPswd")
            ;
        }
    
    
     @Override
        protected void configure(AuthenticationManagerBuilder builder) throws Exception {
            builder
                    .inMemoryAuthentication()
                    .withUser("tom")
                    .password("123123")
                    .roles("ADMIN")
                    ;
        }
    
    ```

    - 操作3：取消spring-web-mvc.xml中以前的自定义登录拦截器

      ![1602572211934](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602572211934.png)

  - 目标3：退出登录

    ```java
     // 开启退出登录功能
                    .logout()
                    // 指定退出登录地址
                    .logoutUrl("/seucrity/do/logout.html")
                    // 指定退出成功只够的地址
                    .logoutSuccessUrl("/admin/to/login/page.html")
    ```

  - 目标4：把内存登录改为数据库登录

    ![1602573015385](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602573015385.png)

    - 操作1：根据`adminId`查询已分配的角色，这个操作以前已经写好了

    - 操作2：根据adminId查询已分配的权限

      - SQL语句

        ```mysql
        select t_auth.`name` 
        from t_auth 
        left join inner_role_auth 
        on t_auth.id=inner_role_auth.auth_id
        left join inner_admin_role on inner_admin_role.role_id = inner_role_auth.role_id
        where inner_admin_role.admin_id=2 and t_auth.name != "" and t_auth.name is not null
        ```

    - 操作3：创建securityAdmin类

      ```java
      **
       * 考虑到user对象中仅仅包含账号和密码，为了能够获取到原始的admin对象，专门创建这个类，对user类进行扩展
       */
      public class SecurityAdmin extends User {
      
          private static final long serialVersionUID = 1L;
      
          // 原始的admin对象，包含admin对象的全部属性
          private Admin originalAdmin;
      
          public SecurityAdmin(Admin originalAdmin, List<GrantedAuthority> authorities) {
      
              super(originalAdmin.getLoginAcct(),originalAdmin.getUserPswd(),authorities);
      
              this.originalAdmin = originalAdmin;
      
          }
      
          public Admin getOriginalAdmin(){
              return originalAdmin;
          }
      
      
      }
      ```

    - 操作4：根据账号查询admin

    - 操作5：创建UserDetailsService的实现类

      ```java
      @Component
      public class CrowdUserDetailService implements UserDetailsService {
      
          @Autowired
          private AdminService adminService;
      
          @Autowired
          private RoleService roleService;
      
          @Autowired
          private AuthService authService;
      
      
          @Override
          public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
      
              // 1.根据账号名称查询admin对象
             Admin admin =  adminService.getAdminByLoginAcct(username);
      
             // 2.获取adminId
              Integer adminId = admin.getId();
      
              // 3.根据adminId查询角色信息
              List<Role> assignedRoleList = roleService.getAssignedRole(adminId);
      
              // 4.根据adminId查询权限信息
              List<String> authNameList = authService.getAssignedAuthNameByAdminId(adminId);
      
              // 5.根据集合对象用来存储GrantedAuthority
              List<GrantedAuthority> authorities = new ArrayList<>();
      
              // 6.遍历assignedRoleList存入角色信息
              for (Role role : assignedRoleList) {
                  // 注意不要忘了加前缀
                  String roleName = "ROLE_" + role.getName();
      
                  SimpleGrantedAuthority simpleGrantedAuthority = new SimpleGrantedAuthority(roleName);
      
                  authorities.add(simpleGrantedAuthority);
              }
      
              // 7.遍历AuthNameList存入权限信息
              for (String authName : authNameList) {
                  SimpleGrantedAuthority simpleGrantedAuthority = new SimpleGrantedAuthority(authName);
      
                  authorities.add(simpleGrantedAuthority);
              }
      
              // 8.封装securityAdmin对象
              SecurityAdmin securityAdmin = new SecurityAdmin(admin, authorities);
      
              return securityAdmin;
          }
      }
      
      ```

    - 操作6：在配置类中使用

      ```java
          @Autowired
          private UserDetailsService userDetailsService;
      
          @Override
          protected void configure(AuthenticationManagerBuilder builder) throws Exception {
      //        builder
      //                .inMemoryAuthentication()
      //                .withUser("tom")
      //                .password("123123")
      //                .roles("ADMIN")
      //                ;
              // 正式功能中使用基于数据库的认证
      
              builder.userDetailsService(userDetailsService);
          }
      ```

  

  

  

##### 14、密码加密

![1602636611545](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602636611545.png)

- 准备`BCryptPasswordEncoder`对象

  - 注意：如果在SpringSecurity的配置类中用@bean注解将BCryptPasswordEncoder对象存入IOC容器，nameservice获取不到

  ![1602637454520](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602637454520.png)

  ![1602637846807](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602637846807.png)

- 使用`BCryptPasswordEncoder`的对象

  ```java
               ;
          // 正式功能中使用基于数据库的认证
  
          builder
                  .userDetailsService(userDetailsService)
                  .passwordEncoder(getPasswordEncoder());
      }
  
  ```

- 使用`BCryptPasswordEncoder`的对象保存时加密

![1602637146184](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602637146184.png)

##### 15、显示用户昵称

- 在页面上导入SpringSecurity的标签库

![1602638063316](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602638063316.png)

- 通过标签库获取已登录用户信息

##### 16、擦除密码

- 擦除密码是在不影响登录认证的情况下，避免密码泄露，增加系统的安全性

![1602640646021](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602640646021.png)

##### 17、权限控制

- 设置测试数据
  - 用户：adminOperator
    - 角色：经理
      - 权限：无
    - 角色：经理操作者
      - 权限：`user:save`
  - 用户：`roleOperator`
    - 角色：部长
      - 权限：无
    - 角色：部长操作者
      - 权限：`role`:`delete`

- 测试1：访问admin分页功能时具备经理角色

  ```java
   // 分页显示admin数据设定访问控制，要求具备经理的角色
                  .antMatchers("/admin/get/page.html")
                  .hasRole("经理")
  
  ```

- 测试2：

  ```java
  
      //@ResponseBody
      @PreAuthorize("hasRole('部长')")
      @RequestMapping("/role/get/page/info.json")
      public ResultEntity<PageInfo<Role>> getPageInfo(
              @RequestParam(value="pageNum",defaultValue = "1") Integer pageNum,
              @RequestParam(value="pageSize",defaultValue = "5") Integer pageSize,
              @RequestParam(value = "keyword",defaultValue = "") String keyword
  
  ```

  

  ![1602643231346](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602643231346.png)

- 测试3：

  - 访问`admin`保存功能时具有`user：save`权限

    ```java
     @PreAuthorize("hasAuthority('user:save')")
        @RequestMapping("/admin/save.html")
        public String save(Admin admin) {
    
            adminService.saveAdmin(admin);
    
            return "redirect:/admin/get/page.html";
        }
    ```

    ![1602655372425](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1602655372425.png)

18、页面元素的权限控制

- 要求
  - 页面上的局部元素根据我们的访问控制规则进行控制
- 标签库