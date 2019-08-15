Docker —v 挂载的数据卷默认权限是读写，用户也可以通过 `:ro` 指定为只读。


**Node**：
如果直接挂载一个文件，很多文件编辑工具，包括 vi 或者 sed --in-place，可能会造成文件 inode 的改变，从 Docker 1.1 .0起，这会导致报错误信息。所以最简单的办法就直接挂载文件的父目录。

## 数据卷容器

如果有一些持续更新的数据需要在容器之间共享，最好创建数据卷容器。

`sudo docker run -d -v /dbdata --name dbdata traingng/postgres echo Data-only container for postgres` 然后再其他容器中使用 `--volumes-from` 来挂载 dbdata 容器中的数据卷

`sudo docker run -d --volumes-from --name db1 training/postgres`

## 利用数据卷来备份/恢复/迁移数据

可以利用数据卷对其中的数据进行备份/恢复和迁移。

备份

首先使用 `--volumes-from` 标记来创建一个加载 dbdata 容器卷的容器，并从本地主机挂载当前到容器的 /backup 目录，：

`sudo docker run --volumes-from dbdata -v $(pwd):/backup ubuntu tar cvf /backup//backup.tar /dbdata`

恢复

如果要恢复数据到一个容器，首先创建一个带有数据卷的容器 dbdata2

`sudo docker run -v /dbdata --name dbdata2 ubuntu /bin/bash`

然后创建另一个容器，挂载 dbdata2 的容器，并使用 `untar` 解压备份文件到挂载的容器卷中。

`sudo docker run --volumes-from dbdata2 -v $(pwd):/backup busybox tar xvf /backup/backup.tar`