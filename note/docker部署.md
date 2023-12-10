# Docker 应用部署

# 统一挂载路径

```shell
#data
-v /usr/mydata/容器名/data
#logs
-v /usr/mydata/容器名/logs
#conf
-v /usr/mydata/容器名/conf
#plugins
-v /usr/mydata/容器名/plugins
```

# 自启动 shell 脚本命令

```shell
sh /usr/mydata/sh/restartall.sh
```

# Docker 命令 ⭐

## 1.创建容器并运行

```shell
docker run --name myNginx -p 80:80 -v html:/usr/share/nginx/html -d Nginx
-name 命名
-p 端口
-v 数据卷挂载
-d 后台运行
```

## 2.进入容器

```shell
docker exec -it myNginx bash
```

## 3.查看容器

```shell
docker ps -a
```

## 4.强制删除运行中的容器

```shell
docker rm -f myNginx
```

## 5.删除镜像

```shell
docker rmi myNginx
```

## 6.停止所有容器

```shell
docker stop $(docker ps -aq)
```

| 命令        | 说明                                    |
| ----------- | --------------------------------------- |
| docker ps   | 列出当前正在运行的容器                  |
| -a          | 显示所有容器，包括已停止的容器。        |
| -q          | 仅显示容器的 ID，而不显示其他详细信息。 |
| docker stop | 停止一个或多个容器。                    |

# 部署

## 1.部署 MySQL⭐

1. 搜索 mysql 镜像

```shell
docker search mysql
```

2. 拉取 mysql 镜像

```shell
docker pull mysql:8.0.24
```

3.创建容器

```shell
docker run -id \
--name=mysql \
-p 3306:3306 \
--privileged=true \
--restart=unless-stopped \
-v /usr/mydata/mysql/conf:/etc/mysql/conf.d \
-v /usr/mydata/mysql/logs:/var/log/mysql \
-v /usr/mydata/mysql/data:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=密码 \
mysql:8.0.24
```

4.进入容器，操作 mysql

```sh
#方式一
docker exec -it mysql /bin/bash
#方式二
docker exec -it mysql bash
```

5.参数说明

| 命令                                        | 描述                                                                             |
| :------------------------------------------ | :------------------------------------------------------------------------------- |
| -d                                          | 后台运行                                                                         |
| -p 3306:3306                                | 将容器右边端口 3306 映射到主机左边端口 3306。                                    |
| --privileged=true                           | 挂载文件权限设置                                                                 |
| --restart=always/unless-stopped             | 开机总是自动重启/除了手动停止运行                                                |
| -v /usr/mydata/mysql/conf:/etc/mysql/conf.d | 将主机当前目录下的/usr/mydata/mysql/conf 挂载到容器的/etc/mysql/conf.d 配置目录  |
| -v /usr/mydata/mysql/logs:/var/logs/mysql   | 主机当前目录下的/usr/mydata/mysql/logs 目录挂载到容器的/var/logs/mysql 日志目录  |
| -v /usr/mydata/mysql/data:/var/lib/mysql    | 将主机当前目录下的/usr/mydata/mysql/data 目录挂载到容器的/var/lib/mysql 数据目录 |
| -e MYSQL_ROOT_PASSWORD=密码                 | 设置 root 用户的密码                                                             |
| mysql:8.0.24                                | MySQL 镜像                                                                       |

### 1.1.解决连接慢问题

1.进入 docker 的 MySQL 容器

```shell
docker exec -it mysql bash
```

2.编辑 my.cnf 配置

```shell
vi /etc/mysql/my.cnf
```

3.如果提示没有 vi/vim 命令,则复制配置到 Linux 中更改

```shell
docker cp mysql:/etc/mysql/my.cnf /usr/mydata/mysql/conf/my.cnf
```

4.添加如下配置

```shell
[mysqld]
skip-name-resolve #由于mysql对连接的客户端进行DNS反向解析导致连接缓慢，导致超时
```

5.重启容器

```shell
docker restart mysql
```

## 2.部署 Tomcat

1. 搜索 tomcat 镜像

```shell
docker search tomcat
```

2. 拉取 tomcat 镜像

```shell
docker pull tomcat
```

3. 创建容器，设置端口映射、目录映射

```shell
# 在/root目录下创建tomcat目录用于存储tomcat数据信息
mkdir ~/tomcat
cd ~/tomcat
```

```shell
docker run -id --name=c_tomcat \
-p 8080:8080 \
-v $PWD:/usr/local/tomcat/webapps \
tomcat
```

- 参数说明：

  - **-p 8080:8080：**将容器的 8080 端口映射到主机的 8080 端口

    **-v $PWD:/usr/local/tomcat/webapps：**将主机中当前目录挂载到容器的 webapps

