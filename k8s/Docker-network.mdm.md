查看映射端口配置

使用 `docker port ` 来查看当前映射的端口配置，也可以查看到绑定的地址

`docker port ImageName ImagePort`

## 容器互联

查看容器的 name 可以使用 docker ps -l 也可以使用 docker inspect

`docker inspect -f "{{ .Name }}" ImageID`

**Node** 

在执行 docker run 的时候，如果添加 -rm 标记，则容器在终止后会立即删除。注意， --rm 和 -d 不可以同时使用


使用 `--link` 参数可以让容器之间安全的进行交互。

1. 先创建一个新的数据库容器

`docker run -d --name db training/postgres`

2. 新建一个 web 容器，并将它连接到 db 容器

`docker run -d -P --name web --link db:db training/webapp python app.py`

--link 参数的格式为 --link name:alias，其中 name 是要链接的容器的名称，alias 是这个连接的别名。

Docker 通过两种方式为容器公开连接信息

- 环境变量
- 更新 `/etc/hosts` 文件


## 容器的访问控制

容器的访问控制，主要通过 Linux 上的 Iptables 防火墙来进行管理和实现。`Iptables` 是 linxu 上默认的防火墙软件，在大部分发行版中都自带。

容器想要访问外部网络，需要本地系统的转发支持。再 Linux 中，检查转发是否打开。

```shell
sysctl net.ipv4.ip_forward

net.ipv4.ip_forward=1
```

如果为 0 ，则说明没有开启转发，需要手动打开

`sysctl -w net.ipv4.ip_forward=1`

如果再启动 docker 服务的时候设定 `--ip-forward=true`,docker 就会自动设定系统的 `ip_forward=1`.

### 容器之间的访问

- 容器的网络拓扑是否已经互联。默认情况下，所有容器都会被连接到 docker0 网桥上
- 本地系统的防火墙软件 —— iptables 是否允许通过。

访问所有端口
当启动 Docker 服务时候，默认会添加一条转发策略到 iptables 的 FORWARD 链上。策略为通过（ACCEPT）还是禁止（DROP）取决于配置--icc=true（缺省值）还是 --icc=false。当然，如果手动指定 --iptables=false 则不会添加 iptables 规则。

可见，默认情况下，不同容器之间是允许网络互通的。如果为了安全考虑，可以在 /etc/default/docker 文件中配置 DOCKER_OPTS=--icc=false 来禁止它。

启动容器（docker run）时使用 --link=CONTAINER_NAME:ALIAS 选项。Docker 会在 iptable 中为 两个容器分别添加一条 ACCEPT 规则，允许相互访问开放的端口（取决于 Dockerfile 中的 EXPOSE 行）

`sudo iptables -nL`

映射容器端口到宿主主机的实现

默认情况下，容器可以主动访问到外部网络的连接，但是外部网络无法访问到容器。

容器访问外部的实现

容器所有到外部网络的连接，源地址都会被 NAT 成本地系统的 IP 地址。这是使用 iptables 的源地址伪装操作实现的。查看主机的 NAT 规则：`sudo iptables -t nat -nL`。

外部访问容器的实现

可以再 docker run 时，使用 -p 或者 -P 来启用。无论是哪种情况，其实也是再本地的 iptables 的 nat 表中添加相应的规则。

### docker0 网桥

Docker 服务默认创建一个 docker0 接口的 IP 地址和子网掩码，让主机和容器之间可以通过网桥相互通信，它还给了 MTU

目前 Docker 网桥是 Linux 网桥，用户可以使用 brctl show 来查看网桥和端口连接信息。

###  自定义网桥

除了默认的 docker0 网桥，用户也可以指定网桥来连接各个容器。


