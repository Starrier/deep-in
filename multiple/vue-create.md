---
title: vue-create
date: 2019-03-10 23:48:54
tags: vue
index_img: ../post_img/vue-create.jpeg
---

## 新建 vue 项目

### 安装 Node

因为现在 node 会自带 npm，所以无需自行安装。

安装全局 vue-cli

``` npm
npm install -g vue-cli
```

创建一个基于 webpack 的新项目

```npm
vue init webpack WeighAnchor
```

然后会出现一些可自定义选择的选项，选择完毕即可自动创建。

进入文件目录后，运行 `npm run dev`，就可在 localhost:8080 中访问。