## 3.部署 Nginx⭐

1. 搜索 nginx 镜像

```shell
docker search nginx
```

2. 拉取 nginx 镜像

```shell
docker pull nginx:版本
```

3. 配置目录环境

```shell
# 创建挂载目录
mkdir -p /usr/mydata/nginx/conf
mkdir -p /usr/mydata/nginx/logs
mkdir -p /usr/mydata/nginx/html
```

4.copy 文件

```shell
# 生成容器
# 将容器nginx.conf文件复制到宿主机
# 将容器conf.d文件夹下内容复制到宿主机
# 将容器中的html文件夹复制到宿主机
# 复制完成后删除容器
docker run --name nginx -p 9080:80 -d nginx:1.12.2
docker cp nginx:/etc/nginx/nginx.conf /usr/mydata/nginx/conf/nginx.conf
docker cp nginx:/etc/nginx/conf.d /usr/mydata/nginx/conf/conf.d
docker cp nginx:/usr/share/nginx/html /usr/mydata/nginx/
docker rm -f nginx
```

​ 5.创建容器

```shell
docker run -id \
--name=nginx \
--net host \
--restart=unless-stopped \
-v /usr/mydata/nginx/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /usr/mydata/nginx/conf/conf.d:/etc/nginx/conf.d \
-v /usr/mydata/nginx/logs:/var/logs/nginx \
-v /usr/mydata/nginx/html:/usr/share/nginx/html \
nginx:1.12.2

```

6.登录主机

```
192.168.91.101:80
```

​

7.参数说明

| 命令                                                       | 描述                              |
| :--------------------------------------------------------- | :-------------------------------- |
| -id                                                        | 开启标准接口输入并后台运行        |
| --name=nginx                                               | 容器命名                          |
| -p 9080:80                                                 | 将容器 80 端口映射到主机端口 9080 |
| --restart=unless-stopped                                   | 开机总是自动重启/除了手动停止运行 |
| -v /usr/mydata/nginx/conf/nginx.conf:/etc/nginx/nginx.conf | 挂载 nginx.conf 配置文件          |
| -v /usr/mydata/nginx/conf/conf.d:/etc/nginx/conf.d         | 挂载 nginx 配置文件               |
| -v /usr/mydata/nginx/logs:/var/logs/nginx                  | 挂载 nginx 日志文件               |
| -v /usr/mydata/nginx/html:/usr/share/nginx/html            | 挂载 nginx 内容                   |
| nginx:1.12.2                                               | nginx 镜像                        |
| --net host                                                 | 监听所有端口                      |

## 4.部署 Redis⭐

1. 搜索 redis 镜像

```shell
docker search redis
```

2. 拉取 redis 镜像

```shell
docker pull redis:6.2.7
```

3. 创建容器，设置端口映射

```shell
docker run -id \
--name=redis \
-p 6379:6379 \
--restart=unless-stopped \
-v /usr/mydata/redis/redis.conf:/etc/redis/redis.conf \
-v /usr/mydata/redis/data:/var/lib/redis \
redis:6.2.7 \
redis-server /etc/redis/redis.conf \
--requirepass 密码 \
--appendonly yes
```

4. 进入容器，操作 redis

```shell
#进入容器
docker exec -it redis bash
#进入客户端
redis-cli
#密码
auth 密码
```

5.参数说明

| 命令                                                  | 描述                                                                                           |
| :---------------------------------------------------- | :--------------------------------------------------------------------------------------------- |
| -id                                                   | i 开启标准输入接口，d 后台运行                                                                 |
| --name=redis                                          | 容器命名                                                                                       |
| -p 6379:6379                                          | 将容器右边端口 6379 映射到主机左边端口 6379                                                    |
| --restart=always/unless-stopped                       | 开机总是自动重启/除了手动停止运行                                                              |
| -v /usr/mydata/redis/redis.conf:/etc/redis/redis.conf | 将主机当前目录下的/usr/mydata/redis/redis.conf 目录挂载到容器 的/etc/redis/redis.conf 配置目录 |
| -v /usr/mydata/redis/data:/var/lib/redis              | 将主机当前目录下的/usr/mydata/redis/data 目录挂载到容器的/var/lib/redis 数据目录               |
| redis-server /etc/redis/redis.conf                    | 容器中设置 redis-server 每次启动读取 /etc/redis/redis.conf 这个配置为准                        |
| --requirepass 密码                                    | 设置 redis 密码                                                                                |
| --appendonly yes                                      | 启动数据持久化                                                                                 |

## 5.部署 nacos⭐

1.创建 nacos 数据库(集群)

