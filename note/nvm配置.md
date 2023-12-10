# ⭐nvm安装

## 1.安装前提

——清除所有已经安装的nodejs版本

1.1.控制模板->程序->删除nodejs

1.2.C:\User\用户名 .npmrc文件是否存在，有就删除

1.3.逐一查看一下文件是否存在，存在就删除

```sh
C:\Program Files (x86)\Nodejs
C:\Program Files\Nodejs
C:\Users\用户名\AppData\Roaming\npm
C:\Users\用户名\AppData\Roaming\npm-cache
```

1.4.查看是否删除成功:cmd->node -v



## 2.nvm下载

nvm 1.1.7-setup.zip（推荐)

> 下载地址：[https://github.com/coreybutler/nvm-windows/releases](https://github.com/coreybutler/nvm-windows/releases)
>



2.1.安装时，选择修改安装路径



2.2.确认是否安装 cmd->nvm 第一行有running version x.x.xx就行

![image-20230808194703235](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230808194703235.png)



## 3.nvm配置



3.1.镜像资源：nvm安装路径下的setting.txt，打开后加入2句话(目的是更换为国内镜像资源，下载更快)

```shell
node_mirror: https://npm.taobao.org/mirrors/node/
npm_mirror: https://npm.taobao.org/mirrors/npm/
```



3.2.环境变量配置：我的电脑->属性->高级系统设置->环境变量->系统环境变量->Path:默认有配置



3.3.在nvm目录下新建nodejs文件，环境变量中(用户和系统）NVM_SYMLINK的内容改为：xxxxx/nvm/nodejs(刚刚新建的文件目录下)



3.4.系统、文件配置

```shell
(1):node_配置：在nvm目录下新建node_cache,node_global

(2):cmd:npm config set prefix "D:\DevelopmentTools\Nodejs\nvm\node_global"
        npm config set cache "D:\DevelopmentTools\Nodejs\nvm\node_cache"
        
(3):系统用户变量->path->编辑：..\nvm\node_global
    系统环境变量->Path->编辑：..\nvm\node_global
    系统环境变量->Path->新建：变量名：NODE_PATH 变量值：..\nvm\node_global\node_modules
```

## 4.镜像资源配置

（安装时最后指令加 -g,这样会存放到自己新建的文件node_global/node_modules下，

​	不然默认c盘:用户/node_modules）

```shell
#4.1.配置淘宝镜像:cmd->  
npm install -g cnpm --registry=https://registry.npm.taobao.org/

#4.2.切换镜像的工具:cmd->  
npm install nrm -g

#4.3.通过 nrm ls 命令，查看npm的仓库列表，带 * 的就是当前选中的镜像仓库
nrm ls

#4.4.指定要使用的镜像源：
nrm use taobao

#4.5.测试速度：
nrm test npm

#4.6查看镜像
npm config get registry

#4.7配置镜像
npm config set registry=https://registry.npm.taobao.org/
```



## 5.nodejs安装和使用

```shell
#5.1.查看目前的nodejs版本（现在默认为空，毕竟前面删除了）:
nvm ls 

#5.2.查看可下载版本：
nvm ls avaialble 

#5.3.下载版本：
nvm install x.x.x(例：18.10.0)

#5.4.更改nodejs版本：
nvm use x.x.x
```

​	

## 6.nvm命令大全

```shell
#查找本电脑上所有的node版本
nvm nvm list 
#查看已经安装的版本
nvm list 
#查看已经安装的版本
nvm list installed 
#查看网络可以安装的版本
nvm list available 
#安装最新版本nvm
nvm install 
#切换使用指定的版本node
nvm use <version> 
#列出所有版本#
nvm ls 
#显示当前版本
nvm current
#给不同的版本号添加别名
nvm alias <name> <version> 
#删除已定义的别名
nvm unalias <name>
#在当前版本node环境下，重新全局安装指定版本号的npm包
nvm reinstall-packages <version> 
#打开nodejs控制
nvm on 
#关闭nodejs控制
nvm off 
#查看设置与代理
nvm proxy 
#设置或者查看setting.txt中的node_mirror，如果不设置的默认是 https://nodejs.org/dist/
nvm node_mirror [url] 
#设置或者查看setting.txt中的npm_mirror,如果不设置的话默认的是https://github.com/npm/npm/archive/.
nvm npm_mirror [url] 
#卸载制定的版本
nvm uninstall <version> 
#切换制定的node版本和位数
nvm use [version] [arch] 
#设置和查看root路径
nvm root [path] 
#查看当前的版本
nvm version 
	        
```


​		  