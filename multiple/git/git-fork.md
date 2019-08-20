# git fork

## 保持fork的项目与上游同步

添加上游仓库：

```text
git remote add upstream url
```

fetch 之：

```text
git fetch upstream
```

切换到本地master分支：

```text
git checkout master
```

将upstream/master merge到 本地master分支：

```text
git merge upstream/master
```

同时别忘了push到自己的github仓库：

```markup
git push origin master
```



