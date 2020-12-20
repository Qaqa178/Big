# 一、Linux的目录结构

| /bin       | 是Binary的缩写，这个目录存放着经常使用的命令                 |
| ---------- | ------------------------------------------------------------ |
| /sbin      | s是Super User的意思，这里存放的是系统管理员使用的系统管理程序 |
| /home      | 存放普通用户的目录                                           |
| /root      | 该目录为系统管理员                                           |
| /lib       | 系统开机所需要的最基本的动态连接共享库                       |
| /lost      | 这个目录存放系统非法关机时的文件                             |
| /etc       | 所有的系统管理所需要的配置文件和子目录                       |
| /usr       | 用于存放用户的应用程序和文件                                 |
| /boot      | 存放启动Linux时使用的一些核心文件                            |
| /proc      | 它是系统内存的映射，访问这个目录来获取系统信息               |
| /srv       | service的缩写，该目录存放一些服务启动之后需要提取的数据      |
| /sys       | 该目录安装了2.6内核中新休闲的一个文件系统                    |
| /tem       | 这个目录用于存放一些临时文件                                 |
| /dev       | 把所有的硬件用文件的形式存储                                 |
| /media     | Linux系统会自动识别一些设备，会把识别的设备挂在到这个目录    |
| /mnt       | 该目录是为了让用户临时挂在别的文件系统的，我们可以将外部存储挂在在/mnt/上，然后就进入目录就和意义查看里面的内容 |
| /opt       | 存放需要安装的软件放在这个目录                               |
| /usr/local | 软件安装的目录                                               |
| /var       | 存放日志                                                     |
|            |                                                              |

# 二、vi/vim编辑器

#### 1.vi/vim三种常见模式

- 正常模式
  - 以vim打开一个文档，就进入了一般模式
- 编辑模式
  - 在这个模式下可以输入内容。按下a  i    o等任何一个都可以进入
- 命令行模式

#### 2.常用快捷键

- 拷贝当前行  yy，拷贝当前行向下n行   n yy，p粘贴
- 删除当前行 dd
- 查找某个单词【命令行下  /关键字】，输入n 就是查找写一个
- 设置文件行号【set nu 】，取消【set nonu】
- 编辑 /etc/profile文件，使用快捷键到文档的最末行【G】和最首行【gg】

#### 3.关机、重启命令

| shutdown -h now | like进行关机         |
| --------------- | -------------------- |
| shutdown  -h 1  | hello，1分钟后关机   |
| shutdown -r now | 现在从新启动计算机   |
| halt            | 关机                 |
| reboot          | 重启                 |
| sync            | 把内存数据同步到磁盘 |

==不管是重启还是关机，都要运行sync指令，把内存数据同步到磁盘上==

# 三、用户管理

- Linux系统是一个多用户的操作同，任何一个要使用资源的用户，都必须向系统管理员申请一个账号，然后以这个账号身份进入系统。
- Linux的用户至少要属于一个组

#### 1.常用指令

|   添加用户   | useradd 【选项】 用户名==当创建用户成功后，会自动的创建和用户同名的家目录，也可以通过useradd -d指定目录 新的用户名，给创建的用户指定家目录== |
| :----------: | ------------------------------------------------------------ |
|   指定密码   | passwd  用户名                                               |
|   删除用户   | 1.userdel  用户名（保留家目录）；2.userdel  -r  用户名（同时删除家目录） |
| 查询用户信息 | id   用户名![1604974420238](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604974420238.png) |
|   切换用户   | su - 用户名           ==输入exit就能返回到原来的用户==       |
| 查看当前用户 | whoami                                                       |

#### 2.用户组 

- 类似于角色，系统可以对共性的多个用户进行统一管理

  | 增加组               | groupadd     组名             |
  | -------------------- | ----------------------------- |
  | 删除组               | groupdel       组名           |
  | 增加用户时直接加上组 | useradd  -g  用户组    用户名 |
  | 修改用户组           | usermod   -g   用户组  用户名 |
  |                      |                               |
  |                      |                               |

