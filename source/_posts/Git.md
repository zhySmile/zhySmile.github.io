---
title: Git 常用命令总结
categories: 版本管理
tags: [version management]
---
Git 常用命令总结
<!-- more -->
# 仓库  

> 在当前目录新建一个 Git 代码库  
    
    git init  
* * *
> 新建一个目录，将其初始化为 Git 代码库

    git init [project-name]
* * *
> 下载一个项目和它的整个代码历史  

    git clone [url]
* * *
> clone 到指定目录  

     git clone XXX.git "指定目录"
* * *
> clone 时创建新的分支替代默认 Origin HEAD(master)
    
    git clone -b [new_branch_name] xxx.git
* * *
> clone 远程分支（git clone 命令默认的只会建立 master 分支）,想要 clone 指定的某一远程分支
 
    git checkout -t origin/dev 或 git checkout -b dev origin/dev
* * *
# 添加/删除文件

> 添加指定文件到暂存区

    git add [file or dir]
* * *
>  添加所有文件到暂存区

    git add .
* * *
> 删除指定文件，并且将这个改变添加到暂存区

    git rm --cached [file]
* * *
>  改名文件，并且将这个改名放入暂存区

    git mv [file-original] [file-renamed]
* * *
>  将工作区清空（针对新添加的文件）

    git clean -fd
* * *
>  将工作区清空（针对有改动的文件）

    git checkout .
* * *
# 撤销

>  重置暂存区的指定文件，与上一次 commit 保持一致，但工作区不变

    git reset [file]
* * *
> 重置暂存区与工作区，与上一次 commit 保持一致

    git reset --hard
    git reset --hard HEAD^ 撤消上一次提交
    git reset --hard HEAD~3  撤销三次提交
* * *
>  该条 commit SHA 号之后（时间作为参考点）的所有 commit 的修改都会退回到 git 暂存区中

     git reset --soft [SHA]
* * *
>  该条 commit SHA 号之后（时间作为参考点）的所有 commit 的修改会被直接丢弃， 缓冲区中不会存储这些修改

     git reset --hard [SHA]

    注意：这样的重置是直接在本地的修改，无法提交到远程服务器，如果直接丢弃的内容已经被推到远程服务器上了，则会造成本地和服务器无法同步的问题，即 git reset --hard 只能针对本地操作，不能针对远程服务器进行同样操作。如果从本地删掉的内容没有推到服务器上，则不会有副作用，如果被推到服务器，则下次本地和服务器进行同步时，这部分删掉的内容仍然会回来。（其实这个问题则可以很好的被 git revert 命令解决，使用g it revert [SHA] 号，该命令撤销对某个 commit 的提交，这一撤销动作会作为一个新的修改存储起来，这样，当你和服务器同步时，就不会产生什么副作用。）
* * *
>  撤销已推送的commit

      git revert [SHA]

    通常情况下，上面这条revert命令会让程序员修改注释，这时候程序员应该标注revert的原因，假设程序员就想使用默认的注释，可以在命令中加上 --no-edit 参数。另一个重要的参数是 -n 或者 --no-commit，应用这个参数会让 revert 改动只限于程序员的本地仓库，而不自动进行 commit，如果程序员想在 revert 之前进行更多的改动，或者想要 revert 多个 commit，这个参数尤其好用
* * *
>  撤销已推送的 merge

      git revert 3826574510467c51e174db0f53b83e3542bc1c6f -m 2（例如）
      
    对于 revert merge 的情况，程序员需要指出 revert 这个 merge commit 中的哪一个。通过 -m 或者 --mainline 参数，以及配合一个整数参数，git 就知道到底要 revert 哪一个 merge。（使用 git log 3826574510467c51e174db0f53b83e3542bc1 可以查看那次合并提交的两个提交 SHA 值。例如 自己分支合并到主分支，则第一个 SHA 是主分支的，第二个是自己分支，此处的指令即 revert 自己分支）
* * *
> 修改提交

    git rebase -i HEAD~3

    将看到 HEAD 到 HEAD~3 的提交
    将第一行的“pick”改成想要的指令，然后保存并退出。修改过的提交呈现退出状态。
    用 commit --amend 保存修改。
    现在已经 commit，但是 rebase 操作还没结束。若要通知这个提交的操作已经结束，请指定 --continue 选项执行 rebase。
* * *
> 终止rebase的行动

    git rebase --abort
* * *
# 提交
> 提交暂存区到仓库区

     git commit -m [message]
* * *
> 提交暂存区的指定文件到仓库区

     git commit  [file1] [file2] ... -m [message]
* * *
> 提交工作区自上次commit之后的变化，直接到仓库区

     git commit -a
* * *

> 提交时显示所有diff信息

     git commit -v

 * * *
> 修改最近一次的提交

    git commit --amend
