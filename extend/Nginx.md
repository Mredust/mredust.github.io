# 安装Nginx

联网配置

```shell
yum install -y gcc
yum install zlib zlib-devel
yum install pcre pcre-devel
make 
make install
```



# 启动Nginx

进入nginx/sbin的目录

```
./nginx 启动
./nginx -s stop 快速停止
./nginx -s quit 关闭
./nginx -s reload 重新加载配置
```

# 安装成系统服务

创建服务脚本

```shell
vim /usr/lib/systemd/system/nginx.service
```

脚本内容

```
[Unit]
Description=nginx - web server
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
PIDFile=/usr/local/nginx/logs/nginx.pid
ExecstartPre=/usr/local/nginx/sbin/nginx -t -c
/usr/local/nginx/conf/nginx.conf
Execstart=/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
ExecReload=/usr/local/nginx/sbin/nginx -s reload
Execstop=/usr/local/nginx/sbin/nginx -s stop
ExecQuit=/usr/local/nginx/sbin/nginx -s quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target

```

重新加载系统服务

```
systemctl daemon-reload
```

启动服务

```
systemctl start nginx.service
```





# 开机启动

```
systemctl enable firewalld.service
```

