# 1.中文问题

- 在进入docker容器中后无法输入中文问题

  ```mysql
  在进入容器是指定字符集
  docker exec -it mysql03  env LANG=C.UTF-8 bash
  ```

  

1.连接层

2.服务层

3.引擎层

- 常用的两种引擎

​		![1604455180648](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604455180648.png)

存储层

# 二、索引的优化分析

### 1.性能下降，执行时间长，等待时间长

- 查询语句写的烂
- 索引失效
  - 单值
  - 复合
- 关联查询太多join(设计缺陷或不得已的需求)
- 服务器调优各个参数设置(缓冲、线程数等)

### 2.常见的join查询

#### ①.`SQL`执行顺序

- 手写
  - `select-from-join on-where-group by-having-orderby-limit`
- `mysql`执行顺序
  - `from-join on-where-group by-having-select-order by-limit`

![1604456614468](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604456614468.png)

#### ②.`join`图

- 内连接inner join 只有两表之间公有的部分，没有的不显示

- 左连接left join 以前边表为主都显示，后边表没有的显示为null

- 有链接right join 以后便表为主都显示，前表没有的显示为null

- 左外连接把公有的去掉，只显示前表独有的内容,后表显示为null

  ```mysql
  select * from tbl_emp a left join tbl_dept b on a.deptId = b.id where b.id is null;
  
  ```

- 右外连接把共有的去掉，只显示后表独有的内容,前表显示为null

- `mysql`不支持全有full语法,可以使用左连接(union)外连接

  - union:去重

  ```mysql
  
   -> select * from tbl_emp a left join tbl_dept b on a.deptId = b.id
   -> union
   -> select * from tbl_emp a right join tbl_dept b on a.deptId = b.id;
  
  ```

- 获取两表独有部分:左外(union)右外

  ```mysql 
   select * from tbl_emp a left join tbl_dept b on a.deptId = b.id where b.id is null
      -> union
      -> select * from tbl_emp a right join tbl_dept b on a.deptId = b.id where a.deptId is null;
  
  ```

  

#### ③.建表join

#### ④.7种join

![1604456796453](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604456796453.png)

![1604456808527](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604456808527.png)





# 三、索引

### 1.是什么？

- 索引是一种数据结构b+树，目的是提高查找效率。
- 排好序的快速查找数据结构
  - 在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用数据，这样就可以在这些数据结构上实现高级查找算法。这种数据结构，就是索引
- 索引本身也很大，以索引文件的形式存在磁盘上

### 2.优势

- 提高数据检索效率，降低数据库的IO成本
- 对数据列进行了排序，降低数据排序的成本，降低了cpu的消耗

### 3.劣势

- 实际上索引也是一张表，该表保存了主键与索引字段，并执行实体表的记录，如果对表进行更新操作，更新表的时候，索引也要更新，索引更新效率慢

### 4.索引的分类

- 单值索引

  - 一个索引只包含单个列，一个表可以有多个单列索引

- 唯一索引

  - 索引列的值必须唯一，但允许空值

- 复合索引

  - 一个索引包含多个列

- 基本语法

  - 创建

    ```mysql
    create [unique] index 索引名字 on 表名(字段);
    alter 表名 add [unique] index [索引名字] on (字段)
    ```

  - 删除

    ```mysql
    drop index [索引名字] on 表名;
    ```

  - 查看

    ```mysql
    show index from table_name\G
    ```

    

![1604648265441](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604648265441.png)

### 5.索引结构

- b数索引
- hash索引
- full-text索引
- r-tree索引

### 6.索引的建立

- 主键自动建立唯一索引
- 频繁作为查询条件的字段应该创建索引
- 查询中与其他表关联的字段，外键关系见建立索引
- 频繁更新的索引不建议创建索引
- where条件里用不到的索引不创建索引
- 单键/组合索引的选择：在该并发的情况下建立组合索引
- 查询中排序的字段，排序字段若通过索引去访问将大大提高查询速度
- 查询中统计或者分组(group by)字段。分组字段之前也要排序

### 7.性能分析

##### 1.`MySQL Query Optimizer`：mysql查询优化分析器

- 通过计算分析系统中收集到的统计信息，为客户端请求的query提供它认为最有的执行计划

##### 2.MySQL的常见瓶颈

- CPU：CPU在饱和的时候一般发生在数据装入内存或从磁盘中读取数据的时候
- IO：磁盘I/O瓶颈发生在装入数据大于内存容量的时候
- 服务器硬件的性能瓶颈

##### 3.`Explain`

- 使用EXPLAIN关键字可以模拟优化器执行SQL语句。从而指导MySQL是如何处理你的SQL语句的。分析你的查询语句或是表结构的性能瓶颈

###### 1.能干嘛？

