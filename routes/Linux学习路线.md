## 路线

### Linux 基础知识

- 发展历史
- ⭐ 特点和优势
- 应用场景
- ⭐ 常见 Linux 系统版本（推荐 CentOS 7+）
  - ⭐ CentOS
  - ⭐ Ubuntu
  - Debian
  - Fedora
- 何为开源？

### Linux 环境

- 搭建方式
  - ⭐ 虚拟机
  - ⭐ 云服务器
  - 在线工具
  - WSL
  - Docker 容器
- 远程连接
  - ⭐ SSH
  - 连接工具
    - ⭐ XShell
    - ⭐ MobaXterm
    - SecureCRT
    - Putty

### Linux 常用命令

> 此处只列举命令名称，命令的具体用法可直接在手册中（[https://www.linuxcool.com/）查询](https://gitee.com/link?target=https%3A%2F%2Fwww.linuxcool.com%2F%EF%BC%89%E6%9F%A5%E8%AF%A2)

#### 系统信息

- uname 查看系统信息
- hostname 查看主机名
- cat /proc/cpuinfo 查看 CPU 信息
- lsmod 查看已加载的系统模块
- top 查看系统使用情况
- df 查看磁盘使用情况
- fdisk 查看磁盘分区
- du 查看目录使用情况
- iostat 查看 I / O 使用情况
- free 显示系统内存情况
- env 查看环境变量
- uptime 查看系统运行时间、用户数、负载

#### 系统操作

- shutdown 关机
- reboot 重启
- mount 挂载设备
- umount 卸载设备

#### 用户相关

- su 切换用户
- sudo 以管理员身份执行
- who 查看当前用户名
- ssh 远程连接
- logout 注销
- useradd 创建用户
- userdel 删除用户
- usermod 修改用户
- groupadd 创建用户组
- groupdel 删除用户组
- groupmod 修改用户组
- passwd 修改密码
- last 显示用户或终端的登录情况

#### 文件相关

- cd 切换目录
- ls 查看目录列表
- tree 打印目录树
- mkdir 创建目录
- rm 删除目录
- touch 新建文件
- cp 复制文件
- mv 移动文件
- ln 创建文件链接
- find 搜索文件
- locate 定位文件
- whereis 查看可执行文件路径
- which 在 PATH 指定的路径中，搜索某系统命令的位置
- chmod 设置目录权限
- cat / more / less 查看文件
- tac 倒序查看文件
- head / tail 查看文件开头 / 结尾
- paste 合并文件
- zip / tar / gzip 压缩文件
- unzip / tar / gunzip 解压文件
- grep / sed / awk 文本处理
- vim 文本编辑

#### 程序相关

- crontab 计划任务
- nohup 后台运行程序
- jobs 查看系统任务
- ps 查看进程
- kill 杀死进程
- rpm / yum / apt / apt-get / dpkg 软件包管理
- service / systemctl 服务管理

#### 网络相关

- ifconfig 查看网络属性
- netstat 查看网络状态
- iptables 查看 iptables 规则

#### 其他

- date 显示系统时间
- cal 显示日历
- history 显示与操作历史
- help 帮助
- alias 别名

### 用户管理

- 用户
- 用户组
- ACL 权限管理
- 用户切换
- 管理员

### 文件管理

- 文件操作
  - 创建
  - 修改
  - 复制
  - 移动
  - 删除
- 文件浏览
- 文件搜索
- 文件权限
- 软硬链接
- 压缩 / 解压

### 文本操作

- 正则表达式
- grep
- sed
- awk

### VIM 编辑器

- 基本操作
- 模式
- 快捷键
- VIM 定制
- 插件增强

送张 VIM 键盘图：

![img](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAADICAAAAACIM/FCAAACh0lEQVR4Ae3ch5W0OgyG4dt/mQJ2xgQPzJoM1m3AbALrxzrf28FzsoP0HykJEEAAAUQTBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEkKK0789+GK/I2ezfQB522PnS1qc8pGgXvr4tE4aY0XOUWlGImThWgyCk6DleixzE7qwBkg/MGiDPlVVAyp1VQGrPKiACDhFI6VkF5LmzCki+sg7IwDoglnVAil0IMkeG9CyUiwsxLFUVFzJJOQaKCjFCDN9RXMjIX7W6ztZXZDKKCyn8sWJvH+nca7WHDN9lROlAliPH9iRKCPI4cswFJQWxB46toLQgQ9jhn5QYZA9DOkoMUoQde5YapAxDWkoNYsOQR3KQd9CxUnIQF4S49CB9ENKlBxmDEKsFUgMCCCCAAHIrSF61f6153Ajy8nyiPr8L5MXnmm4CyT2fzN4DUvHZ+ntA2tOQBRBAAAEEEEAAAQQQ7ZBaC6TwSiDUaYHQ2yuB0MN+ft+43whyrs4rgVCjBUKTFshLC6TUAjGA3AxSaYFYLZBOC2RUAsk8h5qTg9QcbEoOsoQhQ2qQhsO5xCD5dgB5JQaZ+KBKGtKecvR81Ic0ZDjByKdDx0rSEDZ/djQbH+bkIdvfJFm98BfV8hD2zprfVdlu9PxVeyYAkciREohRAplJCaRSAplJCcQogTjSAdlyHRBvSAekJR0QRzogA+mADJkOiCPSAPEtqYBshlRAXC43hxix2QiOuEZkVERykGyNo9idIZKE0HO7XrG6OiMShlDWjstVzdPgXtUH9v0CEidAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQP4HgjZxTpdEii0AAAAASUVORK5CYII=)

### 磁盘管理

- 使用情况查询
- 磁盘分区
- 挂载

### 驱动管理

- 驱动加载
- 驱动更新
- 网卡
- 显卡

### 进程管理

- 启动进程
- 杀死进程
- 查看进程
- 前台 / 后台任务
- 进程监控

### 计划任务

- crond 服务
- crontab 命令

### 网络管理

- IP
- 端口
- 主机名
- hosts
- 网络配置
- 网络状态
- 网络监控

### 系统管理

- 系统设置
  - 日期时间
  - 语言
  - 字符集
- 系统服务
- 环境变量
- 日志
- 系统关机 / 重启
- 数据备份与恢复

### 服务管理

- 服务查看
- 启动服务
- 禁用服务
- 删除服务
- 开机自启

### 软件管理

- 软件包管理器
  - ⭐ rpm
  - ⭐ yum
  - apt
  - apt-get
  - dpkg
- 软件安装
- 软件更新
- 软件卸载
- 源码安装

### 常用软件 / 服务搭建

- HTTP
- Mail
- NFS
- DNS
- FTP
- mysql
- LVS + Keepalived
- Apache
- Nginx
- Redis
- 日志服务

### Shell 脚本编程

- 默认变量
- 运算符
- 条件
- 循环
- 执行
- 函数
  - 系统函数
  - 自定义函数
- 规范
- 调试方法
- 管道
- I/O 重定向

### Linux 启动过程

- BIOS 启动引导
- 引导加载程序
- 内核加载
- 系统初始化（init）
- 运行级别
- 启动内核
- 执行初始化脚本
- 用户登录

### Linux 内核

- 内核的组成
- 目录结构
- 版本
- 模块
- 编译
- 裁剪

具体路线图参考：

![内核知识体系 by 0Voice](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAMgAAADICAAAAACIM/FCAAACh0lEQVR4Ae3ch5W0OgyG4dt/mQJ2xgQPzJoM1m3AbALrxzrf28FzsoP0HykJEEAAAUQTBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEkKK0789+GK/I2ezfQB522PnS1qc8pGgXvr4tE4aY0XOUWlGImThWgyCk6DleixzE7qwBkg/MGiDPlVVAyp1VQGrPKiACDhFI6VkF5LmzCki+sg7IwDoglnVAil0IMkeG9CyUiwsxLFUVFzJJOQaKCjFCDN9RXMjIX7W6ztZXZDKKCyn8sWJvH+nca7WHDN9lROlAliPH9iRKCPI4cswFJQWxB46toLQgQ9jhn5QYZA9DOkoMUoQde5YapAxDWkoNYsOQR3KQd9CxUnIQF4S49CB9ENKlBxmDEKsFUgMCCCCAAHIrSF61f6153Ajy8nyiPr8L5MXnmm4CyT2fzN4DUvHZ+ntA2tOQBRBAAAEEEEAAAQQQ7ZBaC6TwSiDUaYHQ2yuB0MN+ft+43whyrs4rgVCjBUKTFshLC6TUAjGA3AxSaYFYLZBOC2RUAsk8h5qTg9QcbEoOsoQhQ2qQhsO5xCD5dgB5JQaZ+KBKGtKecvR81Ic0ZDjByKdDx0rSEDZ/djQbH+bkIdvfJFm98BfV8hD2zprfVdlu9PxVeyYAkciREohRAplJCaRSAplJCcQogTjSAdlyHRBvSAekJR0QRzogA+mADJkOiCPSAPEtqYBshlRAXC43hxix2QiOuEZkVERykGyNo9idIZKE0HO7XrG6OiMShlDWjstVzdPgXtUH9v0CEidAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQAABBBBAAAEEEEAAAQQQQP4HgjZxTpdEii0AAAAASUVORK5CYII=)

### 第三方工具

- Ansible
- Webmin
- 宝塔 Linux

## 岗位

- 后端开发（Java / Go / C++）
- 底层开发（C / C++）
- 运维开发
- 大数据
- 云计算
- 自动化运维
- 嵌入式开发
- 网络工程师

## 学习建议

多动手实践，建议自己购买一台云服务器，并且在本地搭建 Linux 虚拟机环境。

一定要自己从 0 开始手敲命令安装软件、部署服务，熟悉整个项目的上线流程。

每个命令至少要跟着敲一遍，了解它们的作用，并通过自然地练习，熟悉常用的 Linux 命令。

记不住没关系，用文档查就行了。

先会用，再理解。

时间不多的话，可以通过面试题来了解一些 Linux 设计思想，而不是直接去深入学习内核，虽说学会了的确大有裨益，但性价比不高。

## 资源

- 视频
  - ⭐ 2021 韩顺平 一周学会 Linux：[https://www.bilibili.com/video/BV1Sv411r7vd（基于](https://gitee.com/link?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1Sv411r7vd%EF%BC%88%E5%9F%BA%E4%BA%8E) CentOS 7.6 版本较新，视频长度刚刚好，也比较完整）
  - 【千锋】Linux 云计算基础视频教程 650 集入门：[https://www.bilibili.com/video/BV1pz4y1D73n（很全面，适合时间足够、想认真学的同学）](https://gitee.com/link?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV1pz4y1D73n%EF%BC%88%E5%BE%88%E5%85%A8%E9%9D%A2%EF%BC%8C%E9%80%82%E5%90%88%E6%97%B6%E9%97%B4%E8%B6%B3%E5%A4%9F%E3%80%81%E6%83%B3%E8%AE%A4%E7%9C%9F%E5%AD%A6%E7%9A%84%E5%90%8C%E5%AD%A6%EF%BC%89)
  - 【狂神说 Java】Linux 教程 - 阿里云真实环境学习：[https://www.bilibili.com/video/BV187411y7hF（算是个小的入门教程吧，时间足够的话还是推荐看更完整的）](https://gitee.com/link?target=https%3A%2F%2Fwww.bilibili.com%2Fvideo%2FBV187411y7hF%EF%BC%88%E7%AE%97%E6%98%AF%E4%B8%AA%E5%B0%8F%E7%9A%84%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8B%E5%90%A7%EF%BC%8C%E6%97%B6%E9%97%B4%E8%B6%B3%E5%A4%9F%E7%9A%84%E8%AF%9D%E8%BF%98%E6%98%AF%E6%8E%A8%E8%8D%90%E7%9C%8B%E6%9B%B4%E5%AE%8C%E6%95%B4%E7%9A%84%EF%BC%89)
  - 细说 Linux - 从入门到精通：[https://study.163.com/course/courseMain.htm?courseId=983014（感觉有点啰嗦，作为备用吧）](https://gitee.com/link?target=https%3A%2F%2Fstudy.163.com%2Fcourse%2FcourseMain.htm%3FcourseId%3D983014%EF%BC%88%E6%84%9F%E8%A7%89%E6%9C%89%E7%82%B9%E5%95%B0%E5%97%A6%EF%BC%8C%E4%BD%9C%E4%B8%BA%E5%A4%87%E7%94%A8%E5%90%A7%EF%BC%89)
  - 玩转 Vim 从放弃到爱不释手：[https://www.imooc.com/learn/1129（好评很多）](https://gitee.com/link?target=https%3A%2F%2Fwww.imooc.com%2Flearn%2F1129%EF%BC%88%E5%A5%BD%E8%AF%84%E5%BE%88%E5%A4%9A%EF%BC%89)
  - 阿里云 Linux 运维学习路线：[https://edu.aliyun.com/roadmap/linux](https://gitee.com/link?target=https%3A%2F%2Fedu.aliyun.com%2Froadmap%2Flinux)
- 书籍
  - 《鸟哥的 Linux 私房菜 —— 基础篇》：[http://cn.linux.vbird.org/linux_basic/linux_basic.php（经典）](https://gitee.com/link?target=http%3A%2F%2Fcn.linux.vbird.org%2Flinux_basic%2Flinux_basic.php%EF%BC%88%E7%BB%8F%E5%85%B8%EF%BC%89)
  - 《深入理解 LINUX 内核》：[https://book.douban.com/subject/1767120/](https://gitee.com/link?target=https%3A%2F%2Fbook.douban.com%2Fsubject%2F1767120%2F)
  - 《深入 Linux 内核架构》：[https://book.douban.com/subject/4843567/](https://gitee.com/link?target=https%3A%2F%2Fbook.douban.com%2Fsubject%2F4843567%2F)
  - 《Linux 内核完全剖析》：[https://book.douban.com/subject/3229243/](https://gitee.com/link?target=https%3A%2F%2Fbook.douban.com%2Fsubject%2F3229243%2F)
  - 《Linux 内核设计与实现(原书第 3 版)》：[https://book.douban.com/subject/6097773/](https://gitee.com/link?target=https%3A%2F%2Fbook.douban.com%2Fsubject%2F6097773%2F)
- 文档
  - Linux 教程（菜鸟教程）：[https://www.runoob.com/linux/linux-tutorial.html](https://gitee.com/link?target=https%3A%2F%2Fwww.runoob.com%2Flinux%2Flinux-tutorial.html)
  - Linux 教程（W3CSchool）：[https://www.w3cschool.cn/linux/](https://gitee.com/link?target=https%3A%2F%2Fwww.w3cschool.cn%2Flinux%2F)
  - Linux 工具快速教程：[https://linuxtools-rst.readthedocs.io（基础、工具进阶、工具参考）](https://gitee.com/link?target=https%3A%2F%2Flinuxtools-rst.readthedocs.io%EF%BC%88%E5%9F%BA%E7%A1%80%E3%80%81%E5%B7%A5%E5%85%B7%E8%BF%9B%E9%98%B6%E3%80%81%E5%B7%A5%E5%85%B7%E5%8F%82%E8%80%83%EF%BC%89)
- 合集
  - Linux 内核学习资料：[https://github.com/0voice/linux_kernel_wiki](https://gitee.com/link?target=https%3A%2F%2Fgithub.com%2F0voice%2Flinux_kernel_wiki)
  - GitHub Linux 专区：[https://github.com/topics/linux（很多好项目）](https://gitee.com/link?target=https%3A%2F%2Fgithub.com%2Ftopics%2Flinux%EF%BC%88%E5%BE%88%E5%A4%9A%E5%A5%BD%E9%A1%B9%E7%9B%AE%EF%BC%89)
  - GitHub Linux 合集：[https://github.com/inputsh/awesome-linux（Linux](https://gitee.com/link?target=https%3A%2F%2Fgithub.com%2Finputsh%2Fawesome-linux%EF%BC%88Linux) 系列技术）
  - StackOverflow：[https://stackoverflow.com/questions/tagged/linux（解决问题必备）](https://gitee.com/link?target=https%3A%2F%2Fstackoverflow.com%2Fquestions%2Ftagged%2Flinux%EF%BC%88%E8%A7%A3%E5%86%B3%E9%97%AE%E9%A2%98%E5%BF%85%E5%A4%87%EF%BC%89)
  - 掘金 Linux 专区：[https://juejin.cn/tag/Linux（技术文章）](https://gitee.com/link?target=https%3A%2F%2Fjuejin.cn%2Ftag%2FLinux%EF%BC%88%E6%8A%80%E6%9C%AF%E6%96%87%E7%AB%A0%EF%BC%89)
- 实战
  - ⭐ 蓝桥云课 Linux 基础入门：[https://www.lanqiao.cn/courses/1（强烈推荐）](https://gitee.com/link?target=https%3A%2F%2Fwww.lanqiao.cn%2Fcourses%2F1%EF%BC%88%E5%BC%BA%E7%83%88%E6%8E%A8%E8%8D%90%EF%BC%89)
  - 腾讯云动手实验室：[https://cloud.tencent.com/developer/labs](https://gitee.com/link?target=https%3A%2F%2Fcloud.tencent.com%2Fdeveloper%2Flabs)
  - 阿里云体验实验室：[https://developer.aliyun.com/adc/labs/](https://gitee.com/link?target=https%3A%2F%2Fdeveloper.aliyun.com%2Fadc%2Flabs%2F)
  - 阿里云知行实验室：[https://start.aliyun.com/](https://gitee.com/link?target=https%3A%2F%2Fstart.aliyun.com%2F)
  - 华为云沙箱实验室：[https://lab.huaweicloud.com/](https://gitee.com/link?target=https%3A%2F%2Flab.huaweicloud.com%2F)
- 社区（国内倒的差不多了）
  - Linux 中国：[https://linux.cn/](https://gitee.com/link?target=https%3A%2F%2Flinux.cn%2F)
  - 开源中国：[https://www.oschina.net/（综合的开源社区）](https://gitee.com/link?target=https%3A%2F%2Fwww.oschina.net%2F%EF%BC%88%E7%BB%BC%E5%90%88%E7%9A%84%E5%BC%80%E6%BA%90%E7%A4%BE%E5%8C%BA%EF%BC%89)
  - 红帽官网：[https://www.redhat.com/zh](https://gitee.com/link?target=https%3A%2F%2Fwww.redhat.com%2Fzh)
- 工具
  - DistroTest 在线操作系统测试：[https://distrotest.net](https://gitee.com/link?target=https%3A%2F%2Fdistrotest.net)
  - ⭐ Linux 命令搜索：[https://wangchujiang.com/linux-command](https://gitee.com/link?target=https%3A%2F%2Fwangchujiang.com%2Flinux-command)
  - Linux 命令大全手册：[https://man.linuxde.net/](https://gitee.com/link?target=https%3A%2F%2Fman.linuxde.net%2F)
  - Linux 命令大全手册：[https://www.linuxcool.com/](https://gitee.com/link?target=https%3A%2F%2Fwww.linuxcool.com%2F)
  - Linux 命令示例：[http://linux-commands-examples.com/](https://gitee.com/link?target=http%3A%2F%2Flinux-commands-examples.com%2F)
  - 宝塔 Linux 面板：[https://www.bt.cn/](https://gitee.com/link?target=https%3A%2F%2Fwww.bt.cn%2F)
  - 在线 Shell 脚本检查：[https://www.shellcheck.net](https://gitee.com/link?target=https%3A%2F%2Fwww.shellcheck.net)
- 面试题
  - 牛客网 Linux 专项练习：[https://www.nowcoder.com/intelligentTest](https://gitee.com/link?target=https%3A%2F%2Fwww.nowcoder.com%2FintelligentTest)
  - 牛客网 Linux 面试题：[https://www.nowcoder.com/search?query=linux%E9%9D%A2%E8%AF%95%E9%A2%98&type=question](https://gitee.com/link?target=https%3A%2F%2Fwww.nowcoder.com%2Fsearch%3Fquery%3Dlinux%E9%9D%A2%E8%AF%95%E9%A2%98%26type%3Dquestion)
  - Linux 常见面试题整理：[https://zhuanlan.zhihu.com/p/376749877](https://gitee.com/link?target=https%3A%2F%2Fzhuanlan.zhihu.com%2Fp%2F376749877)
  - Linux 常见面试题整理：[https://github.com/0voice/linux_kernel_wiki#-%E9%9D%A2%E8%AF%95%E9%A2%98](https://gitee.com/link?target=https%3A%2F%2Fgithub.com%2F0voice%2Flinux_kernel_wiki%23-%E9%9D%A2%E8%AF%95%E9%A2%98)
