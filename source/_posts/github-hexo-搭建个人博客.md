---
title: github+hexo 搭建个人博客
date: 2017-02-12 13:05:29
tags: hexo Web
---
**本文针对的主要是Windows用户，不过总体步骤和Linux/Mac
大同小异。**
##环境配置##
 - 安装git bash：直接在[官网][1]下载最新版本并安装，并在[github][2]申请账号；
 - 生成新的ssh-key：
    在终端输入：
```
ssh-keygen -t rsa -C "your_email@example.com" #（在github注册的邮箱）
```
成功后终端显示如下：
```
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/xxxx/.ssh/id_rsa):
```
 - 添加key到ssh：
    将`C:/Users/xxxx/.ssh/github_rsa.pub`中的全文复制，添加到github。
（浏览器登录github，找到`settings -> SSH and GPG keys -> new SSH key`粘贴进去确认即可）
至此，你的github账户已经和你的电脑建立了关联。
（有关SSH的更详尽步骤可以参考[这里][3]）

 - 建立一个仓库来存博客的源码，假设标题为“blog”；
 - 建立一个github仓库，标题是```你的github用户名.github.io```；
 -  安装Node.js：直接在Node.js[官网][4]下载最新版本并安装；
 - 安装hexo（必须在安装Node.js之后进行）：在git bash中输入```npm install -g hexo ```；
 - 任意建一个名为“hexo”文件夹。
——以上就是搭建博客所需的所有环境。

##搭建博客##

**方便起见，以下均假设你的github用户名为XXX.**
 - 进入hexo文件夹，并在该文件夹下运行git bash（右键菜单中选择运行git bash）；
 - git bash中键入```hexo init```，hexo会自动将搭建所需的所有初始材料放入hexo文件夹中；
 - 运行```git init```，将该文件夹初始化为git仓库；
 - 运行```git remote add origin git@github.com:XXX/blog```，把你的本地仓库至连接你的远程仓库blog；
 - 修改hexo文件夹中的```_config.yml```文件：
```
deploy:
    type: git #用git来部署
# 以下两个"repo"任选一种即可
    repo: https://github.com/XXX/XXX.github.io.git
    repo: git@github.com:XXX/XXX.github.io.git
    branch: master
```
保存后即可将本地博客与```XXX.github.io```建立关联；
 - 建立关联之后在git bash中运行```hexo generate```；
 - generate完毕后即可运行```hexo server```，即可在```localhost:4000```浏览你的博客；
 - 本地浏览确认无误后运行```hexo deploy```即可将博客部署到```XXX.github.io```。
 
##后续操作##
###主题的选择###
hexo本身的主题样式比较单调，在网上有各种样式的主题：

 - litten：yilia，[一个简洁优雅的hexo主题.][5]
 - forsigner：fexo，[一个极简主义风格的hexo主题.][6]
 - iissnan：next，[An elegant theme for Hexo.][7]
 - MOxFIVE：yelee，[简而不减Hexo双栏博客主题.][8]

例如你想用的主题是yilia，那么需要先执行如下操作：
```
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```
然后将hexo目录中的```_config.yml```文件修改为```theme: yilia```，再重新部署一次即可。
在使用各种主题时，可以按照自己的需要修改主题中的`_config.yml`文件。

###hexo操作###
前文已经提到了`hexo`的`generate`，`server`，和`deploy`操作，在实际操作时，可以将其简写为`hexo g`，`hexo s`，和`hexo d`。
撰写新文章的步骤如下：

 - 运行`hexo new "AAA"`，在`hexo\source\_posts`文件夹中就会出现一个名为`AAA.md`的文件；
 - 按照markdown语法编辑该文件并保存；
 - 运行`hexo generate`；
 - 运行`hexo deploy`即可完成部署。

###博客源码的更新###

每次对hexo中的文件进行操作之后，就需要更新一次源码的备份，以防资料丢失。即在git bash中运行：
```
git add .
git commit -m "message here"
git push origin master
```
push操作完成后，博客的源码就会被更新到你的仓库blog中。

###将博客绑定在自己的域名上###
**方便起见，假定自己的域名为`www.domain.com`。**
绑定域名步骤如下：

 - 进入仓库`github.com/XXX/XXX.github.io`，进入settings，在custom domain中填写`www.domain.com`；
 - 然后在域名的设置页面设置解析为`lzcwr.github.io`即可。



 


  [1]: https://git-for-windows.github.io/
  [2]: https://github.com/
  [3]: http://www.jianshu.com/p/21234432c94e
  [4]: https://nodejs.org/en/
  [5]: https://github.com/litten/hexo-theme-yilia
  [6]: https://github.com/forsigner/fexon/hexo-theme-yilia
  [7]: https://github.com/iissnan/hexo-theme-next
  [8]: https://github.com/MOxFIVE/hexo-theme-yelee