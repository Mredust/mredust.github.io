# 一、主机配置

打开网络共享中心，修改vmnet1 和 vmnet8 配置

![image-20231007231955614](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007231955614.png)



## 1.vmnet1配置

![image-20231007230816678](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007230816678.png)



## 2.vmnet8配置

![image-20231007230938285](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007230938285.png)





# 二、虚拟机配置

## 1.打开网络编辑器

![](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230802095853758.png)

## 2.修改vmnet1配置

> 主机原先配置的子网IP：192.168.91.1 ——> 这里第四位默认为0 ，只需要第三位一样即可

![image-20231007231119228](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007231119228.png)

### 2.1修改DHCP配置

> 修改DHCP的起始IP，范围在 1~254

![image-20231007231412115](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007231412115.png)



## 3.修改vmnet8配置

> 主机原先配置的子网IP：192.168.44.1 ——> 这里第四位默认为0 ，只需要第三位一样即可

![image-20231007231504258](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007231504258.png)

### 3.1修改NAT配置

> 网关第四位可变

![image-20231007231707933](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007231707933.png)

### 3.2修改DHCP配置

> 修改DHCP的起始IP，范围在 1~254

![image-20231007231815037](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007231815037.png)





## 4.配置国内NDS

<h2>阿里</h2>

```
223.5.5.5
223.6.6.6
```

<h2>腾讯</h2>

```
119.29.29.29
182.254.118.118
```

<h2>114DNS</h2>

```
114.114.114.114
115.115.115.115
```





=====================================================================================================

```
上述操作执行一次即可
上述操作执行一次即可
上述操作执行一次即可
```

```
除非还原了设置
```

![image-20231007233447994](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231007233447994.png)

=====================================================================================================





# 三、IP配置

## 1.修改Linux配置文件

```shell
vim /etc/sysconfig/network-scripts/ifcfg-ens33
```



## 2.1.添加方式一：

```shell
#修改配置
ONBOOT=yes  #启动网卡
BOOTPROTO="static"  #设置静态IP

#添加配置,IP第四位可变
#设置的固定IP
IPADDR=192.168.44.101  
#子网掩码
NETMASK=255.255.255.0 
#网关
GATEWAY=192.168.44.2 
#域名
DNS1=223.5.5.5  
DNS2=223.6.6.6

=======================

IPADDR=192.168.127.130  
NETMASK=255.255.255.0 
GATEWAY=192.168.127.2 
DNS1=114.114.114.114
DNS2=8.8.8.8
```




## 2.2.添加方式二:

> 语法
>
> nmcli con add | mod | delete  <连接名称> [properties]
>
> properties选项
>
> |——ipv4.method manual | auto  #修改BOOTPROTO=static | dhcp
> |
> |——ipv4.addresses "192.168.91.44" # 自定义IP地址
> |
> |——ipv4.gateway "191.168.91.2" #网关
> |
> |——ipv4.dns1 "114.114.114.114"
> |
> |——ipv4.dns2 "8.8.8.8"

```shell
#展示连接名称
nmcli con show
```

```shell
#配置IP，网关，DNS 
nmcli con mod ens33 ipv4.addresses "192.168.150.130/24" ipv4.gateway "192.168.150.2" ipv4.dns "114.114.114.114 8.8.8.8" ipv4.method manual
```

```shell
#重新加载
nmcli con reload
nmcli con up ens33
```



## 3.重启网络服务

```shell
systemctl restart network
```



# 四、SSH连接缓慢

## 1.修改SSH配置

```shell
vim /etc/ssh/sshd_config
```

```shell
#关闭DNS反向解析
UseDNS  no 
#关闭SERVER上的GSS认证
GSSAPIAuthentication  no
#忽略以前登录过主机的记录
IgnoreRhosts yes
```



## 2.更新资源

```shell
service restart sshd
```

