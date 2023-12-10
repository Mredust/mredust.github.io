# GitStack安装

## 1.[下载地址](https://gitstack.com/download/)



## 2.关闭端口	

- <p style="color:red;">不要着急安装</p>

> cmd + R

>  netstat -aon|findstr "80"

![image-20231207233701990](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20231207233701990.png)

> 打开任务管理器关闭对应的ID



## 3.开始安装

> 除了安装路径修改，其余默认next

> 安装完成后，会默认打开 	```http:localhost:80/gitstack```

> 账号/密码：admin









# GitStack使用

## 1.仓库位置

> Settings -> General -> Repositories Location

填入新的路径即可，默认在gitstack安装的目录下。注意，需要使用Linux下的路径形式，即类c:/a/b/c



## 2.服务端口

> Settings -> General -> Server Ports，http:80，https:443



## 3.管理员密码

> Settings -> General -> Administrator password

————————————————



# 项目开发

## 1.项目拉取

```shell
git clone http://localhost/smartApp.git			
```



## 2.文件推送

```shell
cd smartApp
git add .	#添加所有文件
git status #查看文件状态
git commit -m "注释"
git push #推送
```



## 3.用户管理

```shell
git config --global user.name 用户名(建议与GitStack仓库用户名一致) 
git config --global user.email 邮箱地址 (使用GitStack时可随机设置)
```

