CAP：只能选择两个，分布式容忍是必须实现的

​		只等在强一致和高可用之间选择

c:强一致

A：高可用

P：分布式容忍

`Redis`是C(强一致)P(分布式容忍)

BASE：是为了解决关系数据库强一致性引起的可用性降低的解决方案

​	BA：基本可用

​	S：软状态

​	E：最终一致

​	

# `Redis`

- 是什么？是一个高性能的key/value分布式内存数据库，基于内存运行
  - 远程字典服务器  
- 特点：
  - `redis`支持数据的持久化，可以将内存的数据保存到磁盘中，重启的时候可以再次加载使用
  - `redis`支持备份
  - `redis`支持`key/value`，`list`，`set`,`zset`,`hash`等

## 一、在docker容器中操作`redis`

##### 1.查询容器名字

```java
docker ps
```

##### 2.进入容器打开`redis`测试连通性

```java
//-i保持标准输入打开
// -t分配一个伪终端
docker exec -it myredis /bin/sh
redis-cli(redis命令行工具) -p 6379
```

##### 3.使用select + 0-15切换库

![1604479775603](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604479775603.png)

##### 4.`Dbsize`可以查看当前数据库的key的数量

![1604480046866](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604480046866.png)

##### 5.`FLUSHDB`:清除当前库的数据

##### 6.`FLUSHALL`:清除所有库的数据

##### 7.`redis`索引从0开始

##### 8.默认端口是6379

## 二、`redis`五大数据类型

##### 1.字符串`String`

- 是key-value形式，单值单value
- String是二进制安全的，可以包含任何数据
- 一个`redis`中字符串value最多可以是`512M`

- `del`/append/set/get/`strlen`方法
- `incr(加1)/decr(减1)/incrby(加指定的数)/decrby(减指定的数)`,一定要是数字才能加减
- `getrange`(指定获取区间范围的值)/`setreange`(设定指定区间的值，指定开始索引)
- `setex`(设置key的存活时间秒【setex key 时间 value】,如果key存在，value会替换key原有的值)/`setnx(如果不存在设置起效)`
- `mset(批量存储)/mget/msetnx(批量存储，都不存在才会成功)`

##### 2.列表`List`

- 单值多value

- 它底层是一个字符串链表，left、right都可以插入添加，如果键不存在，创建新的链表，如果键已存在，新增内容，如果值完全移除，对应的键也就消失了；链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很慢
- `lpush`(从左向右存放)/`rpush(怎么进怎么出)`：压栈/`lrange(取数据指定区间)`
- `lpop/rpop`：出栈
- `lindex(根据索引获取值)`
- `llen(长度)`
- `lrem` key:删除n个value
- `ltrim key`:开始index结束index，截取指定范围的值再复制给key
- `rpoplpush`:
- ![1604484700346](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604484700346.png)

- `lset key index value`：把当前key的index的值改为value，如果index超过当前列表长度就报错
- linsert key before /after  值1  值2：在值1之前/之后插入值2

##### 3.集合`Set`

- `sadd  key  value`:添加，会把重复的去掉
- `smembers   key`:查看key中的值
- `sismember key value`:判断key中是否存在value
- `scard key`:查看key的长度
- `srem key value`:从key中删除value
- `srandmember  key  count`:从key的值中随机获取count个元素
- `spop key`：随机出栈
- `smove key1 key2 value`:将key1的value移入到key2
- `sdiff  key1  key2  ……:`差集。在第一个key里面而不在后面任何一个set里面
- `sinter key1 key2`:交集
- `sunion key1 key2`:并集

##### 4.哈希（类似Java中的map）`Hash`

- key(String):value(键值对)

- 是一个键值对，适合用于存储对象
- `hset key  vkey  value:`添加
- `hget  key   vkey`:获取key中vkey的value
- `hmset key  vkey1  value 1  vkey2  value2……`：批量添加
- `hgetall  key`:获取key对应的value键值对
- `hdel key  vkey`:删除key中的vkey
- `hlen key`:获取key的长度
- `hexists key vkey`:判断key中是否有vkey
- `hkeys   key`（获取所有的vkey）/`hvals   key`(获取所有的vkey的值)
- `hincrby key vkey count`：让vkey的值加count
- `hincrbyfloat  key vkey count`:可以加小数
- `hsetnx  key  vkey  value`:如果不存在可以修改

##### 5.有序集合`Zset`