- 表的读取顺序
- 数据读取操作的操作类型
- 哪些索引可以使用
- 哪些索引被实际使用
- 表之间的引用
- 每张表有多少行被优化器查询

###### 2.怎么玩？

```mysql 
explain + sql语句
```

- 执行计划包含信息

![1604653586931](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604653586931.png)

###### 3.字段解释

- `id:`select查询的序列号，也包含一组数字，表示查询中执行select字句或操作表的顺序。反应表的加载顺序

  - 三种情况

    - id相同，执行顺序由上至下

    - id不同，如果是子查询，id的序号会递增，id值越大优先级越高。

    - id相同不同，同时存在

      ![1604654671491](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604654671491.png)

- `select_type`：数据读取操作的操作类型
  
  - 有哪些？
    - simple    
      - 简单的select查询，查询中不包含子查询或者union查询
    - primary     
      - 查询中若包含任何复杂的子部分，最外层查询则被标记为primary     
    - subquery
      - 在select或where列表中包含了子查询
    - derived
      - 在from列表中包含的子查询标记为derived（衍生），MySQL会递归执行这些子查询，把结果放在临时表中
    - union
      - 若第二个select出现在union之后，则被标记为union，若union包含在from自居的子查询中，外层select将被标记为derived
    - union  result
      - 从union表获取结果的select
- 查询的类型，主要是用于区别普通查询、联合查询、子查询等复杂查询
  
- table:显示这一行数据是关于哪张表的

- type:访问类型排序

  - 显示查询使用了何种类型，从最好到最差依次是

    ```
    system > const > eq_ref > ref > range > index > all
    ```

  - system

    - 表只有一行记录没这事const类型的特例，平时不会出现

  - const（常量）

    - 表示通过索引一次就找到了，

  - eq_ref

    - 唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见主键或唯一索引扫描

  - ref

    - 非唯一索引扫描，返回撇皮某个单独的所有行。本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而它可能会找到多个符合条件的行，所以它应该属于查找和扫描的混合体

  - range

    - 只检索给定范围的行，使用一个索引来选择行。

  - index

    - 全索引扫描

  - all

    - 扫描全表

- possible_keys
  
- 显示可能应用在这张表上的索引，一个或多个。查询涉及到的字段上若存在索引，则该索引将被列出，但不一定被查询实际使用
  
- key
  
  - 实际使用的索引。如果为null，则没有使用索引。若查询中使用了覆盖索引，则该索引仅出现在key列表中
- key_len
  - 表示索引中使用的字节数，可通过该列计算查询中使用的索引的长度。
  - 在不损失精度的情况下越短越好

- ref
  
- 显示索引的哪一列被使用了，如果可能的话，最好是一个常数。哪些列或常量
  
- rows
  
- 根据表统计信息及索引选用情况，大致估算出找到所需的记录要读取的行数
  
- extra

  - 包含不适合在其他列展示，但十分重要的额外信息

    - ==Using  filesort==:文件内排序

      - 说明MySQL会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行排序。MySQL中无法利用索引完成 的排序称为文件内排序

    - ==Using temporary==

      - 使用了临时表保存中间结果，MySQL在对查询结果排序时使用了临时表。常见于排序order by 和分组查询group by

    - ==USING index==

      - 表示相应的select操作中使用了覆盖索引，避免访问了表的数据行，效率不错。如果同时出现using where，表明索引被用来执行索引键值的查找；如果没有谱出现using where ,表面索引用来读取数据而非执行查找动作

    - using where 

      - 使用了where过滤

    - using join buffer

      - 使用了连接缓存

    - impossible where 

      - where字句的值总是false，不能用来获取元组

      ![1604662060161](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604662060161.png)

## 四、索引优化

##### 1.索引分析

- 单表

  ![1604710559112](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604710559112.png)

  ```mysql
  explain select * from article where category_id = 1 and comments > 1 order by views desc limit 1;
  
  ```

  ![1604710598179](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604710598179.png)

  - 第一次创建索引，where后面的字段需要创建

    ```mysql
    create index idx_article_ccv on article(category_id,comments,views);
    
    ```

    - 创建索引之后的查询结果：

      - type变为了range。但是还是有内排序。按照b树的工作原理，先排序category_id，如果遇到相同的category_id，再排序comments，如果遇到相同的comments，再排序views。当comments字段在联合索引里处于中间位置时，因为comments>1天剑是一个范围值，mysql无法利用索引再对后面的views部分进行检索，即range类型查询后面的索引是无效的

      ![1604710717644](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604710717644.png)

  - 第二次创建索引

    ```mysql
    create index idx_article_cv on article(category_id,views);
    ```

    - 创建之后的查询结果

      ![1604711579812](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604711579812.png)

