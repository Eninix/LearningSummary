# Git的使用

-----

## 1.安装

[下载地址](https://git-scm.com/downloads)：https://git-scm.com/downloads

## 2.首次运行

```shell
# 设置全局用户名和邮箱地址，建议与自己的github帐号一致。
git config --global user.name "用户名"
git config --global user.email "邮箱"
```

## 3.基础指令

```shell
git init # 初始化一个仓库，或者用下面的clone一个仓库
git clone # 后接https地址,可以克隆仓库
git add # 将文件添加到缓存区，进入待提交状态
git commit -m "注释" # 提交当前缓存区所有文件为一个版本
git status # 查看项目的当前状态
git log # 查看当前版本及以前版本的日志,可获取版本号
git log --pretty=oneline # 同上,但每个版本单行显示
git reset --hard 版本号 # 回到曾经的版本
git reflog # 查看历史日志,可获取版本号
git push # 上传至线上仓库
git pull # 从线上仓库同步
```

## 4.分支指令

```shell
git branch # (查看分支)
git branch 分支名 # (创建分支)
git checkout 分支名 # (切换分支)
git branch -d 分支名 # (删除分支)
git merge 需要被分支名 # (合并分支)
```

## 5.文件“.gitigenore”

```
/文件夹名/					 (忽略整个文件夹)
*.zip						(忽略所有.zip文件)
/文件夹名/文件名.后缀		  (忽略某个具体文件)
文件名.后缀					(不忽略某个具体文件)
```

