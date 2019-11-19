---
title: hexo博客同步管理
tag: [hexo,git]
category: blog
excerpt: 多机同步管理博客
cover: /sync-blog/zoro.jpg
---
### **从分析目录结构开始**
首先，需要了解一下hexo项目的目录结构，以及各文件夹用来存放哪些文件（已经了解的可以跳过）。下面是我本地hexo项目的目录结构。文件夹没有展开，为了和文件区别，我在文件夹名后加了一个/用以和文件名区分开来。

``` bash
HEXO
├──.deploy_git/
├──node_modules/
├──public/
├──scaffolds/
├──source/
├──themes/
├──_config.yml
├──.gitignore
├──db.json
├──debug.log
└──package.jsono new "My New Post"
```
先说<font color=red>public/</font>文件夹和<font color=red>.deploy_git</font>文件夹，这两个文件夹里的内容是一样的。用来存放由markdown文件（文章）生成的所有静态文件，猜测<font color=red>.deploy_git</font>文件夹里的内容就是在执行<font color=red>hexo g -d</font>命令时唯一会部署到github仓库<font color=red>username.github.io</font>的内容（这里没有求证过，从命名来看应该是这样，如有谬误还请指正）。换句话说，github pages托管的只是由hexo生成的静态文件，hexo的环境文件是只保留在本地的，而这也正是想要在另一台电脑上重新部署和保持同步hexo博客需要折腾一番的原因。

刚才提到一个环境文件的概念，这个不是什么官方提供的术语，只是我想要区分public/文件夹里的静态文件临时想的一个词，可能不是很准确，但是接下来我将沿用这两个概念（囧）。那么环境文件里我们需要关注的几个比较重要的文件分别是：

<font color=red>source/</font>文件夹：所有发布的文章的md文件都放在这儿
<font color=red>themes/</font>themes/文件夹：主题的相关配置文件以及样式的修改等都在这儿
<font color=red>.gitignore</font>：git提交时将会忽略的项

<font color=red>.gitignore</font>文件的内容如下：

``` bash
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
├──db.json
├──debug.log
└──package.jsono new "My New Post"
```
其实从这也可以看出hexo的本意也是让我们可以把环境文件放在github上托管的，并且帮我们列好了忽略的项，比如<font color=red>public/</font>、<font color=red>.deploy_git</font>文件夹都是被忽略的，是不会被提交的。这样，我们在<font color=red>hexo g -d</font>时部署到github上的只有静态文件，而提交环境文件时则不会把静态文件也提交，节省了空间。

### **怎么把环境文件托管到github**
现在，我们弄清楚了只要把环境文件也托管到github并且和静态文件的托管互不影响就行了，问题是，怎么做？

在hexo项目跟目录下执行命令<font color=red>git status</font>,会得到<font color=red>fatal: Not a git repository (or any of the parent directories): .git</font>的提示：当前项目不是一个git仓库。我之前以为既然静态文件可以被提交到github，那hexo项目肯定是一个git仓库，原来不是，静态文件能被推送到远程仓库hexo引擎在背后是怎么实现的我不知道，也不在本文的讨论范围内。目前要紧的是把当前项目跟一个远程仓库关联起来，好管理环境文件。

那么，难道要新建一个仓库来存放环境文件嘛？不，那样当然也行，但是还要单独占一个仓库，总感觉不舒服。其实我们可以用分支来管理。

在<font color=red>username.github.io</font>仓库中，目前默认的分支是master,用来存放静态文件。我们新建一个分支hexo,并设为默认分支，用来存放环境文件。

<div style="text-align:center">
<img src="/sync-blog/image1.jpg">
</div>
<br />

这里是比较关键的一步了，hexo分支是基于master分支的，它在创建时被赋予了与master分支同样的内容--静态文件。做乜啊，hexo分支不是要存放环境文件的吗？其实现在目的很清晰：**我们希望hexo分支是空的，并把我们本地的hexo项目与hexo分支关联起来，好把环境文件推送过去。**要达到这个目的，要分以下几步走：

