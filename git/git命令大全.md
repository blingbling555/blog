**远程库相关**

```
git remote add origin git@github.com:blingbling555/blog.git   // 添加远程库
git remote -v  //查看远程库
git remote rm 远程仓库别名  //删除远程库

git fetch origin 同步远程服务器的数据到本地
git push (远程仓库名) (分支名) //推送到远程服务器
```



**分支相关**

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


分支与远程库结合
git checkout -b test origin/serverfix //新建test分支，其内容同远程分支 origin/serverfix 一致

```

