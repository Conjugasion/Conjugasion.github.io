---
title: git merge与git rebase的区别
tag: Git
category: Git
excerpt: 合并上的区别
cover: /image18.jpg
---
### **merge与rebase的区别**
假设我们有如下图一所示仓库，该仓库有master和develop两个分支，且develop是在（3.added merge.txt file）commit处从master拉出来的分支。

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image1.jpg">
</div>
<br/>

### **merge**
假设现在HEAD在（6.added hello.txt file）处，也就是在master分支最近的一次提交处，此时执行git merge develop, 结果如下图所示。

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image2.jpg">
</div>
<br/>

工作原理就是：git 会自动根据两个分支的共同祖先即 (3.added merge.txt file)这个 commit 和两个分支的最新提交即 (6.added hello.txt file) 和 (5.added test.txt file) 进行一个三方合并，然后将合并中修改的内容生成一个新的 commit，即图二的(7.Merge branch ‘develop’)。
这是merge的效果，简单来说就合并两个分支并生成一个新的提交。
### **merge**
假设初始状态也是图一所显示的。两个分支一个master，一个develop，此时HEAD在（6.added hello.txt file）处，现在执行git rebase develop,结果如下图三所示:

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image3.jpg">
</div>
<br/>

可以看见develop分支分出来分叉不见了，下面来解释一下它的工作原理：
在执行git rebase develop之前，HEAD在（6.added hello.txt file）处，当执行rebase操作时，git 会从两个分支的共同祖先 (3.added merge.txt file)开始提取 当前分支（此时是master分支）上的修改，即 （6.added hello.txt file）这个commit，再将 master 分支指向 目标分支的最新提交（此时是develop分支）即（5.added test.txt file） 处，然后将刚刚提取的修改应用到这个最新提交后面。如果提取的修改有多个，那git将依次应用到最新的提交后面，如下两图所示，图四为初始状态，图五为执行rebase后的状态。

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image4.jpg">
</div>
<br/>

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image5.jpg">
</div>
<br/>

简单来说，git rebase提取操作有点像git cherry-pick一样，执行rebase后依次将当前的提交cherry-pick到目标分支上，然后将在原始分支上的已提交的commit删除。
### **merge OR rebase**
那什么时候用merge，什么时候用rebase呢？
再举个例子：
初始状态如下图六所示：
和之前一样的是，develop分支也是在 (3.added merge.txt file)处从master分支拉取develop分支。不一样的是两个分支各个commit的时间不同，之前develop分支的4和5commit在master分支3之后6之前，现在是develop分支的4提交早于master分支的5提交，develop分支的6提交晚于master的5提交早于master的7提交。

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image6.jpg">
</div>
<br/>

在上图情况下，在master分支的7commit处，执行git merge develop，结果如下图七所示：

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image7.jpg">
</div>
<br/>

执行git rebase develop，结果如下图八所示：

<div style="text-align:center">
<img src="/git merge与git rebase的区别/image8.jpg">
</div>
<br/>

可以看出merge结果能够体现出时间线，但是rebase会打乱时间线。
