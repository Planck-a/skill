提纲：
---

-初始化
```
git init
git remote add origin 远程仓库地址  //origin是默认主机名
git status
git commit-m "添加新文件"
git push origin master   //添加到远程的master分支
```

-远程仓库到本地仓库进行合并

- git fetch
- git fetch -p
- git merge
- git pull
- git clone

-远程分支到本地的合并

- git branch-a
- git branch-r
- git checkout-b 本地分支 远程分支
- git checkout 远程分支
- git cheacout 本地分支
- git cheakout -b 新分支名
- git branch 新分支名
- git cheacout 本地创建好的分支
- git cheakout HEAD^ -- 要恢复的文件名



提纲的解释
----
-远程仓库到本地仓库进行合并

- git fetch      查看远程仓库
- git fetch -p   查看远程仓库，以远程仓库为标准，本地向远程从仓库对齐
- git merge    
- git pull       相当于fetch+merge
- git clone

-远程分支到本地的合并

- git branch-a    查看远程和本地所有的分支
- git branch-r    查看远程分支
- git checkout-b 本地分支 远程分支      把远程分支拉到本地
- git checkout 远程分支                 
- git cheacout 本地分支
- git cheakout -b 新分支名             创建新分支并切换过去
- git branch 新分支名                  创建新分支
- git cheacout 本地创建好的分支         切换到另一个分支去
- git cheakout HEAD^ -- 要恢复的文件名


问题一：git merge和 git rebase的区别是什么？
---
答：举一个场景来说明，从master分支新建一个分支branch1处理任务，代码写到一半，通知到master分支创建紧急任务分支branch2。这时应先stash暂存，然后切换到master，checkout-b branch2，做完紧急任务后提交到master分支。这时的head指针指向的是branch2，如果我们希望合并branch1和branch2，就有两种合并手段：rebase和merge。merge是新建一个节点分别指向branch1和branch2；而rebase是找到branch1和branch2最前面的那个分支点，把分支点到branch1的所有节点，移植到branch2后边。