- 在set的基础上，加一个score值，之前set是k1 v1 v2 v3,现在zset是k1 score1 v1 score2 v2

- `zset`和set一样是string类型元素的集合，且不允许重复
- `zadd key score1 v1 score2 v2……`：添加
- `zrange key  0 -1`:查询所有的value
- `zrange key 0  -1  withscore`:查询所有的score    和 value
- `zrangebyscore   key   minscore   maxscore`:查询key中大于等于min小于等于max的value
- `zrangebyscore   key   (min   (max`:查询key中大于min小于max的value
- `zrem key 某score下对应的value值`：作用是删除元素
- `zcard key`:查询key的长度
- `zcount key score1 score2`:在score1和score2之间的个数
- `zrank key value`：获取value所在的索引
- `zscore key  value`:获取value对应的score的值

##### 6.`key`

- key * 查看改库中所有的key
- exists key的名字，判断某个key是否存在

- move key db   将key移动到db库中

- expire key秒钟：为给定的key设置过期时间

- `ttl key`:查询还有多少秒过期，-1代表永不过期，-2代表已过期

- type key:查看当前key的属性

## 三、`redis`配置文件解析

使用docker安装的redis没有配置文件，需要去GitHub上自行下载

:set nu:显示行数

##### 1.`includes`

##### 2.`newwork`

- `tcp-backlog 511`:设置`tcp`的backlog，backlog是一个连接队列，backlog队列总和=未完成三次握手队列+已完成三次握手的队列，在高并发的情况下需要一个backlog值来避免慢客户端连接问题
- `tcp-keepalive 300`：单位为秒，如果设置为0，则不会进行`keepalive`检测，

##### 3.`general`

##### 4.`security`安全

- 设置密码：`config set requirepass “123”`
- 输入密码：`Auth “123”`
- 获取密码：`config get requirepass`



##### 5.`limit`限制

- `Maxmemory-policy`：最大内存的缓存清除策略
  - `colatile-lru`:使用lru算法移除key，只对设置了过期时间的键
  - `allkeys-lru`:使用lru算法移除key
  - `colatile-random`:在过期集合中随机的key，只对设置了过期时间的键
  - `allkeys-random`移除随机的key
  - `colatile-ttl`:移除那些ttl值最小的key，即那些最近要过期的key
  - `noeciction`:不进行移除。针对写操作，只返回错误信息

##### 6.参数说明



- | 序号 | 配置项                                                       | 说明                                                         |
  | ---- | ------------------------------------------------------------ | :----------------------------------------------------------- |
  | 1    | `daemonize no`                                               | Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ） |
  | 2    | `pidfile /var/run/redis.pid`                                 | 当 Redis 以守护进程方式运行时，Redis 默认会把 pid 写入 /var/run/redis.pid 文件，可以通过 pidfile 指定 |
  | 3    | `port 6379`                                                  | 指定 Redis 监听端口，默认端口为 6379，作者在自己的一篇博文中解释了为什么选用 6379 作为默认端口，因为 6379 在手机按键上 MERZ 对应的号码，而 MERZ 取自意大利歌女 Alessia Merz 的名字 |
  | 4    | `bind 127.0.0.1`                                             | 绑定的主机地址                                               |
  | 5    | `timeout 300`                                                | 当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能  |
  | 6    | `loglevel notice`                                            | 指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice |
  | 7    | `logfile stdout`                                             | 日志记录方式，默认为标准输出，如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null |
  | 8    | `databases 16`                                               | 设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id |
  | 9    | `save <seconds> <changes>`<br>Redis 默认配置文件中提供了三个条件：<br>`save 900 1`<br>`save 300 10`<br>`save 60 10000`<br> | 分别表示 900 秒（15 分钟）内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改。<br>指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合 |
  | 10   | `rdbcompression yes`                                         | 指定存储至本地数据库时是否压缩数据，默认为 yes，Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大 |
  | 11   | `dbfilename dump.rdb`                                        | 指定本地数据库文件名，默认值为 dump.rdb                      |
  | 12   | `dir ./`                                                     | 指定本地数据库存放目录                                       |
  | 13   | `slaveof <masterip> <masterport>`                            | 设置当本机为 slave 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步 |
  | 14   | `masterauth <master-password>`                               | 当 master 服务设置了密码保护时，slav 服务连接 master 的密码  |
  | 15   | `requirepass foobared`                                       | 设置 Redis 连接密码，如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH <password> 命令提供密码，默认关闭 |
  | 16   | `maxclients 128`                                             | 设置同一时间最大客户端连接数，默认无限制，Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息 |
  | 17   | `maxmemory <bytes>`                                          | 指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后，Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区 |
  | 18   | `appendonly no`                                              | 指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘，如果不开启，可能会在断电时导致一段时间内的数据丢失。因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no |
  | 19   | `appendfilename appendonly.aof`                              | 指定更新日志文件名，默认为 appendonly.aof                    |
  | 20   | `appendfsync everysec`                                       | 指定更新日志条件，共有 3 个可选值：<br>no：表示等操作系统进行数据缓存同步到磁盘（快）<br>always：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）<br>everysec：表示每秒同步一次（折中，默认值）<br> |
  | 21   | `vm-enabled no`                                              | 指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将浏览量较少的页即冷数据 swap 到磁盘上，浏览多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析 Redis 的 VM 机制） |
  | 22   | `vm-swap-file /tmp/redis.swap`                               | 虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享 |
  | 23   | `vm-max-memory 0`                                            | 将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据都是内存存储的(Redis 的索引数据 就是 keys)，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0 |
  | 24   | `vm-page-size 32`                                            | Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值 |
  | 25   | `vm-pages 134217728`                                         | 设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，，在磁盘上每 8 个 pages 将消耗 1byte 的内存。 |
  | 26   | `vm-max-threads 4`                                           | 设置连接swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4 |
  | 27   | `glueoutputbuf yes`                                          | 设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启 |
  | 28   | `hash-max-zipmap-entries 64`<br>`hash-max-zipmap-value 512`  | 指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法 |
  | 29   | `activerehashing yes`                                        | 指定是否激活重置哈希，默认为开启（后面在介绍 Redis 的哈希算法时具体介绍） |
  | 30   | `include /path/to/local.conf`                                | 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件 |