```sql
#数据库
drop database if exists nacos_config;
create database nacos_config;
use nacos_config;

CREATE TABLE `config_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) DEFAULT NULL,
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  `c_desc` varchar(256) DEFAULT NULL,
  `c_use` varchar(64) DEFAULT NULL,
  `effect` varchar(64) DEFAULT NULL,
  `type` varchar(64) DEFAULT NULL,
  `c_schema` text,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfo_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info';


CREATE TABLE `config_info_aggr` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(255) NOT NULL COMMENT 'group_id',
  `datum_id` varchar(255) NOT NULL COMMENT 'datum_id',
  `content` longtext NOT NULL COMMENT '内容',
  `gmt_modified` datetime NOT NULL COMMENT '修改时间',
  `app_name` varchar(128) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfoaggr_datagrouptenantdatum` (`data_id`,`group_id`,`tenant_id`,`datum_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='增加租户字段';


CREATE TABLE `config_info_beta` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `beta_ips` varchar(1024) DEFAULT NULL COMMENT 'betaIps',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfobeta_datagrouptenant` (`data_id`,`group_id`,`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_beta';



CREATE TABLE `config_info_tag` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `tag_id` varchar(128) NOT NULL COMMENT 'tag_id',
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL COMMENT 'content',
  `md5` varchar(32) DEFAULT NULL COMMENT 'md5',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  `src_user` text COMMENT 'source user',
  `src_ip` varchar(20) DEFAULT NULL COMMENT 'source ip',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_configinfotag_datagrouptenanttag` (`data_id`,`group_id`,`tenant_id`,`tag_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_info_tag';