- 两表

  - 左连接，索引加在左表情况

    ![1604712803379](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604712803379.png)

  - 左连接索引加在右表的情况

    ![1604712849360](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604712849360.png)

  - 结论
    
    - 这是由于左连接特性决定的。左连接条件用于确定如何从右表搜索行，左边一定有，所以右边是我们的关键，一定需要建立索引
  - 右连接和左连接一样

- 三表

  - 不建立索引查询

    ![1604714837020](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604714837020.png)

  - 建立索引

    ```mysql
    create index y on book(card);
    create index z on phone(card);
    ```

    ![1604714882804](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604714882804.png)

- join语句的优化
  - 尽可能减少join语句中的循环次数，永远用小表驱动大表
  - 优先优化内层循环
  - 保证join语句中被驱动表上join条件字段已经被索引

##### 2.索引失效(避免)

- 全职匹配我最爱

- 最佳左前缀法则

  - 如果索引了多列，要遵守最左前缀法则。==指的是查询从索引的最左前列开始并且不跳过索引中的列,中间兄弟不能断==

    ```mysql
    create index idx_staffs_nameAgePos on staffs(NAME,age,pos);
    
    ```

    

    ![1604797005421](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604797005421.png)

- 不在索引列上做任何操作(计算、函数（自动or手动）类型转换)，会导致索引失效而转向全表扫描

- 存储引擎不能使用索引中范围条件右边的列

  ![1604797669596](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604797669596.png)

- 尽量使用覆盖索引(只访问索引的查询(索引列和查询列一致))，减少select *

  ![1604797840609](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604797840609.png)

- mysql在使用不等于(!=或<>)的时候无法使用索引会导致全表扫描

  ![1604798116576](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604798116576.png)

- is null,is not null 也无法使用索引

- like以通配符开头(‘%acb’)mysql索引失效会变成全表扫描的操作

  ![1604798345980](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604798345980.png)

  - 问题：解决like‘%字符串%’时索引不被使用的方法？
    - 使用覆盖索引
      - 查询的字段包含在索引字段中

- 字符串不加单引号索引失效

- 少用or，用它来连接时会索引失效



![1604802847831](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604802847831.png)

![1604803359622](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604803359622.png)

## 五、查询截取分析

##### 1.查询优化

- 小表驱动大表

  ![1604804175606](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604804175606.png)
  - exists

    ![1604804257468](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604804257468.png)



- order by关键字的优化
  - order by字句尽量使用index方式排序，避免使用filesort方式排序
  - 尽可能在索引列上完成排序操作，遵照索引建立的最佳左前缀
  - mysql支持两种排序方式，filesort，index效率高，指mysql扫描本身完成排序
  - order by 满足两种情况，会使用index排序
    - order by 语句使用索引最左前列
    - 使用where字句与order by子句条件列组合满足索引最左前列
  - 如果不在索引列上，filesort有两种算法
    - 双路排序
    - 单路排序

![1604816691963](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604816691963.png)

![1604817132901](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604817132901.png)



- group by关键字优化
  - group by 实质是先排序后进行分组，遵照索引建的最佳左前缀，当无法使用索引列，增大max_length_for_sort_data参数的设置+增大sort_buffer)size参数的设置；where高于having，能写在where限定的条件就不要去having限定



##### 2.慢查询日志

- mysql的慢查询日志是mysql提供的一种日志记录，他用来记录mysql中响应事件超过阈值的语句，具体指运行时间超过long_query_time值得SQL，则会被记录到慢查询日志中。

  - 具体运行时间超过long_query_time值得SQL，则会被记录到慢查询日志。long_query_time的默认值是10秒，意思是运行10秒以上的语句
  - 由它来查询哪些SQL超出了我们的最大忍耐时间值，比如一条SQL执行超过5秒中，我们就算慢SQL，希望能收集超过5秒的SQL，结合explain进行全面分析

- 默认情况下，mysql数据库是没有开启的，需要我们手动开启。

- 查看是否开启及如何开启

  ```mysql
   #查看是否开启
   show variables like '%slow_query_log';
  #开启
  set global slow_query_log=1;#只对本次有效，重启mysql之后如果需要使用，还需要重新开启
  ```

  

##### 3.批量数据脚本

- 需要先设置参数

  ![1604818931563](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604818931563.png)

  ```mysql 
  #开启过慢查询日志后，需要开启bin-log，
  #查询是否开启
  show variables like 'log_bin_trust_function_creators';
  
  ```

- 创建函数，保证每条数据都不同

- 随机产生字符串函数

  ![1604820204635](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604820204635.png)

- 随机产生部门编号

  ![1604820255045](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604820255045.png)



- 创建存储过程emp

  ![1604820295545](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604820295545.png)

- dept

  ![1604820315458](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604820315458.png)