- 用户和组的相关文件

  | /etc/passwd文件 | ![1604976026078](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604976026078.png) |
  | --------------- | ------------------------------------------------------------ |
  | /etc/shadow文件 | 口令配置文件![1604976254841](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604976254841.png) |
  | /etc/group文件  | 记录Linux包含的组信息![1604976276123](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604976276123.png) |
  |                 |                                                              |
  |                 |                                                              |
  |                 |                                                              |

# 四、使用指令

#### 1.指令运行级别 

- 0：关机

- 1：单用户【找回丢失的密码】

- 2.多用户无网络

- 3.多用户有网络

- 4.系统未曾使用保留给用户

- 5.图形界面

- 6.重启

  ==常用运行命令是3和5，要修改默认的运行级别，可改文件，系统的运行级别配置文件是/etc/inittab文件==

```linux
/etc/inittab 的id：5：initdefalut：这一行中的数字
命令init：init[0123456]
```

#### 2.切换运行级别的指令

==init:[0123456]==

###### 如何找回丢失的root密码？

- 思路：进入到单用户模式，然后修改root密码。因为进入单用户模式，root不需要密码就可以登录。

#### 3.帮助指令

- 当我们对某个指令不熟悉时，可以使用Linux提供的帮助指令来了解这个指令的使用方法

| 指令            | 功能                  |
| --------------- | --------------------- |
| man获得帮助信息 | man【命令或配置文件】 |
| help            | help命令              |

#### 4.文件目录类指令

| 指令       |                                                              |      |      | 作用                                                         |
| ---------- | ------------------------------------------------------------ | ---- | ---- | ------------------------------------------------------------ |
| ==pwd==    |                                                              |      |      | 显示当前工作目录的绝对路径                                   |
| ==ls==     | ==-a==显示当前目录中所有的文件和目录 ==    ==-l==以列表的形式显示信息 |      |      | 显示当前目录下文件和目录                                     |
| ==cd==     | cd ..回到当前目录的父目录                                    |      |      | 切换目录![1604990799075](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604990799075.png) |
| ==mkdir==  | -p：创建多级目录                                             |      |      | 创建目录                                                     |
| ==rmdir==  |                                                              |      |      | 要删除的空目录，如果非空使用==rm  -rf 文件名==删除           |
| ==touch==  |                                                              |      |      | 创建一个空文件                                               |
| ==cp A B== | -r递归复制整个文件夹![1604992531884](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604992531884.png) |      |      | 拷贝。将A拷贝到B                                             |
| ==rm==     | ==-r==  递归删除整个文件夹   ==-f==强制删除不提示            |      |      | rm   要删除的文件或文件夹                                    |
| mv         | ![1604993123222](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604993123222.png) |      |      | 移动文件或重命名                                             |
| cat        | ![1604993936921](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604993936921.png)-n:显示行号![1604993842353](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604993842353.png) |      |      | 以只读的方式查看文件内容                                     |
| more       | ![1604993984357](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604993984357.png) |      |      | 以全屏的方式分页显示                                         |
| less       | ![1604994147805](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604994147805.png) |      |      | 分屏查看文件内容                                             |
| >          | ![1604995743545](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604995743545.png) |      |      | 输出重定向，会将原来文件的内容覆盖                           |
| ==>>==     | ![1604996314500](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604996314500.png) |      |      | 追加                                                         |
| echo       | ![1604996675158](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604996675158.png) |      |      | 输出内容到控制台                                             |
| head       | ![1604996830116](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604996830116.png) |      |      | 显示文件开头部分，默认是前10行                               |
| tail       | ![1604996876424](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604996876424.png) |      |      | 输出文件中尾部的内容，默认显示最后10行                       |
| ln         | ![1604997563456](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604997563456.png) |      |      | 软连接指令                                                   |
| history    |                                                              |      |      | 查看已经执行过的历史命令                                     |

#### 5.时间日期类指令

| 指令 | 作用                                                         | 使用                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| date | ![1604998452025](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604998452025.png)![1604997996953](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604997996953.png) | ![1604998141512](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604998141512.png)![1604998099570](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604998099570.png) |
| cal  | 查看日历信息                                                 | ![1604998407580](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604998407580.png) |

