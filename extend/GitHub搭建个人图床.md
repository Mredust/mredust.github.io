# 利用 Github 或 Gitee 搭建自己的免费图床



# 1. 介绍

图床这个东西大家一定不会陌生。

对于不清楚这个东西的朋友，大概说一下图床是个啥东西。

图床，其实可以就相当于我们手机上的相册，不过它是在线的，而且是对大家开放的，大家都可以访问查看，但是编辑删除这些功能仅限

于拥有者，就相当于云分享的公开照片，你可以查看，也可以下载下来编辑，但是拥有权还是属于分享者。

那你可能会疑惑，我们需要这个吗，答案很需要。

你想想，我们写博客，网站之类的是不是有很多图片需要插入，总是为路径烦恼 `../../xxx.png`,而且也不好维护。

于是图床就发挥了作用，有了图床，我们只需要上传图片到平台后就能够任意复制到其他平台，不用担心图片丢失问题了。

今天的文章就是给大家分享一个搭建免费图床的教程，既是方便自己，也希望对大家也有所帮助。



# 2. 准备工作

那么在正式开始之前，你需要提前准备以下东西：

> Github 账号 | Gitee账号
>
> PicGO软件



# 3. 安装PicGo软件

## 3.1.下载地址

 [官网](https://link.zhihu.com/?target=https%3A//molunerfinn.com/PicGo/)

|    下载源    |                          下载地址                           |
| :----------: | :---------------------------------------------------------: |
|    Github    |        https://github.com/Molunerfinn/PicGo/releases        |
|  腾讯云COS   |        https://github.com/Molunerfinn/PicGo/releases        |
| 山东大学镜像 | https://mirrors.sdu.edu.cn/github-release/Molunerfinn_PicGo |



## 3.2.安装软件

本人使用是Windows版本：	PicGo-Setup-2.3.0-x64.exe （根据自己的需求下载对应的版本）



## 3.3.界面样式

![image-20230924233503477](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230924233503477.png)



# 4.GitHub搭建图床

## 4.1.创建仓库

![image-20230924234319914](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230924234319914.png)





## 4.2.创建Token

以此打开 `Settings -> Developer settings -> Personal access tokens`，最后点击 `generate new token`；



![image-20230924234517567](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230924234517567.png)



![image-20230924234753859](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230924234753859.png)



![image-20230924235205077](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230924235205077.png)



![image-20230924235548325](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230924235548325.png)



`token` 生成，注意它只会显示一次，所以你最好把它复制下来，方便下次使用，否则下次有需要重新新建.



## 4.3.配置 PicGo

打开 图床设置 --> Github 图床 填写相关信息，最后点击 确定即可，要将其作为默认图床的话，点击设为默认图床；

![img](https://img-blog.csdnimg.cn/1d7ccba31f264a61868f2706cfbbebf5.png)

 上传图片，通过上传区后默认复制图片链接





# 5.Gitee搭建图床

## 5.1.创建仓库

![image-20230925000835017](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230925000835017.png)





## 5.2.设置开源

![image-20230925001237463](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230925001237463.png)

## 5.3.创建Token

以此打开 `Settings -> Developer settings -> Personal access tokens`，最后点击 `generate new token`；

![image-20230925001546006](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230925001546006.png)



![image-20230925001647556](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230925001647556.png)



`token` 生成，注意它只会显示一次，所以你最好把它复制下来，方便下次使用，否则下次有需要重新新建.



## 5.4.安装gitee插件

![image-20230925001847787](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230925001847787.png)

## 5.5.配置 PicGo

打开 图床设置 --> Github 图床 填写相关信息，最后点击 确定即可，要将其作为默认图床的话，点击设为默认图床.

![image-20230925001753793](C:\Users\Mredust\AppData\Roaming\Typora\typora-user-images\image-20230925001753793.png)

 上传图片，通过上传区后默认复制图片链接





# 6. 图床推荐

此外，推荐几个免费的图床给大家，大家可以根据自己的喜好进行选择；

1. [路过图床](https://link.zhihu.com/?target=https%3A//imgchr.com/)
2. [SM.MS](https://link.zhihu.com/?target=https%3A//sm.ms/)
3. [Imgur](https://link.zhihu.com/?target=https%3A//imgur.com/)

