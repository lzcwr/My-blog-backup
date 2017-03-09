---
title: github+hexo 搭建个人博客
date: 2017-02-12 13:05:29
tags: Web
description: "github+hexo 搭建博客详细过程."
---
 - **本文针对的主要是Windows用户，不过总体步骤和Linux/Mac大同小异。**
 - **本文中所有需要打开文件编辑的地方均不推荐记事本，推荐使用[nodepad++][1]或[sublime text][2]。**
## 环境配置

### 安装git bash
直接在[官网][3]下载最新版本并安装；

### 申请github账号
在[github][4]填写信息注册账号；

 - **方便起见，以下均假设你的github用户名为 XXX.**
 - **方便起见，以下均假设你的github邮箱为 aa@bb.com**

### 建立github仓库

 - 建立一个仓库来存博客的源码，假设标题为“blog”；
 - 建立一个github仓库，标题是`XXX.github.io`；

### 安装Node.js
 -  安装Node.js：直接在Node.js[官网][5]下载最新版本并安装；

### 安装hexo
 - 在git bash中执行如下命令以安装hexo（必须在安装Node.js之后进行）；

```
npm install -g hexo
```

  - 执行以下语句以安装hexo所需的依赖包；
```
npm install
```
  
## 搭建博客

### 本地搭建

 - 任意建一个名为“hexo”文件夹，并在该文件夹下运行git bash（右键菜单中选择运行git bash）；
 - git bash中执行如下命令，hexo会自动将搭建所需的所有初始材料放入hexo文件夹中；

```
hexo init
```

 - 在git bash中运行如下命令，即可将博客的静态页面生成在`\hexo\public`目录下；

```
hexo generate #(或简写为 hexo g)
```

  - generate完毕后即可运行如下指令，成功后即可在`localhost:4000`浏览博客；
```
hexo server #(或简写为 hexo s)
```
  
### 部署到github

  - 修改hexo文件夹中的`_config.yml`文件：

```
deploy:
    type: git #用git来部署
# 以下两个"repo"任选一种即可
    repo: https://github.com/XXX/XXX.github.io.git
    repo: git@github.com:XXX/XXX.github.io.git
    branch: master
```

保存后即可将本地博客与`XXX.github.io`建立关联；

   - 本地浏览确认无误后运行如下命令即可将博客部署到`XXX.github.io`；

```
hexo deploy #(或简写为 hexo d)
```

   - 部署完毕后即可在`XXX.github.io`浏览页面。

### 可能出现的问题及解决办法

```
在执行 hexo d 时，可能会出现提示：
    ERROR Deployer not found: git
此时，需要执行如下命令：
    npm install hexo-deployer-git --save
完成后再执行 hexo d 即可。
```

```
在执行 hexo s 时，可能会出现提示：
    ERROR Plugin load failed: hexo-server
此时需要执行如下命令：
    npm install hexo-server
```
   

## 博客源码备份

### git配置

初次使用git，需要进行如下配置：

#### 全局配置
在git bash中依次执行如下语句以完成全局配置：

```
git config --global user.name XXX
git config --global user.email aa@bb.com
```

 
#### 配置ssh-key

 -  在git bash中执行以下语句：

```
ssh-keygen -t rsa -C "your_email@example.com" #(在github注册的邮箱)
```

 - 添加key到ssh：
    将`C:/Users/xxxx/.ssh/github_rsa.pub`中的全文复制，添加到github。
（浏览器登录github，找到`settings -> SSH and GPG keys -> new SSH key`粘贴进去确认即可）
至此，你的github账户已经和你的电脑建立了关联。
有关SSH的更详尽步骤可以参考[这里][6]，本文不再细化。

### git仓库更新

 - 在hexo目录下运行git bash；
 - 运行如下命令即可将该文件夹初始化为git仓库；

```
git init
```

  - 运行如下命令，将本地仓库至连接远程仓库(blog)；

```
git remote add origin git@github.com:XXX/blog
```

 - 依次运行如下命令，即可将源码更新至github的blog仓库，至此备份已经完成。

```
git add .
git commit -m "message here"
git push origin master
```

