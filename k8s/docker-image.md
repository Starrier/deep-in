# Docker Image

Docker 镜像的导入与导出

Docker 镜像的实现原理

docker run -t -i ImageName /bin/bash

-t 让 docker 分配一个伪终端并绑定到容器的标准输入上。 -i 则让容器 的标准输入始终打开

Docker run 的运行包括

 - 检查本地是否存在指定镜像，不存在就从公有仓库下载
 - 利用镜像创建兵启动一个容器
 - 分配一个文件系统，并在只读的镜像层外面挂载一曾可读写层
 - 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去，
 - 从地址池配置一个 IP 地址给容器
 - 执行用户指定的应用程序
 - 执行完毕后容器被终止


 用户既可以使用 docker load 来导入镜像存储文件到本地镜像仓库，也可以使用 docker import 来导入一个容器快照到本地镜像仓库。区别在于，容器快照文件将丢弃所有的历史纪录和元数据信息（即仅保持容器当时快照状态）二镜像存储文件将保存完整的记录，体积也要大。此外，从容器快照文件导入时，可以重新指定标签等元数据信息。