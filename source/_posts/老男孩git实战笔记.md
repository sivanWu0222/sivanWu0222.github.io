---
title: 老男孩git实战视频笔记
author: yirufeng
top: true
cover: true
coverImg: /images/1.jpg
toc: true
mathjax: false
sidebar: [blogger, webinfo, tagcloud, category]
summary: 老男孩git实战课程的笔记，通过故事描述git的具体操作过程，视频参考：https://www.bilibili.com/video/BV19E411f76x?p=1
categories: 
  - 工具
  - git
keywords: git
tags:
  - Tools
date: 2020-06-10 10:43:59
---

[本笔记来自视频观看后的总结](https://www.bilibili.com/video/BV19E411f76x?p=1)

[老师自己总结全文参考](https://www.cnblogs.com/wupeiqi/p/7295372.html)

[git使用参考网站](https://git-scm.com/book/zh/v2)

[廖雪峰git教程](https://www.liaoxuefeng.com/wiki/896043488029600/897013573512192)



本课参考自己下载的对应视频教材的 **git实战课堂笔记**

#  01 git 是什么



# git 是什么

- 分布式
- 版本控制
- 软件



版本管理发展的历史：

1. 多文件管理，只是不同的文件命名，电脑上有多个文件![image-20200610211737949](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200610211737949.png)
2. 本地版本管理系统：电脑上只显示一个文件，会将其他版本下的文件隐藏起来，![image-20200610211716255](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200610211716255.png)
3. 集中式版本控制：![image-20200610211317057](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200610211317057.png) 类似于百度网盘，将所有的版本都存在中央服务器，最有代表性的软件就是**SVN**。 缺点：如果计算机连不上中央服务器，或者服务器挂掉，那么将会文件同步不到服务器上。
4. 分布式版本控制：![image-20200610211649662](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200610211649662.png)

对于集中式的版本而言，所有的版本信息都存在服务器上，而每个具体的计算机都只有一个版本，或者一个文件，而没有历史版本。而对于分布式，每个计算机都存在着所有版本信息，因此中心服务器挂掉之后，不会影响每个计算机存在的各自的版本，我们之后推上去到中心服务器即可。

注意：如果本地计算机写了一个新功能，首先会提交到本地的版本信息库中，然后再从自己的版本信息库中同步到远程服务器的中心。而集中式要想推只能推一个，但是分布式中心挂掉，不影响本地推代码到本地版本库中，最后推到远程即可。



# 02 为什么要做版本控制

...........写好的功能可以回到上一个版本，很方便，不用来回删除代码或加上代码





# 03 安装git

![image-20200610213541958](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200610213541958.png)

装上之后，我们可以在电脑上进行版本控制了，**但是还是只是局限在本地**。没有办法放到服务器(仓库)上，github或gitlab就属于服务器。



[git安装参考](https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%AE%89%E8%A3%85-Git)



# 04 东北热创业初期

一个人开发一个东北热平台，期间使用版本控制开发

## 2.1 第一阶段：单枪匹马开始干

如果想让git 帮助我们进行版本控制(其实就是帮助我们管理文件或文件夹)，需要进行如下几步：

1. 进入要管理的文件夹

2. 初始化：`git init` (相当于做了标记，提名谁是老大管理我们要管理文件夹下的这些文件以及文件夹)

3. 检测当前文件夹下的文件的状态：`git status`

4. 管理文件或文件夹 检测之后采用 `git add 文件名` 管理文件。

5. 按照正常流程，这一步还是要加上的，加上个人信息的配置(邮箱，用户名)，这样提交的时候就有了个人信息。![image-20200611073453989](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200611073453989.png)。【仅需执行如下命令一次即可】

   ```
   git config --global user.email ""
   git config --global user.name ""
   ```

6. 生成版本：`git commit -m '版本的描述信息'`

注意：`git add .` 其中`.`代表当前文件夹下没有被管理进去的所有文件



7. 通过`git log` 查看之前git commit 提交的所有记录

注意git中三种文件状态的变化：

- 红色：git 未被管理的文件或修改后未被管理的文件 红色->绿色：`git add 文件名/.`
- 绿色：git 管理起来的文件，但是没有还没有生成版本。执行`git commit -m '版本信息'`会帮助我们生成版本
- 生成版本



# 05

git commit 之前需要配置信息，查看04即可



# 06 git 三大区域

工作区：当我们执行`git init` 之后，写代码的时候正在操作的文件夹，文件夹里面的文件里面有两种状态(已管理的文件，新文件或修改的文件)。git会自动检测文件是否是新文件，或者原来已管理的文件又修改则自动变成修改的文件。为什么会自动检测：因为我们执行了`git init`

暂存区：可以理解为暂时临时先放在这里。如果文件提交到暂存区，不想要我们可以回滚到工作区。 通过`git add ` 将文件从工作区提交到暂存区

版本库：通过`git commit `将暂存区的文件提交到版本库中



![image-20200611075555062](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200611075555062.png)

因为我们知道工作区和版本区是有必要的，为什么需要暂存区呢？

暂存区我们可以理解为缓冲区，就是可以理解为我们冲动的时候想要做的事，但是事后又不想要。所以暂存区可以理解为给你冷静的时间，让你可以回去也可以继续。





# 07 回滚

通过`git log `看到所有版本的记录

通过`git reset --hard 版本号`来回滚到指定版本号的记录



有时我们遇到如下几种情况：

版本迭代：v1->v2->v3->v4->v5  但是我们从v5回滚到了v3之后，又想回滚到v4。此时通过`git log` 看不到v4的提交记录，但是通过`git reflog`可以看到

如果我们想回到v4，那么执行`git reset --hard 版本号` 版本号可以通过`git reflog`命令看到。也就是回滚到版本后面的状态需要通过`git reflog`查看版本号



## 2.2 第二阶段：拓展新功能

```
git add
git commit -m ''
```



## 2.3 第三阶段：约饭世界



```
git add
git commit -m ''
```

- 遇到有关部门查处，我们要进行回滚，回滚到之前的版本

```
git log 查看之前的回滚记录
git reset --hard 版本号
```

- 约饭功能可以继续上线，回滚的不是原来的状态，而是回滚到原来状态之后的版本，通过`git log`是看不到记录的，需要通过`git reflog`

  ```
  git reflog
  git reset --hard 版本号
  ```

  ![image-20200611104048150](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200611104048150.png)

# 08 git 命令小结



## 2.4 总结

```
git init
git add
git commit
git log
git reflog
git reset --hard 版本号

```

各个区域来回切换：

![image-20200611083038941](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200611083038941.png)



- 比如我们在项目文件夹下修改了index.html 但是没有add和commit，那么修改完之后使用如下命令就可以回到修改前的状态

`git checkout -- index.html`

比如我们在项目文件夹下修改了index.html 但是只是`git add`，那么修改完之后使用如下命令就可以回到修改前的状态

`git reset HEAD index.html`



# 09 分支



前面讲的例子中，我们在第一个版本开发了东北热（共100个文件），之后第二个版本添加了短视频的功能(只是在东北热的基础上修改了10个文件，并且增加了20个文件)



- 问题：git 在第二个版本发布时，保存了哪些文件？

版本2只保存了修改的文件(是修改文件的全部哦，而不是只修改的部分)，没有修改的那90个文件还都在版本1中，版本2中并没有保存，另外需要注意的是版本2有一个指针指向版本1。当我们获取版本2的文件的时候，版本2利用指针拉取版本1的文件，首先从版本1中拉取没有改变的文件，之后从再从版本2中加载修改的文件。









# 10 紧急修复线上bug的思路

默认主干线的分支名字叫做master，在master上可以分其他分支，名字自己定义。修改完之后合并到master

## 2.5 第四阶段 开发一个商城

在开发商城的时候，如果线上的约饭功能遇到了紧急bug，那么如何处理呢？



此时可以新建一个分支开发商城，当我们遇到紧急bug的时候直接回到原始的没有开发商城之前，在此基础上新建一个分支来修复bug，修复完成后再上线。此时切换到我们开发商城的那个分支继续开发，不会耽搁我们开发商城以及修改Bug。

![image-20200611114532550](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200611114532550.png)

![image-20200611122401535](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200611122401535.png)

# 11 基于分支修复线上bug的具体过程



`git branch` 目前所处在的分支

`git branch 分支名` 创建分支

`git checkout 分支名` 切换到指定的分支，





当我们基于目前的代码创建一个分支之后，在该分支上开发不影响master分支。





c1 -> c2 -> c3(master)

此时我们开发商城功能，因此新建一个分支

```
git branch dev
git checkout dev
开发过程中此时遇到了线上的紧急Bug修复
git add .
git commit .
切换到master分支下新建bug分支
git checkout master
git branch bug
git checkout bug
修复完成，
git add .
git commit -m ''
此时需要合并分支
git checkout master  
git merge bug  相当于站在master分支中，将bug分支拉过来
bug完全修复完成,此时应该删掉bug分支
git branch -d bug
此时可以切换到dev分支继续开发我们的商城，但是原来是基于c3开发的，那个时候bug还没有修复，所以我们先不要管继续开发我们的商城。开发完毕之后
git checkout dev
git status
git add .
git commit -m ''
此时合并新功能到master分支
git checkout master
git merge dev  此时会报错，冲突。为什么会产生冲突呢？因为我们此时线上的版本是修改了bug，而dev分支新建的时候线上的bug还没有修复，会有不一样的地方，因此会产生冲突，此时git不知道如何处理，就会冲突，会在冲突文件中展示各个分支的冲突。我们需要手动解决冲突，解决完之后执行如下代码
git status
git add .
git commit -m ''
```

何时会冲突：如果我们新建的分支基于c3，但是合并该分支到master的时候，master此时不是c3那个版本就会冲突。如果没有冲突则会自动合并。



# 12 命令总结和工作流



- 查看分支

  ```
  git branch
  ```


- 创建分支

  ```
  git branch 分支名称
  ```
  
- 切换分支

  ```
  git checkout 分支名称
  git checkout -b 创建并切换分支
  ```

- 分支合并(可能产生冲突，找到冲突的文件快速修改即可，后面还会介绍一个软件来修改冲突)，注意：要合并到那里就要切换到哪里，之后再合并分支，比如我想合并dev，那么首先就要`git checkout master`  **切换分支再合并**

  ```
  git merge 要合并的分支
  ```

- 删除分支

  ```
  git branch -d 分支名称
  ```



git使用的工作流：以后使用git的时候，我们至少有两个分支master以及dev,我们要在dev分支上进行开发



# 13 github

使用步骤：

1. 创建github账号
2. 创建仓库
3. 本地仓库推送到github



大公司都在用Gitlab 因为只需要一个搭建服务器的钱，并且代码不会泄露

# 14 基于github做代码托管





`git remote add origin https://github.com/WuPeiqi/dbhot.git` 相当于给后面的网址起了一个别名叫origin



之后我们可以使用`git push -u origin master` 将我们本地编写的master分支内容推送上去，其他分支内容不会推送哦。



因此可以使用`git push -u origin 分支名` 将分支推送上去





当我们来到公司之后：

`git clone 网址` 直接克隆代码到本地，此时虽然`git branch`显示只有一个分支，但是其实所有分支都克隆下来了，我们可以直接使用`git checkotu 分支名`进行切换





## 2.6 第5阶段：进军三里屯

1. 给远程仓库起别名

   `git remote add origin 网址` 其中origin可以修改成任意字符串

2. 向远程提交推送代码

   `git push -u origin 分支名称` 推送我们想要推送的分支到远程仓库

3. 公司里面拉代码，克隆远程仓库代码。此时不仅仅会拉下来master分支，会克隆下来所有分支

   `git clone 远程仓库地址`  当我们git clone的时候内部已经自动实现了`git remote add origin 远程仓库地址`命令




![image-20200612072355165](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612072355165.png)

![image-20200612072506729](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612072506729.png)



![image-20200612073056906](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612073056906.png)

# 15 奔波于沙河和三里屯之间

前面我们已经在公司克隆下了代码，但是我们在dev分支开发时基于之前的master，而现在master已经更新了好几次，所以继续在在dev分支开发新功能的时候要确保dev分支是基于最新的master。**也就是我们之前的dev分支功能已经合并到master中了，且dev分支的代码还是基于以前的master，代码比较老，如果新开发一些功能，我们肯定要基于目前的master，因为master是运行的最新稳定版，所以我们要在dev分支`git merge master`** 注意只执行一次哦


```
dev分支下执行：
	git merge master

公司第一天代码开发完成以后
	git add .
	git commit -m '公司第一天开发的代码'
	git push origin dev
回到家之后
	git branch 
	git checkout dev
	更新家里的代码：从远端仓库拉下来进行更新
	git pull origin dev  从远程的dev分支拉代码更新
	继续编写代码
	git add .
	git commit -m ''
	git push origin dev
来到公司
	git branch
	git checkout dev
	git pull origin dev  要确保在dev分支下哦

	
	
```

`git push -u origin master` 其中-u指定了以后提交的默认分支，之后我们`git push`命令提交的时候会按照上次的-u指定的分支提交

# 16 约妹子忘推送代码了



故事情节：被妹子约饭，在公司写代码之后`git commit` 命令执行完没有`git push`，回到家`git pull origin dev`无法更新代码，没办法只好继续在之前的文件下开发，之后`git push origin dev`到远程，第二天去到公司`git pull origin dev`代码时出现conflict，此时所有代码都拉取下来了，只是需要我们手动修改冲突的文件(冲突的文件和之前`git merge`冲突文件一样)即可(类似于之前我们`git merge`出现的冲突的解决方式)，解决完冲突之后直接`git add` `git commit` `git push`到远程即可。





```
git pull origin dev 拉取远程仓库代码并合并到本地的dev分支
这一句等同于如下两个命令
git fetch origin dev  将远程代码拉到版本库中
git merge origin/dev  将版本库中的远程拉下来的origin/dev合并到工作区中
```



`git fetch origin 分支名称` 会自动从远程仓库将问价拉取到版本库中，此时为了区分从远程仓库拿下来，git会自动在我们拉取到版本库的文件中的分支名称前面加上一个前缀 `origin/`。

因此我们后面讲版本库中的文件同步到工作区的时候使用`git merge origin/分支名`

![image-20200612075200898](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612075200898.png)

# 17 (rebase是面试重点)rebase应用场景一：将之前的多条commit记录合并

工作中应用`git rebase`可以让我们的提交记录变得很简洁，rebase译为变基的意思。





应用场景：公司里面老板给了一个大任务，需要一周时间完成，因为我们每天都会有提交记录，所以到最后的时候我们会有很多commit 记录，但是这些都是无用的，因为老板想要最开始的代码以及最后开发完时的一条commit记录，所以我们可以通过rebase将我们多个提交记录整合成一个提交记录



`git rebase -i `命令进行修改，有两种形式：

1. `git rebase -i  版本号` 会合并我们目前所在的版本与版本号之间的版本，两边都包括![image-20200612083339893](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612083339893.png)
2. `git rebase -i HEAD~3` 从当前记录开始找最近的3条记录合并，包括当前记录![image-20200612083210486](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612083210486.png)



执行命令后进入如下界面：

![image-20200612083508550](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612083508550.png)

>  此时v4会合并到上一个版本v3 而v3前面我们也是s因此我们将v3再合并到v2中

修改完成之后会进入如下界面：

![image-20200612083550206](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612083550206.png)

因为我们合并了几条提交记录，因此也需要修改几条提交记录合并之后的提交记录。修改为如下内容：![image-20200612083745687](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612083745687.png)

# 18 (rebase是面试重点)rebase应用场景二：将之前我们分支的记录合并到主分支上也就是归成一条分支上的记录





`git log --graph` 以图形化的形式在命令行中展现git的提交记录

`git log --graph --pretty=format:"%h %s"` 其中%h代表提交记录的hash值，%s代表我们commit的信息

![image-20200612085816796](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612085816796.png)







1. 切换到dev

2. `git rebase master`
3. 切换回master分支，直接`git merge dev`即可



```
首先确保master以及dev都保持一致，之后执行如下几条命令，使得master以及dev分支下有自己的新提交记录，
git checkout master
touch master_1.py
git add master_1.py .
git commit -m 'master_1'
git checkout dev
touch dev_1.py
git add dev_1.py
git commit -m 'dev_1'
下面开始本课堂的内容
git checkout dev
git rebase master
ls 会发现已经有了 master_1.py文件
git checkout master
git merge dev
git log --graph
```



此时提交记录就变成了一条线![image-20200612091404961](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612091404961.png)，如果这种情况下我们使用`git merge`命令将会产生分叉



# 19 (rebase是面试重点)rebase应用场景三

其实在前面的约妹子忘记push代码到远程的这个场景中，如果我们使用merge命令合并家里写的代码以及公司写的代码就会产生分叉。如何不产生分叉呢？



```
git checkout dev
第一天下午公司代码开发完
git add .
git commit -m ''
约妹子吃饭
回到家
git pull origin dev
继续开发
git add .
git commit -m ''
git push origin dev
第二天到达公司，按照以往的逻辑会执行如下代码：
git pull origin dev
然后接着开发就行，但是我们这里如果使用git pull将会产生分叉，那么不产生分叉如何操作呢？我们到达公司之后，进行如下操作：
git fetch  # 
git rebase origin/dev
```





解决办法：

第二天到达公司不执行以往的`git pull origin dev`命令，而是替换为如下命令：

1. `git fetch origin dev` 接着版本库中有一个远程的origin/dev分支
2. `git rebase origin/dev` 因为我们要把远程拉下来放到版本库中的dev分支回到工作区中





注意事项：如果git rebase命令之后有冲突此时就会中断rebase，此时不要紧张，我们手动解决冲突，然后(按照提示)重新`git add`，之后执行`git rebase --continue`命令即可

![image-20200612093105811](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612093105811.png)

rebase遇到冲突提示如下：![image-20200612093221919](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612093221919.png)



# 20 使用beyond compare软件快速解决冲突



解决冲突的软件：beyond compare ，mac 和 windows都有



以后我们git中使用compare解决冲突，需要双方进行连接，创建关系之后以后我们合并文件的时候直接可以使用命令启动compare帮我们解决冲突



![image-20200612101924823](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612101924823.png)

![image-20200612102133785](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612102133785.png)

查看安装路径：![image-20200612101952330](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612101952330.png)之后点击安装命令行工具，返回如下结果![image-20200612102029625](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612102029625.png)





`git config --local`  --local指的是当前配置只在当前项目中有效



![image-20200612102744749](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612102744749.png)

# 21 命令总结

![image-20200612103703736](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612103703736.png)



# 22 多人协同开发之gitflow工作流思路

假设又招2个人，并且东北热目前只有有一个上线的版本(v1)。





面试题：公司如何做多人协同开发？

每个人有一个分支，在自己的分支下写代码，是从dev分支下拆除下来的。

当我们每个人自己的功能完成之后想要合并的时候需要老大进行review，只有通过之后才可以合并进去。在小公司里面，代码review通过之后会合并到Master里面，但是大公司比较谨慎，往往会将代码review通过的代码合并到release分支，做测试工作，测试通过之后才上线，如果测试不通过需要修改Bug。

![image-20200612144441814](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612144441814.png)



当我们分配到一个新功能的时候大多情况下以功能命名分支比较多见，当分支开发完成合并上线之后就可以删除了



下图是一个完整的gitflow工作流：

![image-20200612144955658](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612144955658.png)



不正规的团队会忽略release以及code review

![image-20200612145256655](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612145256655.png)





# 23 多人协同开发之创建初始项目和版本

- 创建初始项目并同步到远程仓库：

如果想要多人协同开发，需要在github的项目上邀请别人，项目的settings->collaborators->写上用户的昵称   **这种方式只适合于个人不适合于公司**



(**适合于公司**)公司中会新建一个组织，然后在组织中创建项目。



- 创建版本：

这里我们新建一个项目并且初始化一个最基本的版本，**在公司中我们会基于tag做版本管理** 不要使用hash值做版本管理



还可以创建带有说明的标签，用`-a`指定标签名，`-m`指定说明文字：[参考](https://www.liaoxuefeng.com/wiki/896043488029600/902335212905824)

```
git tag -a v1 -m "第一版"   本地生成版本
git log

git push origin --tags  推送版本到github
```

![image-20200612151048580](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612151048580.png)













# 24 多人协同开发之邀请成员

首先在邀请成员之前我们首先要建立一个dev分支，因为其他成员来了都是在dev分支下进行开发



```
git branch
git checkout -b dev
git branch
git push origin dev
```



此时小弟开始做事，但是首先小弟要注册github账号并且拉小弟进入。github与gitlab都是一样的。

# 25 多人协同开发之邀请小弟开发

默认情况下成员拥有所有项目的只读权限，只有负责对应项目的人才有写的权限。



对于组织可以设置权限，同时对于项目我们也可以设置权限



当我们进入公司之后，需要clone代码到本地，然后切换到dev分支，之后在dev分支下新建我们自己的分支

```
git clone ...
git checkout dev
git checkout -b 你自己的分支名称
```

注意：新建分支默认是基于当前分支新建





# 26 多人协同开发之代码review

面试题：你们公司做不做review？谁来做呢？如何做呢？



做，一般是小组长来做，通过github上的pull request（也叫merge request）来做



首先要进行配置：

![image-20200612164754654](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612164754654.png)

![image-20200612164725770](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612164725770.png)



如何通知老大code review呢？发起一个Pull request

![image-20200612173511345](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612173511345.png)



老大如何做code review呢？

方式1：网站上直接code review![image-20200612173846495](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612173846495.png)![image-20200612173921826](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612173921826.png)![image-20200612174045374](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612174202238.png)![image-20200612174414804](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612174414804.png)

方式2：命令行拉取代码到本地进行review





# 27 多人协同开发之测试上线

做完了代码review，leader或者测试团队首先会在dev分支下创建一个release分支

```
git branch
git checkout dev
git checkout -b release
git push origin release
进行测试，期间不允许修改dev中的代码
没有问题则合并到master中或者pull request到master中都可以，取决于leader
之后合并到master以及dev中(因为可能修改Bug)
此时可以删除release分支，是否删除取决于测试以及leader
然后本地切换到master分支
从远程拉取代码 git pull origin master
有时需要加上标签，git tag -a v2 -m '第二版 增加斗地主功能'
git push --tags
```



注意：release没问题的时候合并到master采用pull request 以及 merge都可以，取决于leader，但是当我们自己开发功能的分支合并到dev中必须pull request


# 28 给开源项目贡献代码

面试题：想要找个牛逼的框架贡献代码如何做？？



1. fork源代码：将别人源代码拷贝到自己仓库
2. 自己仓库修改代码
3. 给源代码作者提交修复bug的申请，申请pull request



# 29 配置文件存放3个位置

```
git config --local user.name ''
git config --local user.email ''

git config --system user.name ''
git config --system user.email ''

git config --global user.name ''
git config --global user.email ''
```



第一个配置文件(**项目配置文件**，对应`--local`, 表示只在当前项目生效)：本地项目的 `.git`目录下的`config`

第二个配置文件(**全局配置文件**，对应`--global`)：存放在`~/.gitconfig`

优先级：首先在本项目的`.git/config`中查找，没有的话就去`~/.gitconfig`



第三个配置文件：(系统配置文件，对应`--system`)：存放在 `/etc/.gitconfig`。**需要有root权限**

![image-20200613072231064](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200613072231064.png)



# 30 git 免密登录

![image-20200613072622395](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200613072622395.png)







![image-20200613073235173](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200613073235173.png)



# 31 gitignore忽略文件

github上官方提供了一个仓库用来存储各个语言的gitignore文件 [官方](https://www.github.com/github/ignore)

![image-20200613074020619](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200613074020619.png)**一定要严格注意一些敏感数据文件我们需要加入到gitignore中**

# 32 github做任务管理



![image-20200612142549275](/Users/yirufeng/%E5%AE%9E%E4%B9%A0/%E9%A1%B9%E7%9B%AE/images/img/image-20200612142549275.png)

Issue：任务管理系统，也可以将他人提出的bug安排给员工

Wiki：对项目的介绍文档以及使用安装等