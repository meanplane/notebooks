## docker

> **Docker 属于 Linux 容器的一种封装，提供简单易用的容器使用接口**

Docker 将应用程序与该程序的依赖，打包在一个文件里面。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。有了 Docker，就不用担心环境问题。

总体来说，Docker 的接口相当简单，用户可以方便地创建和使用容器，把自己的应用放入容器。容器还可以进行版本管理、复制、分享、修改，就像管理普通的代码一样。



### docker的用途

1. 提供一次性的环境。**比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。

2. 提供弹性的云服务。**因为 Docker 容器可以随开随关，很适合动态扩容和缩容。

3. 组建微服务架构。**通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。



### 安装

```shell
# 版本
$ docker version
# 或者
$ docker info

# service 命令的用法
$ sudo service docker start

# systemctl 命令的用法
$ sudo systemctl start docker
```



### 原理

Docker 是一个client-Server结构的系统. Docker守护进程运行在主机上. 然后通过Socket链接从客户端访问, 守护进程从客户端接受命令并管理运行在主机上的容器. ==容器是一个运行时环境==, 就是我们前面说到的集装箱

### image(镜像) 文件

> docker 把应用程序及其依赖, 打包在image文件里面.只有通过这个文件,才能生成Docker容器. image文件可以看做是容器的模板. Docker根据image文件生成容器的实例. 同一个image文件, 可以生成多个同时运行的容器实例.

image 是二进制文件。实际开发中，一个 image 文件往往通过继承另一个 image 文件，加上一些个性化设置而生成。举例来说，你可以在 Ubuntu 的 image 基础上，往里面加入 Apache 服务器，形成你的 image。

```shell
# 列出本机的所有 image 文件。
$ docker image ls

# 删除 image 文件
$ docker image rm [imageName]

# 删除镜像
$ docker rmi -f [镜像ID]
$ docker rmi -f 镜像1:TAG 镜像2:TAG 
$ docker rmi -f $(docker images -qa)
```

image 文件是通用的，一台机器的 image 文件拷贝到另一台机器，照样可以使用。一般来说，为了节省时间，我们应该尽量使用别人制作好的 image 文件，而不是自己制作。即使要定制，也应该基于别人的 image 文件进行加工，而不是从零开始制作。

为了方便共享，image 文件制作完成后，可以上传到网上的仓库。Docker 的官方仓库 [Docker Hub](https://hub.docker.com/) 是最重要、最常用的 image 仓库。此外，出售自己制作的 image 文件也是可以的。

### 容器文件

Image 文件生成的容器实例, 本身也是一个文件, 称为容器文件. 也就是说, 一旦容器生成, 就会同时存在两个文件: image文件 和 容器文件. 而且关闭容器不会删除容器文件. 而且关闭容器并不会删除容器文件, 只是容器停止运行.



```shell
# 列出本机正在运行的容器
$ docker container ls

# 列出本机所有容器，包括终止运行的容器
$ docker container ls --all

# 终止运行的容器文件,依然会占据硬盘空间
# 删除容器文件
$ docker container rm [containerID]
```