每次写新文章之后，都可以按照相同方式将源码再次更新一遍。
更多git操作可以查阅相关文档（例如[这里][7]），本文不再赘述。

## 后续操作

### 主题的选择
hexo默认的主题是landscape，<del>样子比较难看</del>，网络上有各种其他样式的主题：

 - [litten：yilia][8]，一个简洁优雅的hexo主题.
 - [forsigner：fexo][9]，一个极简主义风格的hexo主题.
 - [iissnan：next][10]，An elegant theme for Hexo.
 - [MOxFIVE：yelee][11]，简而不减Hexo双栏博客主题.

例如你想用的主题是yilia，那么需要先执行如下操作：

```
git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
```

然后将hexo目录中的`_config.yml`文件修改为`theme: yilia`，再重新部署一次即可使用yilia主题。
在使用各种主题时，可以按照自己的需要修改主题中的`_config.yml`文件。
**修改主题的各项操作均可在该主题的说明文档中查阅，本文不再赘述。**

### 撰写新文章

 - 运行如下命令，在`hexo\source\_posts`文件夹中就会出现一个名为`AAA.md`的文件；

````
hexo new "AAA"
```

 - 按照markdown语法编辑该文件并保存；
 - 依次运行如下命令即可完成部署。

```
hexo g
hexo d
```

关于markdown语法以及编辑器可自行查阅相关资料，本文不再赘述。

### 将博客绑定在自己的域名上
**方便起见，假定域名为`www.domain.com`。**
绑定域名步骤如下：

 - 进入仓库`github.com/XXX/XXX.github.io`，进入settings页面，在custom domain一项中填写`www.domain.com`（也可以在`hexo`目录下直接新建一个名为CNAME的文件，内容为`www.domain.com`，然后更新博客源码即可）；
 - 然后在域名的设置页面设置解析为`lzcwr.github.io`即可。
具体解析步骤可在自己购买域名后查看，本文不再赘述。
 
### hexo的常用命令

```
hexo new "postName" # 新建文章
hexo new page "pageName" # 新建页面
hexo generate # 生成静态页面至public目录
hexo server # 开启预览端口
hexo deploy # 将.deploy目录部署到GitHub
hexo help  # 查看帮助
hexo version  # 查看Hexo的版本
```

## 总结
以上谈到的步骤按照顺序大概是：

 - 安装所需要的软件：git bash，Node.js，hexo；
 - 申请github账号并新建仓库储存源码和博客页面；
 - 本地配置好git：全局配置（用户名和邮箱），和SSH-key配置；
 - 本地搭建博客：`hexo init`，`hexo g`，`hexo s`预览；
 - 将博客部署至github：修改`_config.yml`中的`deploy`部分，执行`hexo g`，`hexo d`；
 - 可能会出现无法部署至git的问题：安装`git-deployer`；
 - 撰写新文章：`hexo new "postName"`，`hexo g`，`hexo d`；
 - 更新源码：git操作（`add`，`commit`，`push`）。

 ## 可能遇到的其他问题

### Hexo与Mathjax的冲突

参见[Lzcwr——Hexo与Mathjax的冲突及（部分）解决][12]。


 


  [1]: https://notepad-plus-plus.org/
  [2]: http://www.sublimetext.com/
  [3]: https://git-for-windows.github.io/
  [4]: https://github.com/
  [5]: https://nodejs.org/en/
  [6]: http://www.jianshu.com/p/21234432c94e
  [7]: http://www.liaoxuefeng.com/
  [8]: https://github.com/litten/hexo-theme-yilia
  [9]: https://github.com/forsigner/fexon/hexo-theme-yilia
  [10]: https://github.com/iissnan/hexo-theme-next
  [11]: https://github.com/MOxFIVE/hexo-theme-yelee
  [12]: http://www.lizhechen.com/2017/03/08/Hexo%E4%B8%8EMathjax%E7%9A%84%E5%86%B2%E7%AA%81%E5%8F%8A%EF%BC%88%E9%83%A8%E5%88%86%EF%BC%89%E8%A7%A3%E5%86%B3/