<<<<<<< HEAD
# git删除未跟踪文件

```bash
# 删除 untracked files
git clean -f
 
# 连 untracked 的目录也一起删掉
git clean -fd
 
# 连 gitignore 的untrack 文件/目录也一起删掉 （慎用，一般这个是用来删掉编译出来的 .o之类的文件用的）
git clean -xfd
 
# 在用上述 git clean 前，墙裂建议加上 -n 参数来先看看会删掉哪些文件，防止重要文件被误删
git clean -nxfd
git clean -nf
git clean -nfd
```



=======
>>>>>>> 0274e5548c22541f66545f232d76d97d017cd63c
# 显示中文路径

```
git config --global core.quotepath false
```

# 彻底删除大文件

```
git filter-branch --index-filter 'git rm -rf --cached --ignore-unmatch 书籍/'
```



#  分支a想合并3.7.2的代码

你应该是先切到3.7.2，拉最新代码，然后切到你的分支，把3.7.2merge到你的分支，解决冲突，解决完以后向3.7.2发起mr 

# log切换到远程分支**

```
git checkout -b dev origin/dev，作用是checkout远程的dev分支，在本地起名为dev分支，并切换到本地的dev分支

git checkout -b release/api_management origin/release/api_management  第一个是本地分支名 第二个是远程分支


<<<<<<< HEAD
```

# 拉取远程master覆盖本地master

遇到一个问题，本地的master很久没有更新，突然用git pull 拉取会报错，本地git会判断你本地的文件时最新的，用git reset -- hard HEAD都不行

```
 git reset --hard origin/master
```

=======
```

# 拉取远程master覆盖本地master

遇到一个问题，本地的master很久没有更新，突然用git pull 拉取会报错，本地git会判断你本地的文件时最新的，用git reset -- hard HEAD都不行

```
 git reset --hard origin/master
```

>>>>>>> 0274e5548c22541f66545f232d76d97d017cd63c


# git merge简洁用法

```bask
一、开发分支（dev）上的代码达到上线的标准后，要合并到 master 分支
git checkout dev
git pull
git checkout master
git merge dev
git push -u origin master

二、当master代码改动了，需要更新开发分支（dev）上的代码
git checkout master 
git pull 
git checkout dev
git merge master 
git push -u origin dev

https://blog.csdn.net/zl1zl2zl3/article/details/94019526
```



# 取消merge合并**

```
git merge --abort
```

# **git永久保存账号密码，免去git重复输入账号密码操作**

```
//配置全局的用户名及邮箱
git config --global user.name "wangling"
git config --global user.email "2016644182@qq.com"

git config --global credential.helper store
```

# **远程库相关**

```
git remote add origin git@github.com:blingbling555/blog.git   // 添加远程库
git remote -v  //查看远程库
git remote rm 远程仓库别名  //删除远程库

git fetch origin 同步远程服务器的数据到本地
git push (远程仓库名) (分支名) //推送到远程服务器
```



# **分支相关**

```
创建
git checkout -b [分支名]  //创建并切换
git checkout [分支名]     //切换分支


查看
git branch                //全部分支
git branch -v             //各个分支最后一次提交信息
git branch --no-merged    //未与当前分支合并
git branch --merged       // 与当前分支已合并

删除分支
git branch -d [分支名]    //删除分支

git merge <name> 合并某分支到当前分支
git merge --no-ff -m "merge with no-ff" dev //普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。


分支与远程库结合
git checkout -b test origin/serverfix //新建test分支，其内容同远程分支 origin/serverfix 一致

git branch --set-upstream-to <branch-name> origin/<branch-name>//如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令 (git branch --set-upstream-to=origin/dev dev)

```



# **别名**

```
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
$ git config --global alias.st status
```



**git还提供了一个`stash`功能，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作**

> **说明:新增的文件，直接执行stash是不会被存储的**

```
(1) git stash save "save message"  : 执行存储时，添加备注，方便查找，只有git stash 也要可以的，但查找时不方便识别。
(2) git stash list  ：查看stash了哪些存储
(3) git stash pop ：命令恢复之前缓存的工作目录，将缓存堆栈中的对应stash删除，并将对应修改应用到当前的工作目录下,默认为第一个stash,即stash@{0}，如果要应用并删除其他stash，命令：git stash pop stash@{$num} ，比如应用并删除第二个：git stash pop stash@{1}
(4) git stash clear ：删除所有缓存的stash
```



# **版本回退**

```
（1）git reset --hard commit_id，HEAD指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭
 (2) git reset --hard HEAD^ ,上一个版本就是HEAD^,上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。
 
 reset其他参数
 （1）. --soft:git reset --hard <commitId>:撤销本次commit,并且保存add,恢复到暂存区
  (2).--mixed:git reset --mixed,撤销本次commit，并撤销add,恢复到工作区
```



# **查看本地版本库**

```
git log --pretty=oneline ;  commit信息 
git reflog
git log --oneline -3 最近三次commit信息
```

# **撤销**

```
git checkout -- file 丢弃工作区

当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改。两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
```

# **删除**

```
git rm <file> 删除
git rm -r 文件夹/  删除文件夹
```





**git cherry-pick**可以选择某一个分支中的一个或几个commit(s)来进行操作（操作的对象是**commit**）。例如，假设我们有个稳定版本的分支，叫v2.0，另外还有个开发版本的分支v3.0，我们不能直接把两个分支合并，这样会导致稳定版本混乱，但是又想增加一个v3.0中的功能到v2.0中，这里就可以使用cherry-pick了。

```
git cherry-pick <commit id>
```