##### 4.show profile

- 是mysql提供的可以用来分析当前会话中语句执行的资源消耗情况。可以用于SQL调优的测量

- 默认情况是关闭状态，并保存最近15次的运行结果

- 步骤

  - 是否支持。看当前的mysql版本是否支持

    ```mysql
    show variables like 'profiling';
    ```

  - 开启功能，默认是关闭状态，使用前需要开启

    ```mysql
    set profiling= on;
    ```

  - 运行SQL

  - 查看结果

    ```mysql
    show profiles;
    ```

  - 诊断SQL

    ```mysql
    show profile cpu,block io for query query_ID;
    ```

    ![1604829254568](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604829254568.png)

    ![1604829071370](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604829071370.png)

  - 日常开发需要注意的结论

    ![1604829110046](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604829110046.png)

##### 5.全局查询日志

- 只能在测试环境用

## 六、`mysql`锁的机制

##### 1.锁的分类

- 从数据操作的粒度分
  - 表锁
  - 行锁
- 从对数据操作的类型(读/写)分
  - 读锁(共享锁)
    - 针对同一份数据，多个读操作可以同时进行而不会互相影响
  - 写锁(排他锁)
    - 当前写操作没有完成之前，他会阻断其他写锁和读锁

- 三锁

  - 表锁(偏读)

    - 偏向于myisam存储引擎，开销小，家锁块；无死锁；锁定粒度大

    - 查看表是否被锁

      ```mysql
      show open tables;
      ```

      

    - 手动增加表锁

      ```mysql
      lock table 表名字 read(write),表名字2 read(write)，其他；
      ```

    - 释放锁

      ```mysql
      unlock tables;
      ```

    ==加读锁==

    - 当前线程加了表锁之后，对于别的没加锁表不能有任何操作；
    - 当前线程可以查询该表记录，其他线程也可以读取该表记录
    - 其他线程可以查询或更新其他未加锁的表
    - 当加了表锁后，其他线程对该表的更新操作会进入阻塞状态，直到该表释放锁后执行

    ==加写锁==

    - 当前线程可以读当前表，不能读其他表，其他线程对该表的操作进入阻塞

    - 读锁会阻塞写，写锁会把读和写都阻塞

    ![1604832331628](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604832331628.png)

    ![1604832504936](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604832504936.png)

    

  - 行锁(偏写)

    - innodb存储引擎
  
    - 事物隔离级别
  
      - 脏读：一个书屋再对数据操作后，还没提交，这时另外一个事务进来获取获取进行操作
      - 不可重复读：一个事务对数据进行两次操作，当执行完第一次之后，另一个事务对当前数据进行了修改，这是第二次获取的数据和第一次不一样
      - 幻读：和不可重复读类似。一个书屋旧数据进项两次操作，当操作第一次完成之后，另一个事务对当前数据进行了添加，就导致第二次擦汗寻的时候就多了一条数据
  
      ![1604879297515](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604879297515.png)
  
    - 当前线程操作某一行未提交时，当前线程可查看操作后的信息，其他线程需要提交以后可查看操作后的信息。更新时，其他线程也更新这条信息，就进入阻塞，但是可以操作其他行
  
    - 索引失效行锁变表锁
  
      ![1604882105637](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604882105637.png)
  
    - 面试题
  
      - 如何锁定一行
  
      - 再SQL语句后面加for   update 
      
        ```mysql
      select * from 表名 where 语句  for update;
        ```
      
        **![1604883281553](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604883281553.png)**
  
    ![1604883303317](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604883303317.png)
  
  - 页锁





## 七.主从复制

##### 1.复制的基本原理

- slave会从master读取binlog来进行数据同步
  - master将改变记录到二进制日志(binary log)。这些记录过程叫做二进制日志事件
  - slave将master的binary log events拷贝到它的中继日志(relay log)
  - slave重做中继日志的事件，将改变应用到自己的数据库中。mysql赋值是异步的且串行化的

- 复制的基本规则
  - 每个slave只有一个master
  - 每个slave只能有一个唯一的服务器ID
  - 每个master可以有多个salve

- 一主一从常见配置

  - mysql版本一致且后台以服务运行

  - 主从配置在mysqldb节点下，都是小写

  - 修改主机my.db文件

    ![1604885184803](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604885184803.png)

    ![1604885474484](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604885474484.png)

  - 修改从机my.cnf配置文件

![1604885548423](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604885548423.png)

![1604885784888](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604885784888.png)

- 在主机上建立账户并授权slave

  ![1604885917648](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604885917648.png)

  ![1604886042923](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604886042923.png)

- 在从机上配置需要复制的主机

![1604886166680](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604886166680.png)

![1604886383459](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604886383459.png)

![1604886520190](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604886520190.png)

