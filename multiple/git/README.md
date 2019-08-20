---
description: Multi - Branch Development
---

# Git

## Fork

Fork a copy of the original warehouse to your own warehouse

## Clone

Clone  the repository you need  to develop in mutil-version,,for example:



```
git clone 
```

## Branch 

Develop new branches locally using git bash or git GUI

```text
// Method 1: create and checkout
 git checkout -b iss53  

// Method 2: create,then checkout
 git branch iss53
 git checkout iss53
```

```
## 第一次进行 fork 之后的同步
>git remote -v
 git remote add upstream 原仓库地址
 git remote -v
 git fetch upstream
 git checkout master
 git merge upstream/master
此时，本地仓库已经和原仓库同步，但是Github上并没有同步

**方法一**
> git push

**方法二**
> 在github页面进行分支merge 对自己的仓库发起 pull request

## git 删除远程分支

#### 方法一
**例如删除分支 Starrier ，origin 后面一定要加空格**
>git push origin :Starrier 
 原因：发送空分支 相当于删除分支

#### 方法二
> git push origin --delete serverfix
命令做的只是从服务器上移除这个指针。 Git 服务器通常会保留数据一段时间直到垃圾回收运行，所以如果不小心删除掉了，通常是很容易恢复的
  
## git 本地分支并关联远程分支

### 查看本地分支与远程分支的映射关系
** 关联的分支名称不一定要和远程分支名同名**
>git branch -vv

### 本地尚未创建分支

#### 方法一
**在本地新建分支x，并自动切换到该本地分支x.此方法建立的本地分支会和远程分支建立映射关系**
>git checkout -b 本地分支名x origin/远程分支名x

#### 方法二
**在本地新建分支x，不会自动切换到该本地分支x，需要手动checkout。此方法建立的本地分支不会和远程分支建立映射关系**
>git fetch origin 远程分支名x:本地分支名x

### 本地已创建分支

#### 方法一
> git branch -u origin/远程分支名

#### 方法二
>git branch --set-upstream-to origin/远程分支名

### 本地分支取消与远程分支的关联
>git branch --unset-upstream



```