CREATE TABLE `config_tags_relation` (
  `id` bigint(20) NOT NULL COMMENT 'id',
  `tag_name` varchar(128) NOT NULL COMMENT 'tag_name',
  `tag_type` varchar(64) DEFAULT NULL COMMENT 'tag_type',
  `data_id` varchar(255) NOT NULL COMMENT 'data_id',
  `group_id` varchar(128) NOT NULL COMMENT 'group_id',
  `tenant_id` varchar(128) DEFAULT '' COMMENT 'tenant_id',
  `nid` bigint(20) NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`nid`),
  UNIQUE KEY `uk_configtagrelation_configidtag` (`id`,`tag_name`,`tag_type`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='config_tag_relation';


CREATE TABLE `group_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `group_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Group ID，空字符表示整个集群',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数，，0表示使用默认值',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_group_id` (`group_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='集群、各Group容量信息表';


CREATE TABLE `his_config_info` (
  `id` bigint(64) unsigned NOT NULL,
  `nid` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `data_id` varchar(255) NOT NULL,
  `group_id` varchar(128) NOT NULL,
  `app_name` varchar(128) DEFAULT NULL COMMENT 'app_name',
  `content` longtext NOT NULL,
  `md5` varchar(32) DEFAULT NULL,
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00',
  `src_user` text,
  `src_ip` varchar(20) DEFAULT NULL,
  `op_type` char(10) DEFAULT NULL,
  `tenant_id` varchar(128) DEFAULT '' COMMENT '租户字段',
  PRIMARY KEY (`nid`),
  KEY `idx_gmt_create` (`gmt_create`),
  KEY `idx_gmt_modified` (`gmt_modified`),
  KEY `idx_did` (`data_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='多租户改造';


CREATE TABLE `tenant_capacity` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键ID',
  `tenant_id` varchar(128) NOT NULL DEFAULT '' COMMENT 'Tenant ID',
  `quota` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '配额，0表示使用默认值',
  `usage` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '使用量',
  `max_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个配置大小上限，单位为字节，0表示使用默认值',
  `max_aggr_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '聚合子配置最大个数',
  `max_aggr_size` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '单个聚合数据的子配置大小上限，单位为字节，0表示使用默认值',
  `max_history_count` int(10) unsigned NOT NULL DEFAULT '0' COMMENT '最大变更历史数量',
  `gmt_create` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '创建时间',
  `gmt_modified` datetime NOT NULL DEFAULT '2010-05-05 00:00:00' COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='租户容量信息表';


CREATE TABLE `tenant_info` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT COMMENT 'id',
  `kp` varchar(128) NOT NULL COMMENT 'kp',
  `tenant_id` varchar(128) default '' COMMENT 'tenant_id',
  `tenant_name` varchar(128) default '' COMMENT 'tenant_name',
  `tenant_desc` varchar(256) DEFAULT NULL COMMENT 'tenant_desc',
  `create_source` varchar(32) DEFAULT NULL COMMENT 'create_source',
  `gmt_create` bigint(20) NOT NULL COMMENT '创建时间',
  `gmt_modified` bigint(20) NOT NULL COMMENT '修改时间',
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_tenant_info_kptenantid` (`kp`,`tenant_id`),
  KEY `idx_tenant_id` (`tenant_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_bin COMMENT='tenant_info';

CREATE TABLE users (
    username varchar(50) NOT NULL PRIMARY KEY,
    password varchar(500) NOT NULL,
    enabled boolean NOT NULL
);

CREATE TABLE roles (
    username varchar(50) NOT NULL,
    role varchar(50) NOT NULL,
    constraint uk_username_role UNIQUE (username,role)
);

CREATE TABLE permissions (
    role varchar(50) NOT NULL,
    resource varchar(512) NOT NULL,
    action varchar(8) NOT NULL,
    constraint uk_role_permission UNIQUE (role,resource,action)
);

INSERT INTO users (username, password, enabled) VALUES ('nacos', '$2a$10$EuWPZHzz32dJN7jexM34MOeYirDdFAZm2kuWj7VEOJhhZkDrxfvUu', TRUE);

INSERT INTO roles (username, role) VALUES ('nacos', 'ROLE_ADMIN');
```

2.拉取 nacos 镜像

```shell
docker pull nacos/nacos-server:版本
```

3.配置文件

```shell
#新建logs目录
#新建conf目录
#启动容器
#复制文件
#删除容器
mkdir -p /usr/mydata/nacos/logs/
mkdir -p /usr/mydata/nacos/conf/
docker run -p 8848:8848 --name nacos -d nacos/nacos-server:1.4.1
docker cp nacos:/home/nacos/logs/ /usr/mydata/nacos/
docker cp nacos:/home/nacos/conf/ /usr/mydata/nacos/
docker rm -f nacos
```

4.修改 application 配置

```sql
#在宿主机中修改/usr/mydata/nacos/conf/application.properties文件
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://端口:3306/nacos-config?characterEncoding=utf8&connectTimeout=1000&socketTimeout=30000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
db.user=用户名
db.password=密码
```

5.创建容器

```shell
docker run -d \
--name=nacos \
-p 8848:8848 \
-e MODE=standalone \
-v /usr/mydata/nacos/conf/:/home/nacos/conf/ \
-v /usr/mydata/nacos/logs/:/home/nacos/logs \
--restart=unless-stopped \
--privileged=true \
nacos/nacos-server:1.4.1
```

6.登录主机

```
192.168.91.101:8848/nacos/index.html
```

7.参数说明

| 命令                                         | 描述                                                    |
| -------------------------------------------- | ------------------------------------------------------- |
| -d                                           | 后台运行                                                |
| --name=nacos                                 | 容器命名                                                |
| -p 8848:8848                                 | 将容器右边端口 8848 映射到主机左边端口 8848             |
| -v /usr/mydata/nacos/conf/:/home/nacos/conf/ | 将宿主机上 Nacos 的配置文件目录映射到容器内的相应目录。 |
| -v /usr/mydata/nacos/logs/:/home/nacos/logs  | 将 Nacos 日志目录映射到容器内的相应目录。               |
| --restart=unless-stopped                     | 在退出时自动重新启动，除非手动停止容器。                |
| --privileged=true                            | 特权模式运行容器，获得更高的系统权限                    |
| nacos/nacos-server:1.4.1                     | nacos 镜像                                              |

## 6.部署 RabbitMQ⭐

1.搜索 RabbitMQ 镜像

```
docker search rabbitmq
```

2.拉取 RabbitMQ 镜像

```shell
docker pull rabbitmq:3.8.34
```

3.创建容器，设置端口映射

```shell
docker run -d \
--name rabbitmq \
-p 15672:15672 \
-p 5672:5672 \
--hostname mq1 \
--restart=unless-stopped \
-v /usr/mydata/rabbitmq/logs:/var/logs/rabbitmq \
-v /usr/mydata/rabbitmq/data:/var/lib/rabbitmq \
-e RABBITMQ_DEFAULT_USER=mredust \
-e RABBITMQ_DEFAULT_PASS=密码 \
rabbitmq:3.8.34
```

4.进入容器，操作 RabbitMQ

```shell
#进入容器
docker exec -it rabbitmq /bin/bash
#开启管理界面的授权
rabbitmq-plugins enable rabbitmq_management
```

5.主机登录验证

```
192.168.91.101:15672
```

6.参数说明

| 命令                                            | 描述                                                                     |
| ----------------------------------------------- | ------------------------------------------------------------------------ |
| -d                                              | 后台运行                                                                 |
| --name=rabbitmq                                 | 容器命名                                                                 |
| -p 15672:15672                                  | 将 RabbitMQ 的管理界面端口 15672 映射到主机                              |
| -p 5672:5672                                    | 将 RabbitMQ 的 AMQP 端口 5672 映射到主机端口                             |
| --hostname mq1                                  | 设置容器的主机名为 "mq1"                                                 |
| --restart=unless-stopped                        | 容器在退出时自动重新启动，除非手动停止容器。                             |
| -v /usr/mydata/rabbitmq/logs:/var/logs/rabbitmq | 将宿主机上 RabbitMQ 的日志目录映射到容器内的 `/var/logs/rabbitmq` 目录。 |
| -v /usr/mydata/rabbitmq/data:/var/lib/rabbitmq  | 将宿主机上 RabbitMQ 的数据目录映射到容器内的 `/var/lib/rabbitmq` 目录    |
| -e RABBITMQ_DEFAULT_USER=mredust                | 设置 RabbitMQ 用户名为 "mredust"。                                       |
| -e RABBITMQ_DEFAULT_PASS=密码                   | 设置 RabbitMQ 密码为 "密码"。                                            |
| rabbitmq:3.8.34                                 | rabbitmq 镜像                                                            |

## 7.部署 Elasticsearch⭐

1.拉取 elasticsearch 镜像

```
docker pull elasticsearch:7.12.1
```

2.因为需要部署 kibana 容器，因此需要让 es 和 kibana 容器互联。

```shell
docker network create es-net
```

3.创建容器

```shell
docker run -d \
--restart=unless-stopped \
--name=elasticsearch \
-e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
-e "discovery.type=single-node" \
-v /usr/mydata/elasticsearch/data:/var/lib/elasticsearch/data \
-v /usr/mydata/elasticsearch/plugins:/var/share/elasticsearch/plugins \
-v /usr/mydata/elasticsearch/logs:/var/logs/elasticsearch \
--privileged=true \
--network es-net \
-p 9200:9200 \
-p 9300:9300 \
elasticsearch:7.12.1
```

4.主机登录验证

```
192.168.91.101:9200
```

5.参数说明

| 命令                                                                   | 描述                                                                                      |
| ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------- |
| -d                                                                     | 后台运行                                                                                  |
| --name=elasticsearch                                                   | 容器命名                                                                                  |
| --restart=unless-stopped                                               | 容器在退出时自动重新启动，除非手动停止容器                                                |
| -e "ES_JAVA_OPTS=-Xms512m -Xmx512m"                                    | 设置 Elasticsearch 的 Java 虚拟机参数，这里将初始堆大小和最大堆大小都设置为 512MB         |
| -e "discovery.type=single-node"                                        | 设置 Elasticsearch 的发现类型为单节点，用于单节点部署                                     |
| -v /usr/mydata/elasticsearch/data:/var/lib/elasticsearch/data          | 将宿主机上 Elasticsearch 的数据目录映射到容器内的/var/lib/elasticsearch/data 目录。       |
| -v /usr/mydata/elasticsearch/plugins :/var/share/elasticsearch/plugins | 将宿主机上 Elasticsearch 的插件目录映射到容器内的 /var/share/elasticsearch/plugins 目录。 |
| -v /usr/mydata/elasticsearch/logs:/var/logs/elasticsearc               | 将宿主机上 Elasticsearch 的日志目录映射到容器内的/var/logs/elasticsearch 目录。           |
| --privileged=true                                                      | 容器授予特权访问，用于获取宿主机的硬件信息。                                              |
| --network es-net                                                       | 容器连接到 es-net 网络。                                                                  |
| -p 9200:9200                                                           | 将 Elasticsearch 的 HTTP REST API 端口 9200 映射到主机的相应端口。                        |
| -p 9300:9300                                                           | 将 Elasticsearch 的节点间通信端口 9300 映射到主机的相应端口。                             |
| elasticsearch:7.12.1                                                   | elasticsearch 镜像                                                                        |

## 8.部署 kibana⭐

1.拉取 kibana 镜像

```shell
docker pull kibana:7.12.1
```

2.创建容器

```shell
docker run -d \
--name=kibana \
--restart=unless-stopped \
-e ELASTICSEARCH_HOSTS=http://elasticsearch:9200 \
--network=es-net \
-v /usr/mydata/kibana/config:/etc/kibana/config \
-v /usr/mydata/kibana/logs:/var/logs/kibana \
-p 5601:5601  \
kibana:7.12.1
```

3.主机登录可视化界面

```shell
http://192.168.91.101:5601
```

4.参数说明

| 命令                                             | 描述                                                                |
| ------------------------------------------------ | ------------------------------------------------------------------- |
| -d                                               | 后台运行                                                            |
| --name=kiban                                     | 容器命名                                                            |
| --restart=unless-stopped                         | 容器在退出时自动重新启动，除非手动停止容器。                        |
| -e ELASTICSEARCH_HOSTS=http://elasticsearch:9200 | 设置 Kibana 连接的 Elasticsearch 主机地址                           |
| --network=es-net                                 | 将容器连接到 es-net 网络。                                          |
| -v /usr/mydata/kibana/config:/etc/kibana/config  | 将宿主机上 Kibana 的配置目录映射到容器内的/etc/kibana/config 目录。 |
| -v /usr/mydata/kibana/logs:/var/logs/kibana      | 将宿主机上 Kibana 的日志目录映射到容器内的/var/logs/kibana 目录。   |
| -p 5601:5601                                     | 将 Kibana 的默认端口 5601 映射到主机的相应端口                      |
| kibana:7.12.1                                    | kibana 镜像                                                         |

### 安装 IK 分词器 ⭐

4.1.**方式一：**在线安装 ik 插件

```shell
# 进入容器内部
docker exec -it elasticsearch /bin/bash
# 在线下载并安装
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.12.1/elasticsearch-analysis-ik-7.12.1.zip
#退出
exit
#重启容器
docker restart elasticsearch
```

4.2.**方式二：**离线安装 ik 插件

4.2.1.安装插件需要知道 elasticsearch 的 plugins 目录位置，而我们用了数据卷挂载，因此需要查看 elasticsearch 的数据卷目录，通过下面命令查看:

```shell
docker volume inspect plugins
```

4.2.2.上传 ik 分词器到容器的数据卷中

4.2.3.重启容器

```shell
docker restart elasticsearch
```

## 9.部署 xxl-job-admin⭐

1.拉取 xuxueli/xxl-job-admin 镜像

```shell
docker pull xuxueli/xxl-job-admin
```

2.创建数据库

```sql
 CREATE database if NOT EXISTS `xxl_job` default character set utf8mb4 collate utf8mb4_unicode_ci;
use `xxl_job`;

SET NAMES utf8mb4;

CREATE TABLE `xxl_job_info` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `job_group` int(11) NOT NULL COMMENT '执行器主键ID',
  `job_desc` varchar(255) NOT NULL,
  `add_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  `author` varchar(64) DEFAULT NULL COMMENT '作者',
  `alarm_email` varchar(255) DEFAULT NULL COMMENT '报警邮件',
  `schedule_type` varchar(50) NOT NULL DEFAULT 'NONE' COMMENT '调度类型',
  `schedule_conf` varchar(128) DEFAULT NULL COMMENT '调度配置，值含义取决于调度类型',
  `misfire_strategy` varchar(50) NOT NULL DEFAULT 'DO_NOTHING' COMMENT '调度过期策略',
  `executor_route_strategy` varchar(50) DEFAULT NULL COMMENT '执行器路由策略',
  `executor_handler` varchar(255) DEFAULT NULL COMMENT '执行器任务handler',
  `executor_param` varchar(512) DEFAULT NULL COMMENT '执行器任务参数',
  `executor_block_strategy` varchar(50) DEFAULT NULL COMMENT '阻塞处理策略',
  `executor_timeout` int(11) NOT NULL DEFAULT '0' COMMENT '任务执行超时时间，单位秒',
  `executor_fail_retry_count` int(11) NOT NULL DEFAULT '0' COMMENT '失败重试次数',
  `glue_type` varchar(50) NOT NULL COMMENT 'GLUE类型',
  `glue_source` mediumtext COMMENT 'GLUE源代码',
  `glue_remark` varchar(128) DEFAULT NULL COMMENT 'GLUE备注',
  `glue_updatetime` datetime DEFAULT NULL COMMENT 'GLUE更新时间',
  `child_jobid` varchar(255) DEFAULT NULL COMMENT '子任务ID，多个逗号分隔',
  `trigger_status` tinyint(4) NOT NULL DEFAULT '0' COMMENT '调度状态：0-停止，1-运行',
  `trigger_last_time` bigint(13) NOT NULL DEFAULT '0' COMMENT '上次调度时间',
  `trigger_next_time` bigint(13) NOT NULL DEFAULT '0' COMMENT '下次调度时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `xxl_job_log` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `job_group` int(11) NOT NULL COMMENT '执行器主键ID',
  `job_id` int(11) NOT NULL COMMENT '任务，主键ID',
  `executor_address` varchar(255) DEFAULT NULL COMMENT '执行器地址，本次执行的地址',
  `executor_handler` varchar(255) DEFAULT NULL COMMENT '执行器任务handler',
  `executor_param` varchar(512) DEFAULT NULL COMMENT '执行器任务参数',
  `executor_sharding_param` varchar(20) DEFAULT NULL COMMENT '执行器任务分片参数，格式如 1/2',
  `executor_fail_retry_count` int(11) NOT NULL DEFAULT '0' COMMENT '失败重试次数',
  `trigger_time` datetime DEFAULT NULL COMMENT '调度-时间',
  `trigger_code` int(11) NOT NULL COMMENT '调度-结果',
  `trigger_msg` text COMMENT '调度-日志',
  `handle_time` datetime DEFAULT NULL COMMENT '执行-时间',
  `handle_code` int(11) NOT NULL COMMENT '执行-状态',
  `handle_msg` text COMMENT '执行-日志',
  `alarm_status` tinyint(4) NOT NULL DEFAULT '0' COMMENT '告警状态：0-默认、1-无需告警、2-告警成功、3-告警失败',
  PRIMARY KEY (`id`),
  KEY `I_trigger_time` (`trigger_time`),
  KEY `I_handle_code` (`handle_code`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `xxl_job_log_report` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `trigger_day` datetime DEFAULT NULL COMMENT '调度-时间',
  `running_count` int(11) NOT NULL DEFAULT '0' COMMENT '运行中-日志数量',
  `suc_count` int(11) NOT NULL DEFAULT '0' COMMENT '执行成功-日志数量',
  `fail_count` int(11) NOT NULL DEFAULT '0' COMMENT '执行失败-日志数量',
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `i_trigger_day` (`trigger_day`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `xxl_job_logglue` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `job_id` int(11) NOT NULL COMMENT '任务，主键ID',
  `glue_type` varchar(50) DEFAULT NULL COMMENT 'GLUE类型',
  `glue_source` mediumtext COMMENT 'GLUE源代码',
  `glue_remark` varchar(128) NOT NULL COMMENT 'GLUE备注',
  `add_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `xxl_job_registry` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `registry_group` varchar(50) NOT NULL,
  `registry_key` varchar(255) NOT NULL,
  `registry_value` varchar(255) NOT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `i_g_k_v` (`registry_group`,`registry_key`,`registry_value`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `xxl_job_group` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `app_name` varchar(64) NOT NULL COMMENT '执行器AppName',
  `title` varchar(12) NOT NULL COMMENT '执行器名称',
  `address_type` tinyint(4) NOT NULL DEFAULT '0' COMMENT '执行器地址类型：0=自动注册、1=手动录入',
  `address_list` text COMMENT '执行器地址列表，多地址逗号分隔',
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `xxl_job_user` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL COMMENT '账号',
  `password` varchar(50) NOT NULL COMMENT '密码',
  `role` tinyint(4) NOT NULL COMMENT '角色：0-普通用户、1-管理员',
  `permission` varchar(255) DEFAULT NULL COMMENT '权限：执行器ID列表，多个逗号分割',
  PRIMARY KEY (`id`),
  UNIQUE KEY `i_username` (`username`) USING BTREE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

CREATE TABLE `xxl_job_lock` (
  `lock_name` varchar(50) NOT NULL COMMENT '锁名称',
  PRIMARY KEY (`lock_name`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

INSERT INTO `xxl_job_group`(`id`, `app_name`, `title`, `address_type`, `address_list`, `update_time`) VALUES (1, 'xxl-job-executor-sample', '示例执行器', 0, NULL, '2018-11-03 22:21:31' );
INSERT INTO `xxl_job_info`(`id`, `job_group`, `job_desc`, `add_time`, `update_time`, `author`, `alarm_email`, `schedule_type`, `schedule_conf`, `misfire_strategy`, `executor_route_strategy`, `executor_handler`, `executor_param`, `executor_block_strategy`, `executor_timeout`, `executor_fail_retry_count`, `glue_type`, `glue_source`, `glue_remark`, `glue_updatetime`, `child_jobid`) VALUES (1, 1, '测试任务1', '2018-11-03 22:21:31', '2018-11-03 22:21:31', 'XXL', '', 'CRON', '0 0 0 * * ? *', 'DO_NOTHING', 'FIRST', 'demoJobHandler', '', 'SERIAL_EXECUTION', 0, 0, 'BEAN', '', 'GLUE代码初始化', '2018-11-03 22:21:31', '');
INSERT INTO `xxl_job_user`(`id`, `username`, `password`, `role`, `permission`) VALUES (1, 'admin', 'e10adc3949ba59abbe56e057f20f883e', 1, NULL);
INSERT INTO `xxl_job_lock` ( `lock_name`) VALUES ( 'schedule_lock');

commit;

```

3.创建容器

```shell
docker run -d \
--name xxl-job-admin \
--restart=unless-stopped \
-p 7080:8080 \
-e PARAMS="--spring.datasource.url=jdbc:mysql://192.168.91.101:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=root --spring.datasource.password=密码" \
-v /usr/mydata/xxl-job-admin/logs:/data/applogs \
-v /usr/mydata/xxl-job-admin/conf/:/data/xxl-job-admin/conf \
--privileged=true \
--link mysql:mysql \
xuxueli/xxl-job-admin:2.3.1
```

3.登录主机

```
192.168.91.101:7080/xxl-job-admin
```

4.参数说明

| 命令                                                                                                                                                                                                                                      | 描述                                                                                                                         |
| :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| -d                                                                                                                                                                                                                                        | 后台运行                                                                                                                     |
| --name=xxl-job-admin                                                                                                                                                                                                                      | 容器命名                                                                                                                     |
| --restart=unless-stopped                                                                                                                                                                                                                  | 容器在退出时自动重新启动，除非手动停止容器                                                                                   |
| -p 7080:8080                                                                                                                                                                                                                              | 将容器端口 8080 映射到主机端口 7080                                                                                          |
| -e PARAMS=" --spring.datasource.url=jdbc:mysql://192.168.91.101:3306/xxl_job?useUnicode=true&characterEncoding=UTF-8&autoReconnect=true&serverTimezone=Asia/Shanghai --spring.datasource.username=root --spring.datasource.password=密码" | 设置环境变量 PARAMS，用于传递一些参数给容器内的 xxl-job-admin 服务。                                                         |
| -v /usr/mydata/xxl-job-admin/logs:/data/applogs                                                                                                                                                                                           | 将主机的 /usr/mydata/xxl-job-admin/logs 目录挂载到容器内的/data/applogs 目录，用于存储 xxl-job-admin 的日志文件。            |
| -v /usr/mydata/xxl-job-admin/conf/:/data/xxl-job-admin/conf                                                                                                                                                                               | 将主机的 /usr/mydata/xxl-job-admin/conf/目录挂载到容器内的/data/xxl-job-admin/conf 目录，用于存储 xxl-job-admin 的配置文件。 |
| --privileged=true                                                                                                                                                                                                                         | 容器授予特权访问，用于获取宿主机的硬件信息。                                                                                 |
| --link mysql:mysql                                                                                                                                                                                                                        | 容器与名为 "mysql" 的另一个容器连接起来，以便可以访问容器内的 MySQL 数据库。                                                 |
| xuxueli/xxl-job-admin:2.3.1                                                                                                                                                                                                               | xuxueli/xxl-job-admin 镜像                                                                                                   |

## 10.部署 minio⭐

1.拉取 minio 镜像

```shell
docker pull minio/minio
```

2.创建容器

```shell
docker run -d \
--name minio \
--restart=unless-stopped \
-p 9000:9000 \
-p 9090:9090 \
--net=host \
-e "MINIO_ACCESS_KEY=adminmredust" \
-e "MINIO_SECRET_KEY=adminmredust" \
-v /usr/mydata/minio/data:/data \
-v /usr/mydata/minio/conf:/etc/.minio \
minio/minio server /data --console-address "0.0.0.0:9090" -address ":9000"
```

3.访问主机

```sh
192.168.91.101:9090
```

4.参数说明

| 命名                                                                       | 描述                                                                                                                      |
| -------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| -d                                                                         | 后台运行                                                                                                                  |
| --name minio                                                               | 容器命名                                                                                                                  |
| --restart=unless-stopped                                                   | 设置容器在非手动停止的情况下会自动重启。                                                                                  |
| -p 9000:9000                                                               | 将主机的 9000 端口映射到容器的 9000 端口，用于访问 MinIO 对象存储服务。                                                   |
| -p 9090:9090                                                               | 将主机的 9090 端口映射到容器的 9090 端口，用于访问 MinIO 的 Web 管理界面。                                                |
| --net=host                                                                 | 使用主机网络模式将容器将与主机共享网络                                                                                    |
| -e "MINIO_ACCESS_KEY=adminmredust"                                         | 设置 MinIO 的访问密钥为 "adminmredust"。                                                                                  |
| -e "MINIO_SECRET_KEY=adminmredust"                                         | 设置 MinIO 的秘钥为 "adminmredust"。                                                                                      |
| -v /usr/mydata/minio/data:/data                                            | 将主机的 /usr/mydata/minio/data 目录挂载到容器内的/data 目录，用于存储 MinIO 的数据文件。                                 |
| -v /usr/mydata/minio/conf:/etc/.minio                                      | 将主机的 /usr/mydata/minio/conf 目录挂载到容器内的 /etc/.minio 目录，用于存储 MinIO 的配置文件。                          |
| minio/minio server /data --console-address "0.0.0.0:9090" -address ":9000" | 使用 minio/minio 镜像运行 MinIO 服务，数据目录为 /data，Web 管理界面监听地址为 0.0.0.0:9090，MinIO 服务监听地址为 :9000。 |

## 11.部署 gogs
