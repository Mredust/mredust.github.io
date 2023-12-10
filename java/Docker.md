# 0.CentOS安装Docker

Docker 分为 CE 和 EE 两大版本。

CE 即社区版（免费，支持周期 7 个月），EE 即企业版，强调安全，付费使用，支持周期 24 个月。

Docker CE 分为 `stable` `test` 和 `nightly` 三个更新频道。

官方网站上有各种环境下的 [安装指南](https://docs.docker.com/install/)，这里主要介绍 Docker CE 在 CentOS上的安装。

# 1.安装Docker

Docker CE 支持 64 位版本 CentOS 7，并且要求内核版本不低于 3.10。

## 1.1.卸载（可选）

如果之前安装过旧版本的Docker，可以使用下面命令卸载：

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine \
                  docker-ce
```



## 1.2.安装docker

首先需要大家虚拟机联网，安装yum工具

```sh
yum install -y yum-utils \
           device-mapper-persistent-data \
           lvm2 --skip-broken
```



然后更新本地镜像源：

```shell
# 设置docker镜像源
yum-config-manager \
    --add-repo \
    https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
    
sed -i 's/download.docker.com/mirrors.aliyun.com\/docker-ce/g' /etc/yum.repos.d/docker-ce.repo

yum makecache fast
```



然后输入命令：

```shell
yum install -y docker-ce
```

docker-ce为社区免费版本。稍等片刻，docker即可安装成功。



## 1.3.启动docker

Docker应用需要用到各种端口，逐一去修改防火墙设置。非常麻烦，因此建议大家直接关闭防火墙！

启动docker前，一定要关闭防火墙后！！

启动docker前，一定要关闭防火墙后！！

启动docker前，一定要关闭防火墙后！！

```shell
systemctl stop firewalld	# 关闭防火墙

systemctl disable firewalld	# 禁止开机启动防火墙
```



通过命令启动docker：

```sh
systemctl start docker  # 启动docker服务

systemctl stop docker  # 停止docker服务

systemctl restart docker  # 重启docker服务

systemctl enable docker.service #开机自启docker
```



然后输入命令，可以查看docker版本：

```
docker -v
```



## 1.4.配置镜像加速

docker官方镜像仓库网速较差，我们需要设置国内镜像服务：

参考阿里云的镜像加速文档：https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors



# 2.安装Docker-Compose

## 2.1.下载

Linux下需要通过命令下载：

```shell
# 安装
curl -L https://github.com/docker/compose/releases/download/1.23.1/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
```

上传到`/usr/local/bin/`目录也可以。



## 2.2.修改文件权限

修改文件权限：

```sh
# 修改权限
chmod +x /usr/local/bin/docker-compose
# 查看版本信息 
docker-compose -version
```



## 2.3.Base自动补全命令：

```sh
# 补全命令
curl -L https://raw.githubusercontent.com/docker/compose/1.29.1/contrib/completion/bash/docker-compose > /etc/bash_completion.d/docker-compose
```

如果这里出现错误，需要修改自己的hosts文件：

```sh
echo "199.232.68.133 raw.githubusercontent.com" >> /etc/hosts
```



## 2.3.卸载Docker-Compose

```sh
# 二进制包方式安装的，删除二进制文件即可
rm /usr/local/bin/docker-compose
```



# 3.Docker镜像仓库

搭建镜像仓库可以基于Docker官方提供的DockerRegistry来实现。

官网地址：https://hub.docker.com/_/registry

```sh
# 拉取私有仓库镜像 
docker pull registry
```



## 3.1.简化版镜像仓库

搭建方式比较简单，命令如下：

```sh
# 启动私有仓库容器 
docker run -d \
    --restart=always \
    --name registry	\
    -p 5000:5000 \
    -v registry-data:/var/lib/registry \
    registry
```

命令中挂载了一个数据卷registry-data到容器内的/var/lib/registry 目录，这是私有镜像库存放数据的目录。



打开浏览器 输入地址，看到{"repositories":[]} 表示私有仓库 搭建成功

```shell
http://192.168.91.44:5000/v2/_catalog
```



## 3.2.带有图形化界面版本

使用DockerCompose部署带有图象界面的DockerRegistry，命令如下：

```yaml
version: '3.0'
services:
  registry:
    image: registry
    volumes:
      - ./registry-data:/var/lib/registry
  ui:
    image: joxit/docker-registry-ui:1.5-static
    ports:
      - 8080:80
    environment:
      - REGISTRY_TITLE=红尘私有仓库
      - REGISTRY_URL=http://registry:5000
    depends_on:
      - registry
```



## 3.3.配置Docker信任地址

我们的私服采用的是http协议，默认不被Docker信任，所以需要做一个配置：

```sh
# 打开要修改的文件
vi /etc/docker/daemon.json
# 添加内容：
"insecure-registries":["192.168.91.44:8080"]
# 重加载
systemctl daemon-reload
# 重启docker
systemctl restart docker

#新建配置
cd /tmp
mkdir registry-ui
cd registry-ui
touch docker-compose.yml#添加3.2内容
```

## 3.4运行

```
cd /tmp/registry-ui
docker-compose up -d
```

## 3.5推送和拉取

```shell
#重新tag本地镜像，名称前缀为私有仓库的地址
docker tag nginx:latest 192.168.91.44:8080/nginx:1.0
#推送镜像
docker push 192.168.91.44:8080/nginx:1.0
#拉取镜像
docker pull 192.168.91.44:8080/nginx:1.0

```







# 5.使用docker compose编排nginx+springboot项目

## 5.1.创建docker-compose目录

```shell
mkdir ~/docker-compose
cd ~/docker-compose
```

## 5.2.编写 docker-compose.yml 文件

```shell
version: '3'
services:
  nginx:
   image: nginx
   ports:
    - 80:80
   links:
    - app
   volumes:
    - ./nginx/conf.d:/etc/nginx/conf.d
  app:
    image: app
    expose:
      - "8080"
```

## 5.3.创建./nginx/conf.d目录

```shell
mkdir -p ./nginx/conf.d
```



## 5.4.在./nginx/conf.d目录下 编写itheima.conf文件

```
server {
    listen 80;
    access_log off;

    location / {
        proxy_pass http://app:8080;
    }
   
}
```

## 5.5.在~/docker-compose 目录下 使用docker-compose 启动容器

```shell
docker-compose up
```

## 5.6.测试访问

```shell
http://192.168.91.44/hello
```











