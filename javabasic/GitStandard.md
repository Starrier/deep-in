# Git

## 开发完成后，新建开发分支

#### 方法一: 在 git 客户端新建提交分支

1. step: 新建提交分支

``` git
git checkout -b newbranch
```

2. step:上传到远端仓库

```
git push 
```

3. step:在 GitHub GUI 界面，点击 new pull request

![](../.gitbook/assets/newPullRequest.png)

![](../.gitbook/assets/chooseBranch.png)

4. setp:将新建 newbranch 提交到主仓库的 develop 分支，同时在 title 的下的文本框中，选择要解决的 issue，形如: issue 已经完成，#20 (20 为解决问题的 issue 号码)。

![](../.gitbook/assets/gitIssue.png)