## 四、持久化之`RDB`

rdb和aof可以共存，默认寻找aof文件

##### 1.`RDB(Redis DataBase)`是什么？

- 在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是行话讲的Snapshot快照，他恢复时是将快照文件直接读到内存里

- redis会单独创建一个子进程进行持久化，会先将数据写入到一个临时文件中，待持久化进程都结束了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作，这就确保了极高的性能，如果需要大规模的数据恢复，且对数据恢复的完整性不是特别敏感，那RDB方法要比AOF方式更加高效。RDB的缺点是最后一次持久化后的数据可能丢失。

- RDB是整个内存的压缩过的Snapshot，RDB的数据结构，可以配置复合的快照出发条件，默认：

  ​			1.一分钟内改了1万次

  ​			2.五分钟内改了10次

  ​			3.十五分钟内改了1次

- 优势

  - 适合大规模的数据恢复
  - 对数据完整性和一致性要求不高

- 劣势

  - 在意外挂的话，最后一次备份不进去
  - fork的时候，内存中被克隆了一份，大致2倍的膨胀性需要考虑

##### 2.`Fork`

- `Fork`的作用是复制一个和当前进程一样的进程，新进程的所有数据、数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程

##### 3.如何触发RDB快照

- 配置文件中默认的快照配置
  - 冷拷贝之后重新使用
  - 备份的机器分开，使用不同的机器
- 命令Sava或者是bgsave
  - Sava：只管保存，其他不管，全部阻塞
  - bgsava:redis会在后台异步进行快照操作，快照同时还可以响应给客户端请求
- 执行flushall命令，也会产生dump.rdb文件，但是里面是空的，无意义

​	

##### 4.如何恢复

- 将备份文件移动到redis安装目录并启动服务即可

## 五、持久化之`AOF`

##### 1.`AOF`是什么？

- 以日志的形式来记录每个写的操作，将redis执行过的所有的指令记录下来(读操作不记录)，只许追加文件不可以改写文件，redis启动之初会读取改文件重新构建数据，换而言之，重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据恢复

- `AOF`保存的是`appendonly.aof`文件

##### 2.`aof`文件修复

```java
//执行这个命令修复aof文件
redis-check-aof --fix appendonly.aof
```

##### 3.配置位置

- `appendonly.aof`：默认是no，如果改成yes，就打开了aof持久化

- `appendfilename`:appendonly.aof
- `appendsync`:
  - always:同步持久化，每次发生数据变更会被立刻记录到磁盘，性能较差但数据完整性比较好
  - `everysec`:出厂默认推荐，异步操作，每秒记录  如果一秒内宕机，有数据丢失

##### 4.`AOF`启动/修复/恢复

