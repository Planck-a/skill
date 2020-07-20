提纲：
---

- 初始化
```
git init    //自己创建一个空文件夹，cd进该文件夹，然后git init 该文件才能具有git管理功能
git status
git add  //添加文件进暂存区
git commit-m "添加新文件"   //将暂存区文件添加到仓库中

git remote add origin [远程仓库地址]  //origin是默认远程仓库主机名
git push origin master   //添加到远程的master分支
```

- 查看提交日志
 ```
 git log
 git reflog  //查看历史的操作日志，比log更详细
 q
 ```

- 版本回退和前进
```

git reset --hard HEAD^     //回到上一个版本
git reset --hard 1094a     //回到指定版本号的版本，可以是向前，也可以是向后
```

- 远程仓库到本地仓库进行合并
  - 远程仓库就是一个中心仓库，比如github，每个员工都可以从这个中心仓库clone到本地
```
- git fetch      查看远程仓库，不会自动的merge；
- git fetch -p   查看远程仓库，以远程仓库为标准，本地向远程从仓库对齐
- git merge    
- git pull       相当于fetch+merge，所以相比起来fetch更为安全，因为可以先比较本地和远程仓库，再决定要不要merge
  git push      命令用于将本地分支的更新，推送到远程主机。默认推送到远端的master；第一次推送的时候加上-u参数，可以把本地master分支和远程的master分支关联起来，以后push就简单了
- git clone
```

- 远程分支到本地的合并
```
- git branch-a    查看远程和本地所有的分支
- git branch-r    查看远程分支
- git checkout-b 本地分支 远程分支      把远程分支拉到本地
- git checkout 远程分支                 
- git cheacout 本地分支
- git cheakout -b 新分支名             创建新分支并切换过去
- git branch 新分支名                  创建新分支
- git cheacout 本地创建好的分支         切换到另一个分支去
- git cheakout HEAD^ -- 要恢复的文件名
```

- git 标签
  - 标签的目的是为了给commit起一个好记的名字，比如今天commit了10次，我希望将第9次commit定义为 v 1.0，就要用到标签
```
git branch   查看本地所有分支
git checkout master   切换到目标分支，不论master或者是dev都可以打标签
git tag v1.0    给当前版本打标签为v1.0
git tag 
git show v1.0  查看该版本对应的commit等详细信息

//如何给之前的某次commit定义一个tag
git log  //先通过log查询某次commit的commit_id
git tag v0.9 f52c633  //给该次commit定义tag
git tag
git show v0.9

```

问题一：git merge和 git rebase的区别是什么？
---
答：举一个场景来说明，从master分支新建一个分支branch1处理任务，代码写到一半，通知到master分支创建紧急任务分支branch2。这时应先stash暂存，然后切换到master，checkout-b branch2，做完紧急任务后提交到master分支。回到branch1后通过git stash pop，可以找回刚刚stash的代码。
这时的head指针指向的是branch2，如果我们希望合并branch1和branch2，就有两种合并手段：rebase和merge。merge是新建一个节点分别指向branch1和branch2；而rebase是找到branch1和branch2最前面的那个分支点，把分支点到branch1的所有节点，移植到branch2后边。