* * *
> 重做上一次commit，并包括指定文件的新变化

     git commit --amend [file1] [file2]...
* * *
# 分支
> 列出所有本地分支

     git branch
* * *
> 列出所有远程分支

     git branch -r 
* * *
> 列出所有分支

    git branch -a
* * *
> 切换分支

     git checkout [branch]
* * *
> 新建一个分支，并切换到该分支

     git checkout -b [branch] 或 git branch [branch]; git checkout [branch]
* * *
> 新建一个分支，指向指定commit

     git branch [branch] [commit]
* * *
> 推送分支

     git push origin [branch]
* * *
> 删除分支

     git branch -d [branch]
* * *
> 删除远程分支

     git push origin --delete [branch]
     git branch -dr [remote/branch]
* * *
> 清理其他人删除的远程

     git remote prune origin
* * *
> 将当前分支移到branch分支上

     git reset --hard [branch]
* * *
> 将一个本地分支重命名

     git branch -m oldbranchname newbranchname
* * *

> 将一个远程分支重命名(假设本地分支和远程对应分支名称相同)

a. 重命名远程分支对应的本地分支

    git branch -m old-local-branch-name new-local-branch-name

b. 删除远程分支

    git push origin  :old-local-branch-name

c. 上传新命名的本地分支

    git push origin  new-local-branch-name: new-local-branch-name
* * *
> 在master分支上做了提交想git pull时要使用这个命令，即merge本地提交到下载下来的分支上面了

    git pull -r
>当刚merge完master 又merge zhanghaiyan/player-info时是不可以直接merge的，要使用此命令

    git merge zhanghaiyan/player-info --no-ff
* * *
> 本地分支合并最新的主分支

    git merge origin/master
* * *

> 本地分支直接同步到远程分支

    git reset --hard origin/master
* * *

# 标签
> 列出所有tag

     git tag
* * *
> 新建一个tag在当前commit

     git tag [tag]
* * *
> 新建一个tag在指定commit

     git tag [tag] [commit]
* * *
> 删除本地tag

     git tag -d [tag]
* * *
> 删除远程tag

     git push origin :refs/tags/[tagName]
* * *
> 查看tag信息

     git show [tag]
* * *
> 提交指定tag

     git push [remote] [tag]
* * *
> 提交所有tag

    git push [remote] --tags
 * * *
> 新建一个分支，指向某个tag

     git checkout -b [branch] [tag]
* * *
# 查看信息
> 显示有变更的文件

     git status
* * *
> 显示暂存区和工作区的差异

     git diff

* * *
> 显示当前分支的版本历史

     git log
* * *
> 显示commit历史，以及每次commit发生变更的文件

     git log --stat
* * *
> 搜索提交历史，根据关键词

     git log -S [keyword]
* * *
> 显示指定文件是什么人在什么时间修改过

     git blame [file]
* * *
> 显示某次提交的元数据和内容变化

     git show [commit]
* * *
> 显示当前分支的最近几次提交

     git reflog
* * *
# 远程同步
> 显示某个远程仓库的信息

     git remote show [remote]
* * *
> 取回远程仓库的变化，并与本地分支合并

     git pull [remote] [branch]
* * *
> 上传本地指定分支到远程仓库

     git push [remote] [branch]
* * *
# 技巧
> 忽略自己对工作目录中的文件修改

     git update-index --skip-worktree <filepath>
* * *
> 列出工作目录中已被忽略修改的文件

     git ls-files -v . | grep ^S
* * *
> 调出某一命令的帮助

     git reset --help (例如：调出reset的帮助)
* * *

> 修改已提交的作者

    git filter-branch --commit-filter \
    'if [ "    GIT_AUTHOR_NAME" = "your old name" ]; then \
    export GIT_AUTHOR_NAME="your correct name";\
    export GIT_AUTHOR_EMAIL= your correct email;\
    export GIT_COMMITTER_NAME=" your correct name";\
    export GIT_COMMITTER_EMAIL= your correct email;\
    fi;\
    git commit-tree "    @"'
* * *

> 假如上面一步执行的过于慢，可以执行下面的方法一个一个修改

    git rebase -i -p <some HEAD before all of your bad commits>

    Then mark all of your bad commits as "edit" in the rebase file. If you also want to change your first commit, you have to manually add it as first line in the rebase file (follow the format of the other lines). Then, when git asks you to amend each commit, do
        git commit --amend --author "New Author Name <email@address.com>"

    edit or just close the editor that opens, and then do
        git rebase --continue

    to continue the rebase.
* * *
> 在执行 rebase 之前自动 stash ,之后再自动 pop

    git config rebase.autoStash true 
    Git 1.8.4之后增加 rebase.autoStash 命令，在2.7引入 --no-autostash 选项取消该标记的功能
* * *
> 查看配置名字和邮箱

    git config --list
* * *