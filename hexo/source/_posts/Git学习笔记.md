---
title: Git学习笔记
date: 2021-01-21 21:54:28
tags: Git
categories: 学习笔记
---

> 作为一个面向Github科研的水笔研究生，日常依赖Github完成科研工作，熟练掌握``git clone``的一百种方法。实验室的师兄给我们搭了个GitLab多人同步开发，着实让我记不住命令...根据[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/896043488029600)的整理一份个人的学习笔记

<!-- more -->

# Git本地仓库命令

## 基本单分支操作

- 建立本地仓库
  
  ``git init``

- 添加文件到本地仓库
  
  ``git add .``

- 提交文件到本地仓库

  ``git commit -m "做了啥改动"``

  > add操作可以多次执行，然后1次commit操作打包提交

- 查看本地仓库状态

  ``git status``

- 版本回溯
	
	每一次commit操作，相当于打上一个“存档点”，因此可以通过commit记录回到过去
	
	```bash
	# 查看commit的历史记录
	$ git log
	commit c43cbeab0fbee5b344415c6ec7d44cac51f857f6 (HEAD -> master)
	Author: lixiang <lix0419@outlook.com>
	Date:   Wed Jan 20 21:23:51 2021 +0800
	
	    reset_test添加commit 2行
	
	commit 9bc12b122bd803e9ded5af5b10ae805257b00312
	Author: lixiang <lix0419@outlook.com>
	Date:   Wed Jan 20 21:22:42 2021 +0800
	
	    添加test文件夹
  ```
  
    当前在c43cb...这个**HEAD**节点上。上一个版本就是**HEAD^**，上100个版本是**HEAD~100**
  
    ```bash
	# 回退版本
	$ git reset --hard HEAD^
	# 再次git log
	$ git log 
	commit 9bc12b122bd803e9ded5af5b10ae805257b00312 (HEAD -> master)
	Author: lixiang <lix0419@outlook.com>
	Date:   Wed Jan 20 21:22:42 2021 +0800
	
	    添加test文件夹
	  ```
	
    发现我们丢失了最后一次commit的东西，如果后悔的话，可以通过制定commit的id来找回

    ```bash
	$ git reflog
    9bc12b1 (HEAD -> master) HEAD@{0}: reset: moving to HEAD^
    c43cbea HEAD@{1}: commit: reset_test添加commit 2行
    9bc12b1 (HEAD -> master) HEAD@{2}: commit: 添加test文件夹
    87bd242 HEAD@{3}: commit: 修改笔记
    722f491 HEAD@{4}: commit: add readme
    3d224cd HEAD@{5}: commit (initial): 添加[本地仓库]笔记
    ```

    reflog命令记录了全部的操作，reset之前的id为c43cbea

    ```bash
    # 撤销reset操作
    git reset --hard c43cbea
    ```

- 撤销修改
  
  当改动了本地库的文件，需要撤销的时候，可以通过git checkout命令撤下修改
  
  > 对于vscode的用户，这一部分直接在vscode自带的版本控制栏里面点点点就完事了...

  ```bash
  # 当没有执行git add操作的时候，将回到上一次commit后的情况
  $ git checkout -- test/checkout_test.txt
  # 当执行了git add操作的时候，将回到上次add后的情况
  $ git status
  位于分支 master
  要提交的变更：
    （使用 "git reset HEAD <文件>..." 以取消暂存）

            修改：     test/checkout_test.txt
  # 所以撤销add操作的命令为
  $ git reset HEAD test/checkout_test.txt
  ```

- 删除文件
  
  当文件已经被commit之后，想要删除它(不再使用git跟踪修改)

  ```bash
  git rm test/rm_test.txt
  git commit -m "remove rm_test.txt"
  ```

## 多分支操作

- 创建分支
  
  > 创建分支实际上是建立了一个指针指向当前的commit，同时HEAD指针指向dev
  
  ```bash
  # -b表示创建并切换
  git checkout -b dev
  # 等效于下面
  git branch dev
  git checkout dev
  # 查看当前分支
  git branch
  ```

