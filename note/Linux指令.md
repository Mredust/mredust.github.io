#  Linux命令

## 一、用户管理



（标准创建：useradd -g 用户组 用户名）

### 1.1.添加用户

```shell
useradd 用户名
```

### 1.2.添加指定路径的用户 

```
useradd -d 路径 用户名 
```

### 1.3.设置用户密码 

```
passwd 用户名 
```

### 1.4.删除用户 

```
userdel 用户名
```

### 1.5.删除用户即主目录

```
 userdel -r 用户名
```

### 1.6.查看用户信息

```
 id 用户名
```

### 1.7.切换用户 

```
su - 用户名
```

### 1.8.退出用户/返回主用户  

```
logout/exit
```

### 1.9.添加用户组 

```shell
groupadd 组名

#创建用户组test，指明组id为2023
groupadd -g 2023 test
```

### 1.10.删除用户组 

```
groupdel 组名 
```

### 1.11.修改用户组 ID

```
groupmod -g newID 用户组 
```

### 1.12.重命名组

```
groupmod -n new old
```

### 1.13.添加用户到组

```
gpasswd -a 用户名 用户组
```

### 1.14.移除用户从组

```
gpasswd -d 用户名 用户组
```



## 二、文件目录



### 2.1.创建目录 

```
mkdir 目录名
```

### 2.2.创建多级目录 

```
mkdir -p 目录名/目录名
```

### 2.3.删除空目录 

```
rmdir 目录名
```

### 2.4.删除非空目录 

```
rm -rf 目录名
```

### 2.5.创建文件 

```
touch 文件名
```

### 2.6.复制文件 

```
cp 文件名 路径名
```

### 2.7.(强制覆盖)复制整个文件夹

```
cp -r /被复制的文件夹   /复制到新目录下的路径名
```

### 2.8.删除(强制删除)文件 

```sh
rm （-rf） 文件名
```

### 2.9.重命名 

```sh
mv 旧名 新名
```

### 2.10.移动文件 

```sh
mv 文件 路径
```

### 2.11.查看文件 

```sh
cat -n 文件
```





## 三、权限管理

### 3.1.查看文件所有者 

```sh
ls -ahl
```

### 3.2.修改文件所有者 

```sh
chown 用户名 文件名
```

### 3.3.修改文件所在 

```sh
chgrp 组名 文件名
```



## 四、任务调度

### 4.1.调度时间

```shell
*  	 *    *	   *  	* 
分	时   天 	 月	 周
#编辑调度文件
crontab -e
#查看调度文件
crontab -l
#终止调度任务
crontab -r
#重启服务
service crond restart
```

### 4.2.定时任务

```shell
at [选项] 时间（5pm + 2days:两天后下午5点执行）
```



### 4.*.实例1

```shell
#每隔一分钟，就将当前日期追加到/temp/mydate
*/1 * * * * date >> /temp/mydate
```



## 五、文件管理

### 5.1.按文件名查询

```
find /大概文件目录 -name 文件名(*.txt)
```

### 5.2.按用户查询

```
find /大概文件目录 -usr 文件名(*.txt)
```

### 5.3.按文件大小查询

```shell
#(+n:大于 -n:小于 n:等于)
find / -size +200M
```

### 5.4.快速查询

```shell
#先执行更新数据库
updatedb
locate 文件名
```

### 5.5.过滤查询

```
...... | grep 查找内容
```

### 5.6.压缩文件

```
gzip 文件名/目录名
tar -zcvf 文件名/目录名
```

### 5.7.解压文件

```
gunzip 文件.gz
tar -zxvf 文件名/目录名
```





## 六、服务管理⭐

### 6.1.查看进程

```shell
ps -ef
ps -aux |grep sshd
```

### 6.2.杀死进程

```shell
kill 进程号
```

### 6.3.查看进程树

```
pstree -p (端口)
pstree -u (用户)
```

### 6.4.管理指令

```shell
systemctl [ start | stop | restart | status ] 服务名
```

### 6.5.查看服务开机启动状态

```shell
systemctl list-unit-files [ grep | 服务名]
```

### 6.6.设置服务开机启动

```shell
systemctl enable 服务名
```

### 6.7.关闭服务开机启动

```sh
systemctl disenable 服务名
```

### 6.8.查看端口

```sh
firewall-cmd --list-port
```

### 6.9.添加端口

```sh
firewall-cmd --permanent --add-port=端口号/tcp
firewall-cmd --reload
```

### 6.10.移除端口

```shell
firewall-cmd --permanent --remove-port=端口号/tcp
firewall-cmd --reload
```

### 6.11.查看安装包

```shell
rpm -qa |[ grep | more ] 软件包
```

### 6.12.查看安装包信息

```shell
rpm -qi |[ grep | more ] 软件包
```



## 七、系统操作



### 7.1.运行级别

| 级别（init + 数字) |         说明         |
| :----------------: | :------------------: |
|         0          |         关机         |
|         1          |        单用户        |
|         2          |    多用户没有网络    |
|         3          |     多用户有网络     |
|         4          | 系统未使用保留给用户 |
|         5          |       图形界面       |
|         6          |       系统重启       |



### 7.2.忘记重置密码

```shell
#1.开机进入界面按 e 
#2.光标移到最下面(UTF-8)输入: init=/bin/sh
#3.Ctrl+x 进入到单用户模式
#4.接着在光标处输入: mount -o remount,rw /
#5.接着新一行输入: passwd
#6.输入两次要重置的密码
#7.输入: touch /.autorelabel
#8.输入: exec /sbin/init
```



### 7.3.系统日期

```shell
date		(功能描述：显示当前时间)
date +%Y	(功能描述：显示当前年份)
date +%m	(功能描述：显示当前月份)
date +%d 	(功能描述：显示当前是哪一天)
date"+%Y-%m-%d %H:%M:%S"(显示年月日时分秒)
```



### 7.4.查看分区

```shell
lsblk
```

### 7.5.添加硬盘

```shell
1.在虚拟机设置添加硬盘容量
2.进入虚拟机后reboot重启
3.分区命令：fdisk /dev/sdb -->n (p/e) 1-4 w 
4.格式化分区：mkfs -t ext4 /dev/sdb1
5.挂载分区：mount /dev/sdb1 挂载目录
6.卸载分区：unmount /dev/sdb1
7.永久挂载：vim /etc/fstab	添加uuid或者/dev/sdb1	保存退出后mount -a
```

### 7.6.查看磁盘

```shell
df -h
```



# 扩展：

## 1.安装tree

```shell
yum -y install tree
```





