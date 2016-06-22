---
title: git
date: 2016-04-19 14:52:01
tags:
  - git
categories:
  - 工具
photos:
  - 
description: git常用方法和技巧整理归纳
---

* git常用方法和技巧整理归纳

{% asset_img logo.png 图片来源：bing %}

<!--more-->


## .gitconfig 配置

### 常用配置如下

```
[user]
    name = xxx
    email = xxx@yyy.com
[push]
    default = simple
[alias]
    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
[alias]
    st = status
[alias]
    co = checkout
[alias]
    bh = branch
[alias]
    pl = pull
[alias]
    cm = commit -m
[alias]
    ps = push
[alias]
    rlg = reflog
[alias]
    info = config --local -l
```

* `lg`的配置可以非常漂亮简洁地将日志打印出来，使用`git lg`即可
* 其他简写命令同`lg`

### oh-my-zsh
1、推荐使用oh-my-zsh这个shell
* 提供了许多默认的git命令简写配置

2、简写配置路径（MAC系统）
* 可查看家目录下的`.oh-my-zsh/plugins/git/git.plugin.zsh`

## git 常用命令

### 一、修改内容和版本管理
1、丢弃工作区修改
* 修改了内容之后，还未执行`git add`
* 如果需要丢弃工作区修改，那么按照git status的提示恢复
```
# <file>为文件名（全路径）
# 注意命令中的--，如果没有--则变成了切换分支命令
git checkout -- <file>  
```

* 执行后的两种情况（总结就是回到最近一次git add或commit时的状态）
    * 修改内容还没有被放到暂存区，那么撤销修改就回到和版本库一模一样的状态；
    * 修改内容已经添加到暂存区后，又作了修改，那么撤销修改就回到添加到暂存区后的状态。

总之，就是让这个文件回到最近一次git commit或git add时的状态。

2、撤销暂存区修改，放回到工作区
* 执行`git add`后，修改内容提交到了暂存区
* 如果需要将暂存区修改放回到工作区，执行如下命令
```
git reset HEAD <file>
```

3、回退版本库
* 执行了`git commit`后，修改内容被提交到版本库，产生了新的版本hash值
* 回到某一个历史版本
```
# working copy：工作区
# stage/index：暂存区
# Repository：版本库
# HEAD：当前分支版本顶端的指针，也就是在当前分支最近的一次commit提交

git reset --soft HEAD^   # HEAD恢复为上一个版本，相当于撤销了commit操作
git reset HEAD^          # 默认同--mixed
git reset --mixed HEAD^  # HEAD、暂存区恢复为上一个版本，相当于撤销了commit和add两个操作
git reset --hard hash    # HEAD、暂存区、工作区全部恢复为hash对应的版本，相当于撤销了commit、add和文件修改三个操作
```
* 注意
    * git reset --hard是非常危险的操作，会导致最近一次add、commit后的内容全部丢失
    * 如果是误操作，可以使用`git reflog`查找命令执行历史记录，找到想要回退的版本号

### 二、关联远程仓库和首次提交
1、关联远程仓库
* 将本地仓库和远程仓库关联起来
```
git remote add origin git@server-name:path/repo-name.git
```

2、首次提交
* 第一次推送master分支的所有内容
```
git push -u origin master
```
3、后续提交
* 首次提交命令执行后，后续有修改需要推送到远程分支，可以执行下面命令
```
git push origin master  # 可使用zsh缩写命令gp
```

### 三、分支
1、创建并切换到新分支
```
git checkout -b dev

# 等同于下面两条命令
git branch dev  # 新建分支
git checkout dev  # 切换分支
```

2、删除分支
```
git branch -d dev
git branch -D dev  # 不管是否被merge，都直接强行删除该分支
```

3、合并某分支到当前分支
```
git merge dev -m '合并说明'
```

### 四、保存和恢复工作现场
1、保存工作现场
* 临时遇到bug需要修复，就需要新建分支来修复这个bug
* 但是本地的工作还未完成，且不像提交本地修改
* 可执行下面命令来保存当前工作现场，之后工作区就会显示是干净的，于是就可以去新建分支处理bug了
```
git stash
```

2、恢复工作现场
* 方法一（推荐使用）
    * 用git stash pop，恢复的同时把stash内容也删了，即`git stash list`最近一条记录被删除了
* 方法二
    * 用git stash apply恢复，但是恢复后，stash内容并不删除，需要用git stash drop来删除

3、查看工作现场
```
git stash list
```


### 五、标签
1、打标签
```
git tag v0.1  # 为当前版本打标签
git tag v0.1 hash  # 为某个特定版本打标签
git tag -a v0.1 -m "version 0.1 released" hash  # 创建带说明的标签，-a为标签名，-m为说明
```

2、删除标签
```
git tag -d v0.1
```


3、查看所有标签
```
git tag
```


## git 技巧

### 一、git hooks
1、钩子的作用
* 当执行某些git命令时，可以自动调用事先安装的钩子
* 钩子文件中是一系列命令的集合
* 如`pre-commit`这个钩子没有参数，在得到提交消息和开始提交(commit)前被调用。如果钩子执行结果是非零，那么 'git-commit' 命令就会中止执行。

2、安装钩子
* 将pre-commit文件拷贝到项目根目录下的.git/hooks/中
* 修改钩子执行权限
```
chmod 755 .git/hooks/pre-commit
```

3、pre-commit示例
```
# buid gitbook
gitbook build ./

# use node to do something
# echo "do something ...";
# node xxx.js
# echo "done !";

# git
git add -A

exit 0;
```

### 二、忽略文件 .gitignore
1、github准备的配置文件
[https://github.com/github/gitignore](https://github.com/github/gitignore)


### 三、配置别名
1、把暂存区的修改撤销掉（unstage），重新放回工作区的命令别名
```
git config --global alias.unstage 'reset HEAD'
```

2、配置别名文件
* 不加`--global`，则配置位于当前仓库的`.git/config`文件中
* 加了`--global`，则配置位于用户主目录的隐藏文件`.gitconfig`中

> 以上部分内容参考总结自[《廖雪峰的git教程》](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/)