- 合并分支
  
  > 在一个分支上面进行修改并**commit之后**，切回master分支，进行合并分支

  ```bash
  # 回到主分支，此时修改没了
  git checkout master
  # 把dev分支合并到master上面去
  git merge dev
  # 删除dev分支
  git branch -d dev
  ```

- 切换分支
  
  > git checkout命令既可以切换分支，也可以撤销修改，为了避免迷惑，在git 2.23版本引入了switch命令。目前Ubuntu18的官方apt安装的git是2.17.1版本，还不支持这个特性。。。

  ```bash
  # 创建并切换
  git switch -c dev
  # 仅切换
  git switch master
```
  
- 分支冲突

  > 回顾上面的分支合并操作，新建的dev分支上面的commit是领先于master的，也就是说在切出dev分支之后，master分支没有进行过commit。当master和deb分支都进行了commit，就要解决分支冲突的问题。

  ```bash
  # 创建新的分支
  git checkout -b dev1
  # 修改一些文件后提交commit(在dev1分支)
  # 切回master分支
  git checkout master
  # 尝试合并
  git merge dev1
  # 出现冲突解决后成功合并，可以看一下此时的时间线
  $ git log --graph --pretty=oneline --abbrev-commit
  *   85aa805 (HEAD -> master) Merge branch 'dev1'
    |\  
    | * ef82add (dev1) 测试冲突
    * | ecaecfb 测试冲突-在master上进行commit
    |/  
    * d401351 增加dev分支
  # 删除dev1分支
  git branch -d dev1
  ```

# Git远程仓库命令

> 没有配置好Github的ssh秘钥的话参考[Github官网教程](https://docs.github.com/cn/github/authenticating-to-github/connecting-to-github-with-ssh)

- 本地仓库关联远程仓库
  
  ``git remote add origin git@github.com:seanleecn/learn_git.git``

  > 这样就添加了一个名字叫做origin的远程仓库

- 本地首次推送远程
  
  ``git push -u origin master``

  > 注意1：2020年10月开始，Github为了XX政治正确，将默认分支的名称从master改为了main，但是本地的默认是在master分支的。建议修改Github的[默认新建分支](https://github.com/settings/repositories)为master。
  > 
  > 注意2：建仓库的时候不要新建readme文件，否则远程就多了一次commit

- 克隆远程仓库
  
  ``git clone xxx``

  从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来了，并且，远程仓库的默认名称(在本地仓库的角度看)是origin

- 查看远程库
  
  ``git remote -v``

- 推送分支
  
  ``git push origin master``

  上面这句话的意思是把本地的master推送到origin这个远程库上

  这里需要**注意**的是，在push命令中并没有指定远程库的分支。前面说过clone下来的库默认把本地的master和远程的master关联起来，但是当有多个分支的时候，就会出现问题。

- 多分支仓库的多人协作
  
  当克隆一个多分支的仓库的时候，只是把master分支拉到了本地

  比如想要在本地dev分支上面继续origin上面的dev分支开发，需要把远程的dev分支拉到本地的dev分支上面

  ``git checkout -b dev origin/dev``

  之后就可以在dev上面修改并推送到origin/dev

  当一个仓库是由多人维护的时候，存在origin的提交比我们本地更靠前的情况(也就是别人已经往origin上面推几个commit)，这时候需要先把远程的修改拉到本地

  ``git pull``

  如果``git pull``提示``no tracking information``，说明本地分支和远程分支的链接关系没有创建，用命令``git branch --set-upstream-to <branch-name> origin/<branch-name>``

- 一个本地库关联多个远程库
  
  举个应用情景的例子：
  
    - 从Github直接clone了一个程序，改啊改啊改啊，当我想要push到Github上面的时候，因为不是fork之后再clone，没有权限push，这时候就需要链接一个新的远程库
  
  ```bash
  # 添加一个新的远程库，注意不能叫origin了
  git remote add my_repo git@github.com:seanleecn/learn_git.git
  # 这时候可以指定push的远程库
  git push github master
  ```

# Git的自定义

- .gitignore文件
  
  ``.gitignore``文件可以忽略一些中间生成文件(比如build目录)，其语法是

  ```
  # 排除所有.class文件:
  *.class
  # 不排除.gitignore
  !.gitignore
  ```