---
title: git命令
date: 2022-05-28 14:01:00
categories: Github
---
## 创建版本库  

``` bash  
$ mkdir name //创建了一个空目录  
$ git init //变成git可以管理的仓库   


注意：使用Windows，千万不要使用自带的记事本编辑任何文本。原因是Microsoft开发记事本的团队使用了一个非常弱智的行为保存UTF-8编码的文件，他们在每个文件开头家里0xefbbf的字符，你会遇到很多不可思议的问题，比如网页第一行可能会显示一个“？”。明明正确的程序一编译就会报语法错误，等等。

$ git add name.txt //把文件添加到仓库里  
$ git add . //添加当前目录下的所有文件到暂存区  
$ git commit -m “注释” //把文件提交到仓库，-m后面输入本次说明  
$ git status //掌握仓库当前状态  
```

## 删除文件
``` bash  
$ git rm name.txt //删除具体文件  
$ git rm -r name //删除文件目录 
``` 
## 版本回退
``` bash  
$ git log //查看历史记录  
$ git reset --hard HEAD^ //回退到上一个版本  
$ git reset --hard 1234567 //指定回到未来的某个版本，1234567时commit id。  
$ git reflog //用来记录以前的以前提交的版本，可以找到commit id。  
$ git checkout -- name.txt //我理解的就是把name.txt文件在工作区的修改全部撤消，和暂存区保持一致。  
$ git reset HEAD name.txt //把暂存区name.txt或者换成*，就是全部修改撤销，重新放回工作区  
```
## 远程库
``` bash  
$ git clone git@192.168.120.200:bios05/FT-2000Plus-S-KaiFaBan-ACPI-20191125.git //克隆项目  
$ git push -u origin master //把本地缓存区的所有内容推送到远程库上  
$ git push origin master //推送修改的内容，如果把master修改成其他分支，就推送到其他分支  
$ git pull //更新  
```
## 分支管理
``` bash  
$ git branch //查看分支  
$ git branch name //创建分支  
$ git checkout name //切换分支  
$ git checkout -b name //创建并切换分支  
$ git merge name //合并某分支到当前分支  
$ git branch -d name //删除分支  
```
## 遇到冲突时，只能先解决冲突。
``` bash  
$ git merge --no-ff -m "说明" 分支名 //合并分支，强制禁用”fast forward”  
$ git log --graph --pretty=oneline --abbrev-commit //可以看到分支合并图  
$ git stash //储存工作区现场  
$ git stash list //列出来保存的工作现场  
$ git stash pop stash@{数字} //恢复数字的工作现场，并删除list  
$ git stash apply stash@{数字} //功能如上，但是不删除list  
$ git stash drop stash@{数字} //删除list数字的工作现场  
$ git branch -D 分支名 //强行删除某分支  
```
## 标签管理
``` bash  
$ git tag v1.0 //打标记  
$ git show v1.0 //查看标记  
$ git tag -d v1.0 //删除标签  
$ git push origin V1.0 //推送某标签到远程  
$ git push origin --tags //一次性推送本地全部标签  
$ git tag -d V1.0 //先删除本地标签  
$ git push origin :/refs/tags/V1.0 //再删除远程标签 
``` 

## 配置别名
``` bash  
$ git config --global alias.co checkout //把checkout配置为co
```
