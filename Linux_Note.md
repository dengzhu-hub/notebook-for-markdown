# Linux Note

跟着老男孩学运维第 8 章，接着笔记后面

## time：2022/2/28

## 8.1 ==fdisk==：磁盘分区工具

| 参数选择 | 解释说明             |
| -------- | -------------------- |
| ==-l==   | 显示所有磁盘分区信息 |

> eg:

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img20220528194229.png)



### 上述信息功能说明如下：

> Device: 分区，这里有两个分区；
>
> Boot: 启动分区。用*表示的是启动分区；
>
> Start：表示开始的柱面；
>
> End：表示结束的柱面；
>
> Block：block块数量；
>
> Id：分区类型id；
>
> System：分区类型；

### 自己分区

先创建一块硬盘：

然后再去查看

```linux
fdisk -l
```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528201014.png)

```
[root@localhost ~]# fdisk  -l /dev/sdb
```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528201218.png)

- 开始分区

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528201720.png)

```
[root@localhost ~]# partprobe  /dev/sdb        //通知内核分区表已更改
```

+ 格式化磁盘

```
[root@localhost dev]# mkfs.ext4  /dev/sdb1
```



![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528202148.png)



```
[root@localhost dev]# tune2fs  -c -1 /dev/sdb1
```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528202310.png)

```
[root@localhost mnt]# mount /dev/sdb1 /mnt/      //挂载到指定目录
```



![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528202500.png)

+ 开机自动挂在

```
[root@localhost mnt]# vi /etc/fstab
```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528202729.png)

+ 非交互式分区

![image-20220528203121238](C:\Users\30521\AppData\Roaming\Typora\typora-user-images\image-20220528203121238.png)==partprobe==: 更新内核的硬盘分区表信息

| 参数选择 | 解释说明       |
| -------- | -------------- |
| -d       | 不更新内核     |
| -s       | 显示摘要和分区 |

```
[root@localhost mnt]# partprobe  /dev/sdb1
```

==tune2fs==： 调整ext2/ext3/ext4文件系统参数

| 参数选项 | 解释说明                   |
| -------- | -------------------------- |
| -c       | 设置自检次数               |
| -C       | 设置文件系统已经挂在的次数 |
| -I       | 设置自检时间间隔           |
| -j       | 将ext2转换为ext3类型       |
| -l       | 查看文件系统信息           |

==eg==

```
[root@localhost mnt]# tune2fs  -C 2 /dev/sdb1   //设置挂载次数
```



![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528204134.png)

```
[root@localhost mnt]# tune2fs  -l /dev/sdb1 |grep -i mount
[root@localhost mnt]# tune2fs  -i 3 /dev/sdb1  //设置强制自检的时间
[root@localhost mnt]# tune2fs  -l /dev/sdb1 |grep -i check //查看

```

==parted==：磁盘分区工具

**对大于2TB磁盘的操作**

| 参数选项 | 解释说明             |
| -------- | -------------------- |
| -l       | 显示所有磁盘分区信息 |
| -h       | 查看帮助             |

```
[root@localhost mnt]# parted  -l
```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528205022.png)

Number：分区编号

Strat： 分区开始位置

End：分区结束位置

Size：分区大小

Type：分区类型

Primary：为主分区

File System： 文件系统，ext4，swap

Flags：标志位，boot为启动分区

==eg==

```
[root@localhost mnt]# parted  /dev/sdb
mklable gpt //为sdb磁盘创建gpt分区表，大于2TB的需要
yes
mkpart primary 0 500   //创建主分区 500M
Ignore
p

rm 1   //删除第一个分区
```

==mkfs==：创建Linux文件系统

| 参数选项 | 解释说明                   |
| -------- | -------------------------- |
| -t       | 指定要创建的文件类型       |
| -c       | 创建文件系统是检查磁盘坏块 |
| -v       | 显示详细信息               |

```
[root@localhost mnt]# ls /sbin/mkfs*
```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220528210038.png)

通过mkfs（-t）创建文件系统

```
[root@localhost mnt]# mkfs -t ext4 -v /dev/sdc
```

通过mkfs.ext4创建文件系统

```
[root@localhost mnt]# mkfs.ext4 /dev/sdb
```

==dumpe2fs==：导出ext2/ext3/ext4文件系统信息

```

[root@localhost /]# dumpe2fs  /dev/sda2 |egrep  -i "block size|block count"

```



==resize2fs==: 调整ext2/ext3/ext4 文件系统大小

```

[root@localhost /]# resize2fs  /dev/sda1

```

| 参数选项 | 解释说明                         |
| -------- | -------------------------------- |
| -a       | 自动修复文件系统                 |
| -s       | 按顺序检查多个文件系统           |
| -t       | 指定要检查的文件系统类型         |
| -A       | 依照/etc/fstab配置文件的内容检查 |
| -N       | 不执行指令                       |

==dd==：转换或复制文件

| 参数选项      | 解释说明                    |
| ------------- | --------------------------- |
| if=<输入文件> | input file从指定文件读取    |
| of=<输出文件> | 写入到指定文件output file   |
| bs=<字节数>   | 一次读写的字节数 block size |
| count=<块数>  | 指定复制block块的个数       |

eg:

```
[root@localhost jackDeng]# dd if=/dev/sda1 of=text.img

```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220531111023.png)

删除/dev/sda1

```
dd if=/dev/zero of=/dev/sda1
```

制作Linux系统ISO镜像

```
dd if=/dev/cdrom of=Centos.iso
```

dd复制进行格式转换

```
dd if=text.txt conv=ucase of=text.txt_img
```

==mount==：挂载文件系统