● github上切换到hexo分支，<font color=red>git clone</font>仓库到本地。
● 此时本地会多出一个<font color=red>username.github.io</font>文件夹，命令行cd进去，删除除.git文件夹（如果你看不到这个文件夹，说明是隐藏了。windows下需要右击文件夹内空白处，点选'显示/隐藏 异常文件'，Mac下我就不知道了）外的其他文件夹。
● 命令行<font color=red>git add -A</font>把工作区的变化（包括已删除的文件）提交到暂存区（ps:<font color=red>git add .</font>提交的变化不包括已删除的文件）。
● 命令行<font color=red>git commint -m "some description"</font>提交。
● 命令行<font color=red>git push origin hexo</font>推送到远程hexo分支。此时刷下github，如果正常操作，hexo分支应该已经被清空了。
● 复制本地<font color=red>username.github.io</font>文件夹中的<font color=red>.git</font>文件夹到hexo项目根目录下。此时，hexo项目已经变成了和远程hexo分支关联的本地仓库了。而<font color=red>username.github.io</font>文件夹的使命到此为止，你可以把它删掉，因为我们只是把它作为一个“中转站”的角色。以后每次发布新文章或修改网站样式文件时，<font color=red>git add . & git commit -m "some description" & git push origin hexo</font>即可把环境文件推送到hexo分支。然后再<font color=red>hexo g -d</font>发布网站并推送静态文件到master分支。
至此，hexo的环境文件已经全部托管在github的hexo分支了。

### **新电脑中的环境搭建**
这部分应该要简单一点，如果你已经搭建过一个hexo博客的话。

● 新电脑上安装node.js和git。不赘述。git教程推荐[廖雪峰老师](https://www.liaoxuefeng.com/wiki/896043488029600)。
● 安装hexo：<font color=red>npm install -g hexo-cli</font>。
● clone远程仓库到本地 <font color=red>git clone git@github.com:username/username.github.io.git</font>。
● 根据packge.json安装依赖<font color=red>npm install</font>。
● 本地生成网站并开启博客服务器：<font color=red>hexo g & hexo s</font>。如果一切正常，此时打开浏览器输入<font color=red>http://localhost:4000/</font>已经可以看到博客正常运行了。

### **在两台电脑上的同步操作**
至此，迁移工作已完成，在两台电脑之间的同步操作如下：

● <font color=red>git pull</font>从远程hexo分支拉取最新的环境文件到本地，可以理解为svn的更新操作。比如在公司写了博客，回家在电脑上也要写需要先执行这一步操作。
● 文章写完，要发布时，需要先提交环境文件，再发布文章。按以下顺序执行命令：git add .、git commit -m "some descrption"、git push origin hexo、hexo g -d。

### **一些坑**
有些坑是在上面的工程中会遇到的，但是如果穿插在工程中将会如过程显得凌乱，所以我单独拿出来讲一下。

### **添加ssh key**
这个坑是在执行<font color=red>git clone</font>之前要解决的。

github提供了两种<font color=red>git clone</font>的方式：https和ssh。由于https速度慢，且每次都要输入口令，所以我们一般选择使用ssh方式，这就需要我们添加ssh key。

<div style="text-align:center">
<img src="/sync-blog/image2.jpg">
</div>
<br />

● 命令行进入用户主目录,比如在我的电脑下：<font color=red>c:\users\administrator</font>。
● 命令行运行<font color=red>ssh-keygen -t rsa -C "username@example.com"</font>。它会提示你输入邮箱，输入完成以后一路回车就行。完成这一步，找到该目录下的<font color=red>.ssh</font>文件夹，里面多了两个文件：id_rsa和id_rsa.pub分别是你这台电脑的ssh key的私钥和公钥。用文本打开公钥文件，复制里面的所有内容备用。
● 登录github后，进入[settings] -> [SSH and GPG keys]。点击[New SSH key],然后title随便填一个，比如可以是你的电脑名称，把刚才复制的公钥内容黏贴到key中，最后点击[Add SHH key]就完成了。
● 此时<font color=red>git clone</font>就不会再需要你输入口令了。