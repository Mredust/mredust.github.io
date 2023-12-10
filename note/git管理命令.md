# Git命令指南

## 1.初始化目录

```shell
git init
```



## 2.添加文件

```shell
#目标文件/文件夹
git add test.txt/Test
#当前目录的所有文件
git add .
```



## 3.提交文件

```sh
git commit -m '文件描述信息' 
```



## 4.拉取远程仓库

```shell
git remote add origin #https地址

git remote remove远程仓库别名 # 删除远程仓库地址
```



## 5.推送、合并

推送不成功则合并一下

```bash
git pull origin master --allow-unrelated-histories
```

推送

```bash
git push -u origin master
```



## 6.克隆仓库

```shell
git clone https://gitee.com/mredust/network-disk-resources.git
```







# 使用

```shell
git reset版本号 # 切换版本代码到暂存区和工作区
git branch分支名 #创建分支
git branch #查看本地分支
git branch -d分支名 # 删除分支
git checkout分支名 # 切换分支
git checkout -b分支名 # 创建并立刻切换分支
git merge分支名 #把分支提交历史记录合并到当前所在分支
```







# 配置gitee和github仓库

## 1.解除之前配置

查看之前是否配置过用户名和邮箱

```bash
git config --global --list
```

解除绑定

```bash
git config --global --unset user.name "Mredust" #你的名字
git config --global --unset user.email "3130383647@qq.com" #你的邮箱
```

重新绑定

```bash
git config --global user.name "Mredust"                      
git config --global user.email "3130383647@qq.com"
```



## 2.生成公钥

```bash
ssh-keygen -t rsa -C '3130383647@qq.com' -f ~/.ssh/id_rsa_github
ssh-keygen -t rsa -C '3130383647@qq.com' -f ~/.ssh/id_rsa_gitee
```

> 生成文件默认在C:\Users\用户名\ .ssh



## 3 识别 SSH keys 新的私钥

默认只读取 id_rsa，为了让 SSH 识别新的私钥，需要将新的私钥加入到 SSH agent 中。这一步也需要再探索一下，我没有设置也可以成功。

```bash
ssh-agent bash
ssh-add ~/.ssh/id_rsa.github
ssh-add ~/.ssh/id_rsa.gitee
```



## 4.生成配置文件

主要是告诉Git我在访问这两个网站时，你要知道拿着哪个私钥去验证。
在.ssh目录下，生成config文件（文件使用Windows自带的文本编辑器编辑即可）

```bash
# gitee
Host gitee.com
HostName gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_gitee
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_github
```



## 5.添加公钥

文件后缀为.pub,分别添加SSH到Gitee和Github：



## 6.测试连接

```bash
ssh -T git@gitee.com
ssh -T git@github.com
```

出现 You've successful authenticated 即为成功。