| 参数选项 | 解释说明                               |
| -------- | -------------------------------------- |
| -l       | 显示系统以挂载的设备信息               |
| -a       | 根据/etc/fstab文件里的配置挂载文件系统 |
| -t       | 指定挂载的文件系统                     |
| -o       | 接挂载选项                             |
| -r       | 只读挂载                               |
| -w       | 读写挂载                               |

mount -o可接参数

| 参数选项 | 解释说明 |
| -------- | -------- |
| async    |          |
| sync     |          |
| atime    |          |
| noatime  |          |
| auto     |          |
| exec     |          |
|          |          |

对系统光驱进行挂载

```
[root@localhost jackDeng]# mount /dev/cdrom /mnt

```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220531113857.png)

开机自启

```

[root@Jackdeng ~]# chkconfig --level 35 nfs on
[root@localhost jackDeng]# mount -t nfs -o nosuid,noexec,nodev 124.221.178.213:/root/hexo data/
mount -o remount,rw
```

==umount==:卸载文件系统

| 参数选项 | 解释说明                               |
| -------- | -------------------------------------- |
| -f       | 强制卸载                               |
| -l       | 将文件系统从文件系统层次结构中分离出来 |

==df==：报告文件系统磁盘空间使用情况

| 参数选项 | 解释说明                |
| -------- | ----------------------- |
| -a       | 显示所有文件系统        |
| -h       | 以容易理解的格式显示    |
| -i       | 显示文件系统的inode信息 |
| -t       | 显示指定类型的磁盘      |
| -T       | 列出文件系统类型        |

==mkswap==: 创建交换分区

| 参数选项 | 解释说明     |
| -------- | ------------ |
| -c       | 创建前先检查 |
| -f       | 强制创建     |

```
```

==swapon==: 激活交换分区

-s  显示所有交换分区的信息

==swapoff==：关闭交换分区

-a   关闭所有交换分区

==sync==：刷新文件系统缓冲区



# Linux 第九章

##Linux进程管理

==ps==：查看进程

+ UNIX格式：一个“-”开头。
+ BSD格式：没有“-”开头。
+ GNU格式：两个“-”开头。

| 参数选项 | 解释说明                                         |
| -------- | ------------------------------------------------ |
| -a       | 显示所有终端下执行的进程                         |
| a        | 显示与终端相关的所有进程，包含每个进程的完整路径 |
| x        | 显示与终端无关的所有进程                         |
| u        | 显示进程的用户信息                               |
| -e       | 显示所有进程                                     |
| -f       | 显示额外的UID.PPID.C与STIME                      |
| -u       | 显示指定用户的进程信息                           |

```
[root@localhost jackDeng]# ps -ef

```

![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220531123619.png)

- UID:进程被该UID所拥有
- PID:进程的标识号
- C:CPU使用的资源百分比
- PPID:进程的父进程的标识号
- STIME: 进程开始的时间
- TTY: 与终端无关显示“？”，若是在本机显示tty1-tty6，pts/0表示由网络连接主机进程
- TIME: 进程所使用的总的CPU时间
- CMD：正在执行的命令

```
ps -ef |grep ssh
ps aux
ps -u root
ps axf
```



==pgrep==: 查找匹配条件的进程



-u ：   显示指定用户的所有进程

pgrep -u root

==kill==：终止进程



| 参数选项 | 解释说明                                         |
| -------- | ------------------------------------------------ |
| -l       | 列出所有信号名称                                 |
| -p       | 指定kill命令只打印相关进程的进程号，而不发送信号 |
| -s       | 指定要发送的信号                                 |



![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220531124655.png)

```c++

[root@localhost jackDeng]# kill -l

```

==killall==:通过进程名终止进程

| 参数选项 | 解释说明                   |
| -------- | -------------------------- |
| -u       | 终止指定用户的进程         |
| -w       | 等待所有被终止的进程死去。 |

==pkill==：通过进程名终止进程

⭐⭐⭐⭐⭐

| 参数选项 | 解释说明           |
| -------- | ------------------ |
| -t终端   | 杀死指定终端的进程 |
| -u用户   | 杀死指定用户的进程 |

```
pkill -u oldboy
```



![](https://makeforpicgo.oss-cn-chengdu.aliyuncs.com/img/20220531170919.png)





==rpm==：RPM包管理器

⭐⭐⭐⭐⭐

| 参数选项 | 解释说明                                                     |
| -------- | ------------------------------------------------------------ |
| -q       | 查询软包                                                     |
| -p       | 后接以".rpm" 为后缀的软件包❤️                                 |
| -i       | 如果与-qp配合使用，则表示显示软件包的概要信息<br />安装软件包， |
| -l       | 显示软件包中的所有文件列表                                   |
| -R       | 显示软件包的依赖环境                                         |
| -v       | 显示详细信息                                                 |
| -h       | 用#显示安装进度条                                            |
| -a       | 与-q参数搭配使用，用于查询所有的软件包                       |
| -e       | 卸载软件包                                                   |
| -f       | 查询文件或命令属于哪个软件包                                 |

查看软件包：

```
[root@localhost jackDeng]# rpm -qpi mysql57-community-release-el7-10.noarch.rpm

```

```
查看rpm包内容
[root@localhost jackDeng]# rpm -qpl mysql57-community-release-el7-10.noarch.rpm

```

查看rpm包依赖

```

[root@localhost jackDeng]# rpm -qpR mysql57-community-release-el7-10.noarch.rpm

```



安装rpm包

```

[root@localhost jackDeng]# -ivh -qpR mysql57-community-release-el7-10.noarch.rpm
很重要的
```

- 查询系统是否安装指定的rpm包

```
[root@localhost jackDeng]# rpm -qa lrzsz

```

- x卸载rpm包

```

[root@localhost data]# rpm -e mysql57-community-release

```

查询文件属于哪个rpm包

```
[root@localhost data]# rpm -qf $(which  mysql)

```

 

