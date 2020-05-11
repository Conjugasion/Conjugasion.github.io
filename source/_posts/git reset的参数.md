---
title: git reset的参数hard、mixed、soft
tag: [Git]
category: Git
excerpt: git reset命令
cover: /image17.jpg
---
### **git reset**
hard：将HEAD之后的变更内容全部丢弃。

mixed：将HEAD之后的变更内容放到工作区中

soft：代表将HEAD之后的变更内容放到暂存区中。

以下下三个图均在我当前HEAD中，将test.txt添加到了暂存区，但是没有提交的条件下执行的。不用关注我过程修改的内容，你只需要关注当我执行了mixed/soft/hard之后，使用git status查看的状态。


<div style="text-align:center">
<img src="/git reset的参数/image1.jpg">
</div>
<br/>

<div style="text-align:center">
<img src="/git reset的参数/image2.jpg">
</div>
<br/>

<div style="text-align:center">
<img src="/git reset的参数/image3.jpg">
</div>
<br/>