- 正常恢复
  - 启动，修改默认的`Appendonly` no，改为为yes
  - 将有数据的aof文件复制一份到对应目录
  - 恢复：重启redis然后重新加载
- 异常恢复
  - 启动
  - 备份被写坏的`aof`文件
  - 修复：使用`Redis-check-aof --fix` 进行修复
  - 恢复：重启redis然后重新加载

5.`Rewrite`

- 是什么？
  - aof是采用文件追加方式，导致文件越来越大，为了避免出现此情况，新增了重写机制，当aof文件大小超过所设定的阈值时，redis就会启动aof文件的内容压缩，只保留可以恢复数据的最小指令，可以使用命令bgrewriteaof



- 重写原理
  - `AOF`文件持续增长而过大时，会fork出一条新进程将文件重写
- 触发机制
  - redis会记录上次重写时的AOF文件大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发

![1604565866804](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604565866804.png)

![1604566046698](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604566046698.png)

## 六、redis事务

##### 1.是什么？

- 可以一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都可以去序列化，按顺序地串行化执行而不会被其他命令插入，不许加塞

##### 2.能干嘛？

- 一个队列中，一次性、顺序性、排他性的执行一系列命令

##### 3.怎么用？

 - 常用命令
   	- multi：标记一个事务块的开始
      	- exec:执行所有事务块的命令
   	- `unwatch`:取消watch命令对所有key的监控
   	- watch key[key……]:监视一个或多个key，如果在事务执行之前这个key被其他命令所改动，那么事务将被打断
   	- discard:取消事务，放弃执行事务块内的所有命令
	- 正常执行
	- 放弃事务
	- 全体连坐
	- 冤头债主
 - watch监控
    - 乐观锁/悲观锁/CAS(check and set)
       - 乐观锁
         	- 更新的时候会有一个版本号，提交的时候版本必须大于记录当前版本才能更新
       - 悲观锁
         	- 就是把整张表锁住，别人拿这个数据就会block直到它拿到锁
       - CAS
         	- 
      	- 初始化信用卡可用余额和欠额
   	- 无加塞篡改，先监控再开启multi，保证两笔金额变动在同一个事务内
   	- 有加塞篡改
   	- unwatch
   	- 一旦执行了exec之前加的监控锁都会被取消掉了
   	- 小结

![1604570858040](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604570858040.png)

## 七、`redis`的发布和订阅

##### 1.是什么？

- 进程间的一种消息通信模式

## 八、`redis`主从复制

##### 1.是什么？

- 主机数据更新后根据配置和策略，自动同步到备机上读写机制。主机挂了，迅速把指针指向从机

##### 2.能干嘛？

- 读写分离
- 容载恢复

##### 3.怎么用？

- 配从库不配主库
- 从库配置：`slaveof`(读) 主库IP  主库端口
  - 每次与master断开之后，都需要重新连接，除非配置到redis.conf文件中
- 修改配置文件细节操作
- 常用三招
  - 一主二仆
    - info replication
    - 从机备份 ：  lsaveof   主机ip    端口
    - 只有主机可以进行写的操作，从机只能读
    - 当主机挂了，从机就进入待命状态
    - 当从机挂了，重新启动会就是master，需要重新连接，除非写进配置文件
  - 一条龙服务
    - 主机<----从机1<----从机2……
  - 反客为主
    - 主机挂了，使用slaveof  no  one，让从机成为主机，翻身农奴把歌唱

##### 4.复制原理

​	1.slave启动成功后连接到master后会发送一个sync命令

​	2.master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集的命令，在后台进程执行完毕之后，master将传送整个数据文件到slave，以完成一次同步

- 全量复制

  - 而slave服务在接收到数据库文件数据后，将其存盘你并加载到内存中

- 增量复制

  - master继续将新的所有收集到的修改命令一次传给slave，完成同步

    但是只要是重新连接master，一次完全同步将被自动执行

首次是全量复制，之后是增量复制

##### 5.哨兵模式

- 反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库
- 怎么玩？
  - 自定义的.myredis目录下新建sentinel.conf文件，名字绝不能错
  - 配置哨兵
    - sentinel   monitor   主机名字    主机ip   主机端口    投票机制
    - 
  - 启动哨兵
    - `redis-sentinel /myredis/sentinel.conf`,上述目录按照各自实际情况配置
  - 如果主机挂了，就会自动地选出一台主机
  - 如果主机回来了，就会变成从机

##### 6.复制的缺点