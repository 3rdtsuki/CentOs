#### docker打包ubuntu镜像

容器技术使用宿主操作系统的底层内核，因此Ubuntu的镜像文件仅包含文件系统。相对于完整的Ubuntu系统镜像的2GB+的大小，Ubuntu容器的镜像文件仅64MB

```sh
docker pull ubuntu:18.04 # 拉取ubuntu18镜像
docker images # 查看所有镜像
docker rmi ubuntu # 删除镜像

docker run -itd --name ubuntu-test ubuntu /bin/bash # 从ubuntu镜像(image)生成一个容器ubuntu-test，并与bash终端进行交互。exit退出命令行

docker ps -a # 展示所有容器(container)
docker start [id]	# 启动容器
docker attach [id] # 使用容器
docker stop [id] # 退出容器
docker rm [id] # 删除容器
docker commit [id] ubuntu01 # 利用已有的容器6f构造两个新容器
docker commit [id] ubuntu02

docker rename exciting_lumiere ubuntu02	# 修改容器的名字
```

#### docker run

从一个镜像生成一个容器，并执行一个命令

```sh
docker run -p [端口映射] -itd --name [容器名] [镜像名] [要执行的命令]
```

例：从ubuntu镜像生成一个容器ubuntu-test1，然后将容器的5000端口映射到主机的5000端口，并运行/bin/bash命令。然后docker ps，可以看到显示了运行容器的端口号PORTS

```sh
docker run -p 5000:5000 -itd --name ubuntu-test1 ubuntu /bin/bash
```

![](../组会/202207/pic/docker ps.png)

#### no space left on device

docker start某容器出现`no space left on device`，表明docker根目录空间不足