---
title: git
date: 2016-04-19 14:52:01
tags:
- git
categories:
- 工具
---

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

### 一、恢复文件
1、执行`git add .`后放弃修改
* 执行下面命令
```
# <file>为文件名（全路径）
git checkout -- <file>
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