#### 6.搜索查找指令

| 指令           | 作用                                                         | 使用                                                         |
| -------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| find           | find指令将从指定目录向下递归地遍历其各自目录，将满足条件的文件或者目录显示在终端==find 【搜索范围】【选项】==![1604998725903](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604998725903.png) | ![1604998895970](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604998895970.png)![1604999121287](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604999121287.png) |
| locate         | 快速定位文件路径![1604999371008](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604999371008.png) | ![1604999509985](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604999509985.png) |
| grep和管道符\| | grep过滤查找，管道符表示将前一个命令的处理结果输出传递给后面的命令![1604999585039](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604999585039.png) | ![1604999891827](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604999891827.png) |
|                |                                                              |                                                              |

#### 7.压缩和解压缩

| gzip/gunzip | gzip压缩文件；gunzip解压文件![1604999981859](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1604999981859.png) | 压缩文件之后，原文件就没有了![1605000074971](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605000074971.png) |
| ----------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| zip/unzip   | zip压缩文件；unzip解压文件；-r递归压缩，即压缩目录；-d指定解压缩的文件存放的目录![1605000250305](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605000250305.png) | ![1605002767002](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605002767002.png)![1605002781788](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605002781788.png)![1605002948951](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605002948951.png) |
| tar         | ![1605002956210](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605002956210.png)![1605003002557](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605003002557.png) | 打包指令，最后打包的指令是.tar.gz文件![1605003222991](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605003222991.png)![1605003399576](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605003399576.png)![1605003519912](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605003519912.png) |
|             |                                                              |                                                              |
|             |                                                              |                                                              |

# 五、组管理和权限管理

#### 1.Linux组的基本介绍

- Linux中的每个用户必须属于一个组，不能独立于组外。在Linux中每个文件有：所有者、所在组、其他组的概念
  - 文件/目录所有者
    - 谁创建的谁就是所有者

| ls  -ahl            | a:查看所有文件；h:大小便于查看；l:长列表形式 | 查看文件所有者 |
| ------------------- | -------------------------------------------- | -------------- |
| chown 用户名 文件名 |                                              | 修改文件所有者 |
| charp 组名 文件名   |                                              | 修改文件所在组 |
| usermod             | -g组名   用户名；-d目录名 用户名             | 改变用户所在组 |
|                     |                                              |                |

#### 2.权限的基本介绍

| -    | 普通文件                                                     |
| ---- | ------------------------------------------------------------ |
| d    | 目录                                                         |
| l    | 软连接                                                       |
| c    | 字符设备【键盘、鼠标……】                                     |
| b    | 块文件【硬盘……】                                             |
| rw   | 文件所有者可以对该文件读写                                   |
| r--  | 文件所在组的用户只有读的权限、文件其他组的用户权限           |
| rwx  | ![1605058887111](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605058887111.png) |
|      |                                                              |
|      |                                                              |

![1605058610168](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605058610168.png)

![1605058768135](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605058768135.png)

**![1605059038677](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605059038677.png)**

#### 3.修改权限

| ==chmod== | 修改文件或目录权限 | ![1605059916318](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605059916318.png)![1605060385203](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605060385203.png) |
| --------- | ------------------ | ------------------------------------------------------------ |
| chown     | 修改文件所有者     | ![1605060563420](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605060563420.png) |
| chgrp     | 改变文件所在组     |                                                              |

![1605060204189](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605060204189.png)

![1605060321188](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605060321188.png)

![1605060487433](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605060487433.png)

![1605061156153](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605061156153.png)

#### 4,、实践

![1605061422489](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605061422489.png)

2.![1605061628433](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605061628433.png)

# 六、定时任务调度

| crontab 【选项】 | -e:编辑定时任务；-l:查询任务;-r删除当前用户所有任务 | 进行定时任务设置 |
| ---------------- | --------------------------------------------------- | ---------------- |
|                  |                                                     |                  |
|                  |                                                     |                  |
|                  |                                                     |                  |
|                  |                                                     |                  |

![1605063612752](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605063612752.png)

![1605063624665](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605063624665.png)

![1605063648201](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605063648201.png)

# 七、磁盘分区、挂载

#### 1.分区基础知识

![1605075409885](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605075409885.png)

- Linux无论有几个分区，分给哪一个目录使用，它归根结底就是一个根目录，一个独立且惟一的文件结构，Linux中每个分区都是用来组成整个文件系统的一部分
- Linux采用了一种叫载入的处理方式，它的整个文件系统中包含了一整台的文件和目录，且将一个分区和一个目录联系起来。这时要载入的一个分区将使它的存储空间在一个目录下获得

![1605076659523](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605076659523.png)

![1605077889675](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605077889675.png)

![1605077924147](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605077924147.png)

![1605077981089](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605077981089.png)

#### 2.磁盘查询情况

| df    -h      | 查询系统整体磁盘使用情况                                     |      |
| ------------- | ------------------------------------------------------------ | ---- |
| du -h   /目录 | -s:指定目录占用大小；-h：带计量单位；-a：含文件；-c：列出明细的同时，增加汇总值 ； --max-depth=1：子目录深度 |      |
|               |                                                              |      |
|               |                                                              |      |
|               |                                                              |      |

#### 3.磁盘情况-工作使用指令

- 统计/home文件夹下文件的个数

  | ls -l /home \| grep “^-” \| wc -l |
  | --------------------------------- |
  |                                   |

  ![1605079386829](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605079386829.png)

# 八、网络配置

#### 1.修改IP地址

![1605080457752](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605080457752.png)

![1605081572234](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605081572234.png)

# 九、进程管理

![1605082076519](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605082076519.png)

#### 1.显示系统进程

| ps   | 查看目前系统中执行的进程；；；；-a:显示当前终端所有进程；-u：以用户的格式显示进程信息；-x:显示后台进程运行的参数 |      |
| ---- | ------------------------------------------------------------ | ---- |
|      |                                                              |      |
|      |                                                              |      |
|      |                                                              |      |
|      |                                                              |      |

![1605082293917](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605082293917.png)

![1605082602985](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605082602985.png)

![1605082663017](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605082663017.png)

#### 2.应用实例

#### 3.终止进程

| kill   -9 | 终止进程，强制-9强制停止 |      |
| --------- | ------------------------ | ---- |
| killall   | 终止所有进程             |      |
|           |                          |      |
|           |                          |      |
|           |                          |      |

- 踢掉非法用户

  ![1605083476750](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605083476750.png)

- ![1605083625015](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605083625015.png)

- 杀掉一个终端

![1605083955702](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605083955702.png)

![1605084041940](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605084041940.png)

# 十、服务管理

#### 1.介绍

- 服务本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其他程序的请求，比如（mysql、sshd防火墙等），因此我们有称为守护进程，是Linux中非常重要的知识点

- 防火墙常见命令

  ```java
  systemctl status firewalld    # 查看firewalld服务状态
  systemctl enable firewalld    # 设置firewalld服务开机启动
  systemctl disable firewalld   # 禁止firewalld服务开机启动
  service firewalld start       # 开启
  service firewalld restart     # 重启
  service firewalld stop        # 关闭
  ```

  

#### 2.动态监控进程

| top 【选项】 | -d:指定top命令每隔几秒更新。-i：使top不显示任何闲置或僵死进程。-p通过指定进程ID仅仅监控某个进程状态![1605144069979](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605144069979.png) | 显示进程，可以实时更新 |
| ------------ | ------------------------------------------------------------ | ---------------------- |
|              |                                                              |                        |
|              |                                                              |                        |
|              |                                                              |                        |

![1605144358877](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605144358877.png)

![1605144461864](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605144461864.png)

#### 3.监控网络服务状态

| netstat -anp | 查看网络情况![1605144647012](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605144647012.png) |      |
| ------------ | ------------------------------------------------------------ | ---- |
|              |                                                              |      |

![1605144838877](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605144838877.png)

# 十一、RPM包管理

| rpm -qa \| grep xx                                           |      |      |
| ------------------------------------------------------------ | ---- | ---- |
| ![1605145230950](C:\Users\14823\AppData\Roaming\Typora\typora-user-images\1605145230950.png) |      |      |

