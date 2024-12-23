---
title: Docker入门教程
id: 160
date: 2024-12-03 17:09:59
auther: yutanbird
cover: 
excerpt: 仅为学习记录
permalink: /archives/docker-ru-men-jiao-cheng
categories:
 - linux
tags: 
 - docker
---

# 前言





# 基本概念

先了解一下基本概念：`Image`, `Container`, `Repository`。如果用操作系统来比喻，那么镜像就相当于操作系统的镜像文件（本质就是文件系统），容器相当于正在运行的操作系统本身，仓库包含管理、分发镜像等功能。

镜像不像操作系统的镜像，包含所有文件，而是利用了[Union FS](https://en.wikipedia.org/wiki/UnionFS)的功能，并且docker的文件系统是分层的。镜像也是分层的，后面构建的镜像可以在前面的基础上构建镜像，而不需要重新从头开始进行镜像构建。

容器是镜像的实体，其本质只是运行在操作系统的进程，但是利用[Linux namespace](https://en.wikipedia.org/wiki/Linux_namespaces)可以实现容器有自己的文件系统，网络配置，进程空间等等。值得注意的是，容器的存储空间是会随着容器消亡而消亡的，所以最好使用数据卷或者绑定宿主目录的方式来读写。

仓库常用的有：[Docker Hub](https://hub.docker.com/)，[Github](https://docs.github.com/zh/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Google](https://docs.github.com/zh/packages/working-with-a-github-packages-registry/working-with-the-container-registry)，[Rethat](https://quay.io/repository/)。如果不指定仓库，默认会使用`Docker Hub`的仓库。

# 基本命令

## 镜像相关

### 镜像拉取

本小节主要涉及镜像拉取，镜像操作相关的。

```shell
docker pull [选项] [<IP地址>[:端口]/<用户名>/]<软件名>[:标签]
# 如果没有指定 IP 地址和端口，默认从 docker.io 拉取
# 如果没有指定用户名，对于 docker.io 默认从 library 拉取
# 如果没有指定标签，默认拉取 lastest，这个一般是版本号，也可以是其他的
```

上面是关于如何拉取镜像的，实例如下：

```shell
docker pull ubuntu:18.04  # 默认从 docker.io 上面的用户名 library 拉取镜像软件 ubuntu，其标签为18.04
```

### 镜像查看

```shell
docker image --help  # 查看与镜像相关的命令
docker image ls  # 显示所有镜像
docker image ls -a # 显示所有镜像，包括中间层镜像
docker image ls ubuntu # 类似与 linux 的ls命令，可以过滤显示含有 ubuntu 的镜像

docker image ls -f/--filter # 强大的过滤器，下面举例
docker image ls -f label=<> since=<> before=<>  # 过滤镜像label，某镜像之后构建的，某镜像之前构建的

docker image ls -q  # 只显示镜像ID，可以方便用管道符来传递给其他命令

docker image ls --format "{{.ID}}: {{.Reposity}}"  # format 后面跟的是 Go的模板语法

docker system df # 查看镜像实际占用空间

docker inspect <image> # 查看镜像的一些详细数据
```

- 虚悬镜像，使用`docker image ls`命令查看，偶尔会看到 `<none>` 的镜像，这个是虚悬镜像。其是由于官方对这个镜像进行更改，并且仍然沿用原来的TAG，然后该机器重新`pull`过这个镜像，导致名字与tag用到新拉取的镜像了。一般没什么用了的

```shell
docker image prune # 删除没用的镜像，虚悬镜像就是没用的

docker image rm [选项] <镜像1> [<镜像2> ... ]  # 删除镜像，后面的ID只要能区分机器上唯一镜像即可，不需要完整写出来，也可以删除镜像名
# 镜像ID分为长ID和短ID，默认列出的是短ID

docker image ls --digests  # 镜像摘要
docker image rm <镜像名>@<摘要>  # 更精准的删除镜像

# untagged 和 deleted：由于中间层镜像可能会被其他镜像用到，所以会先对其 untagged （相当于引用计数）如果没有tag了，那么就可以删除了，可以类比GC机制

docker image rm $(docker image ls -q redis)  # 组合命令来删除镜像
```

### 镜像制作

```shell
# 加入已经进入了一个镜像，并且进行了一些更改，我们就可以commite这个镜像
docker diff webserver  # 查看与webserver的区别

docker commit --author "Yutan<bird@lmzyoyo.top>" --message "提交新的内容" webserver nginx:v2 
# 上面的命令是可以将当前的操作保存到新的镜像

docker tag <src image> <dst image>  # 重新标记一个镜像
```

**注意**：尽量别用`docker commit`这会导致制作的镜像臃肿，应用`Dockerfile`来替代

## 容器相关

### 容器运行

```shell
docker run -it --rm ubuntu:18.04 bash
docker container run -it testothers
# -it: 是两个参数，-i是交互，-t是终端，也可以分开写
# --rm 容器退出后就删除
# ubuntu:18.04 用的ubuntu18.04镜像
# bash:容器启动后运行的程序，这里运行的bash
# run完之后，可以exit或者ctrl+D退出容器，如果只有一个终端的容器，那么退出容器就会停止。
# 如果想退出容器但是不关闭，就ctrl + P + Q

docker exec -it webserver bash  # 进入正在运行的镜像里，并且用终端进行交互，webserver是镜像名称

docker container start <container>  # 将一个已经终止（exited）的容器重新启动
docker container stop <container> # 将一个容器停止
docker container ls -a  # 查看所有容器，包括终止状态的
```

一些参数：

- `-d`：以后台运行，不会将容器中的终端输出输出到宿主机屏幕上面
- 启动后运行的程序：如上述bash，就会默认运行bash程序，也可以写成`docker run -it --rm ubuntu:18.04 My_command arg1 arg2 arg3...`，也就是后面的所有内容，都会放到容器启动后自动运行的命令和参数

### 导入导出

```shell
# 导入和导出镜像快照
docker export <container> > <image>  # 将容器导出成镜像快照
docker import <container> <image> # 将容器导入成镜像，容器可以从网上来，也可以用管道符
docker import <URL> <image> # 从网上下载
cat <container file> | docker import - <image>  # 将<container file>导入到镜像
```

### 容器删除

```shell
docker container rm <container> # 删除容器，如果加上-f参数，可以给运行中的容器发送一个 SIGKILL 信号
docker container prune # 清除终止状态的容器
```

## 仓库相关

### 登录登出

```shell
docker login  # 登录仓库
docker logout # 登出仓库
```

### 搜索镜像

```shell
docker search <key words> # 用关键字搜索镜像
```

### 拉取和推送

```shell
docker pull <image:tag> # 拉取你需要的镜像到本地
docker push <image:tag> # 推送镜像到仓库
```

### 搭建仓库

```shell
# 使用官方的docker-registry来搭建个人仓库
docker run -d -p 5000:5000 --restart=always --name registry registry -v /opt/data/registry:/var/lib/registry
# 会把上传的镜像保存到宿主机/opt/data/registry目录，因为仓库会被默认创建在容器的/var/lib/registry目录下


# 把镜像标记成私有仓库的
docker tag IMAGE[:TAG] [REGISTRY_HOST[:REGISTRY_PORT]/]REPOSITORY[:TAG]  # 私有仓库的地址为 REGISTRY_HOST/PORT
docker push # push 上面的镜像，会被自动push到HOST上面
```

其默认使用`HTTPS`方式，因此有些地方没有`HTTPS`则需要按照下面的方法配置

```json
// 更改 /etc/docker/daemon.json，没有就创建新文件
{
    "registry-mirrors": [  // 这里是其他源
        "https://hub-mirror.c.163.com",
        "https://mirror.baidubce.com"
    ],
    "insecure-registries": [  // 这里也是其他源
    "192.168.199.100:5000"
  	]
}
```

# Docker File

## 构建镜像

假设现在已经写好了`Docker File`，当然，如何写在下节才会提到。开始构建它吧

```shell
docker build -t <image name>:<tag> .

# 上述命令是由如下组成
docker build [选项] <上下文路径/URL/->
```

-   上下文路径：上述命令的上下文路径是`.`，这并不是在指定`Dockerfile`所在路径。解释如下：

    >   docker是基于C/S模式，也就是，本机运行了一个docker引擎（服务器），然后在终端（客户端）使用命令（如 docker, docker build）本质是在调用引擎的API。那么很容易知道，`docker build`命令其实是引擎在执行该命令，所以就需要引入上下文概念。

-   上下文概念：上下文的意思是告诉docker引擎，你默认访问的路径在哪。可以从http协议中获取一些解释，比如我们访问`http://example.com`，那么就是访问该服务器下的一个默认目录（本质默认访问index.html等），假设默认目录是`/opt/www/example` ，那样就是访问服务器`/opt/www/example/index.html` 。那我们也可以访问`http://example.com/../`，那就是访问`/opt/www/index.html`，当然这一般会被服务器禁止的。

同理，本质是告诉docker引擎，你访问的上下文目录在哪里，从下面一个例子来深化了解，假设下面是文件路径，但是如果不涉及路径只涉及一些命令的docker file，可以没有上下文路径。

```
/home/example
			- test
				- app
				- Dockerfile
				- test.py
				- back.json
			- back.json
```

我们执行一些命令

```shell
cd /home/example/test
docker build -t test:v1 .

# 加入Dockerfile中有如下两条不同的命令，由于指定的上下文路径是 /home/example/test ，引擎默认访问的路径也是 /home/example/test
COPY ./package.json /app/  # 复制 /home/example/test/back.json
COPY ../package.json /app/ # 复制 /home/example/back.json
```

经过上述命令，应该能够理解上下文路径了，下面继续学习`docker build`的一些用法

```shell
# 可以看到，上述命令中提到可以用 URL，也就是可以从URL构建，如下
docker build -t hello-world https://github.com/docker-library/hello-world.git#master:amd64/hello-world
# 上述命令就是从GitHub仓库中的master分支构建，构建目录是/amd64/hello-world
# 除此之外，如果下载的是一个压缩包，那么docker build会自动解压这个压缩包并构建

# 还注意到可以是一个减号'-'，这表示从标准输入输出构建
docker build - < Dockerfile
cat Dockerfile | docker build -
docker build - < context.tar.gz
# 上述命令是从标准输入输出构建，所以没有上下文，就没法用跟路径相关的命令，如果 COPY
```

关于`Dockerfile`所在的位置，同样用上述例子说明

```shell
cd /home/example/test
docker build -t test:v1 .
# 当前路径是/home/example/test，那么docker build会默认从当前路径寻找Dockerfile，注意不是从上下文路径寻找，只是这里恰好相同
# 可以用-f/--file参数指定Dockerfile文件
cd /home/example
docker build -t test:v1 ./test -f ./test/Dockerfile  
```

镜像文件一般如下所示，都是用dockerfile命令组成，注意不要想当然认为这里面写的是linux命令，其支持`\`符号来换行书写

```dockerfile
FROM ubuntu
RUN apt update \
    && apt upgrade
CMD ["hello_world"]
...
```

## 编写文件

这一节来学习下如何编写`Dockerfile`文件，官方的文档：https://docs.docker.com/reference/dockerfile/

**注意：**要区分好容器运行时以及镜像构建运行时两个阶段

### 打包依赖文件

依赖文件相关的命令有两个：`ADD`和`COPY`，下面是两个的用法，后面给出两者区别

```dockerfile
COPY [选项] <源路径1> ... <目标路径>  # 支持 * ? 通配符
COPY [选项] ["<源路径1>", ... , "<目标路径>"]
# 从上下文复制文件
[选项]：
	--from=<image|stage|context>  # 默认是从上下文目录复制文件，也可以改成从其他镜像或者stage复制，stage可以参考https://docs.docker.com/build/building/multi-stage/
	--chown=<user>:<group>  # 改变文件元数据中的所有者
	--chmod=<perms>  # 改变元数据中的权限，三位数字表示，比如777
	--link  # 请看官方介绍，通俗来说就是把文件复制到独立的image层，在前一层image使用链接的方式引入。构建好的镜像无异，只有构建过程被加快
	--parents # 是否把父目录引入，比如下面例子，默认是false
		COPY ./x/a.txt /no_parents/  # /no_parents/a.txt
		COPY --parents ./x/a.txt /parents/ # /parents/x/a.txt，默认创建了x目录
	--exclue=<pate>...  # 后面如果用了通配符，可以用这个设置排除文件，比如
		COPY --exclude=*.txt --exclude=*.md hom* /mydir/  # 排除了所有的txt和md文件

ADD [选项] <源路径1> ... <目标路径>
ADD [选项] ["<源路径1>", ... , "<目标路径>"]
# 从网络/上下文复制文件，源路径可以是url了，比如GitHub路径（如果是压缩包，就不推荐这种方法，更推荐RUN命令）
[选项]:
	--keep-git-dir=true  #  将.git文件保留，不加就是false
	--checksum=<hash> # 检查是否是这个哈希
	--chown # 改变所有者
	--chmod # 改变权限，下载的默认是600权限
	--link # 同COPY
	--exclude # 同COPY

```

### 容器运行时

涉及容器运行的命令有：`CMD`，`ENTRYPOINT`

在此之前，先明确一点容器和虚拟机的关系，虚拟机是为了运行一个完整的机器，而容器是为了运行一个完整的应用程序。包括前面提到的`Ubuntu`镜像，其默认是运行了`bash`这个程序，如果容器的程序退出，那么容器也会退出的。因此，容器中的应用程序一般都是前台运行，如果前台退出了，容器就会自己退出。比如利用`systemctl`服务形式来运行虚拟机中的应用程序，那么是不可取的。

```dockerfile
# CMD有两种形式的用法，如果参数涉及空格，最好用shell形式
CMD ["可执行文件", "参数1", "参数2", ...] # exec形式
CMD 可执行文件 参数1 参数2 ...# shell形式

# ENTRYPOINT有两种形式的用法
ENTRYPOINT ["可执行文件", "参数1", "参数2", ...] # exec形式
ENTRYPOINT 可执行文件 参数1 参数2 ... # shell形式

# 在讲这CMD和ENTRYPOINT的关系时，先引入另一种用法
CMD ["参数1", "参数2", ...]  # 这里没有可执行程序
# 这种用法本质上是：ENTRYPOINT CMD两种组合，CMD只提供参数，可执行文件在ENTRYPOINT中提供

# 注意：不管哪个，更加推荐 exec 形式，防止有些数据在宿主机被求值，比如用了环境变量的情况，会导致宿主机的环境变量被用了，而不是虚拟机的环境变量被使用
```

现在来讲讲这两个命令有什么用，加入我们构建的容器应用程序如下

```shell
mycommand -i <input> -o <output> # 这里是我们自己的一个可执行程序，接收两个参数
```

现在来写`Dockerfile`，v1版

```dockerfile
FROM ubuntu:18.04
RUN apt update \ # 可以使用\换行写，并且这里写成 && 连接两条命令，请看后面的RUN命令部分
	&& apt install -y mycommand  # 注意这里的 -y 参数，因为构建的时候，是没法交互的，所以一条命令应该运行完整
CMD ["mycommand", "-o", "<output>"]  # 注意这里我没有指定输入参数 -i <input> ，我想在运行容器时由用户指定
```

`docker build -t mycmd .` 构建上述容器之后，可以开始使用 `docker run mycmd`来直接运行容器，并且输入输出都会显示在宿主机的终端。该容器仅仅运行一个命令，命令退出，容器就会退出。而且，我们运行这个容器，就跟我们运行一条终端命令一样，启动了一个虚拟机。一切发生的都是那么的迅速，可以看出容器技术的轻量级了。

上述可以说构建了一个成功的容器，但是没有达到我们的目的，毕竟我们还有`-i <input>`参数没有指定。现在想当然的，前面的容器运行提到了，只需要运行`docker run mycmd -i <input>`，会不会成功呢？那么会发生下面的错误

```shell
docker: Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: exec: "-i": executable file not found in $PATH: unknown.
```

可以看出，容器中的机器把`-i`当成可执行文件了，那我们试试`docker run mycmd mycommand -i <input> -o <output>`是不是就能解决问题呢？答案是是的，但是有个问题是，每个人使用这个容器，都需要把完整命令写出来，如果写错了呢？所以，这里可以使用`ENTRYPOINT`来解决这个问题，改写`Dockerfile`成v2版，如下所示

```dockerfile
FROM ubuntu:18.04
RUN apt update \
	&& apt install -y mycommand
ENTRYPOINT ["mycommand", "-o", "<output>"]
```

继续构建并且运行，然后发现，可以使用`docker run mycmd -i <input>`，这是由于，后面加入的`CMD`会自动接到`ENTRYPOINT`后面。那么可以着重区分两者的特性了

-   `CMD`：可以指定多个，但是只有最后一个才有效，如果在`docker run`后面指定了新命令，那么`CMD`就无用了，比如在v1版本中执行`docker run mycmd echo "hello world"`，那么会发现容器就打印了`hello world`，跟之前写的`mycommand`没有任何关系，因为其被覆盖了。
-   `ENTRYPOINT`：通常只有一个，并且一定会被执行，对于服务比较复杂的程序，通常会写一个脚本，然后通过`COPY`命令拷贝到容器层，然后运行该脚本，再在`docker run`中传入需要的参数。并且可以通过`docker run --entrypoint`来覆盖`dockerfile`中的

### 环境变量

涉及环境的命令有：`ARG`，`ENV`

```dockerfile
ENV <key>=<value> ...
ENV <k1>=<v1>
ENV <k2>=<v2>
...
# 或者
RUN k1=v1 run_cmd # 仅在构建时使用

ARG <key>[=<value>] # 注意这里的value是可选的，如果填了就是默认值，下面会讲到这个用法
ARG <k1>[=<v1>]
ARG <k2>[=<v2>]
```

-   `ENV`：设置的环境变量可以在`ADD, COPY, ENV, EXPOSE, FROM, LABEL, USER, WORKDIR, VOLUME, STOPSIGNAL, ONBUILD, RUN`以及容器运行时能够使用到，在其他命令可以写成`$key`的方法来使用这个`key`对应的`value`
-   `RUN k1=v1`：仅仅在构建过程中才会用到的环境变量
-   `ARG`：在构建的过程中才会用到，并且如果没有指定默认参数，就需要通过 `docker build --build-arg key=value` 来指定，如果指定了默认参数，也可以用这个方式来更改。不建议在此保存密钥信息，并且如果在`FROM`之前的`ARG`，只能在`FROM`中使用。
-   `ENV`和`ARG`可以有相同的key，但是作用范围请在[官网](https://docs.docker.com/reference/dockerfile/#scope)查看

### 基础镜像

看了上述内容会发现，每个`Dockerfile`中，必定有一个`FROM`命令，这表示该镜像是以哪个镜像为基础的

```dockerfile
FROM [--plateform=<plateform>] <image>[:<tag>/@<digest>] [AS <name>]

# --plateform 指定特殊平台的镜像，比如 linux/amd64 linux/arm64 windows/amd64
# image 镜像名字
# :<tag> 镜像的标签，通常是版本号，没有就默认是latest
# @digest 镜像的摘要信息
# AS name 可以类比成python中的 import aLongLengthNameLib as alib中的as，可以在后续使用这个镜像，比如COPY命令

FROM ubuntu AS ub
...
COPY --from=ub /root/a.txt /root # 从前面定义的镜像拷贝文件
```

-   多个`FROM`的用法（17.05版本以后支持），设想一个场景下，写好了go，rust等代码，但是容器程序只需要这些代码生成的可执行文件，所以在构建镜像的时候，需要先下载这些语言的编译器，那么这里其实就会产生一层镜像。在语言编译器下好之后，编译的中间文件在运行时又不需要，就会导致镜像冗余。因此，可以使用多个`FROM`的方式来构建。如下所示

```dockerfile
# 编译阶段
FROM golang:1.10.3
COPY serve.go /build/
WORKDIR /build
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 GORAM=6 go build -ldflags '-w -s' -o server

# 运行阶段
FROM scratch  # 第二个FROM才会起作用，第一个会被忽略
COPY --from=0 /build/server /  # 0表示第一个阶段的镜像，可以理解为第二个FROM之前的这个镜像
ENTRYPOINT ["/server"]
```

下面介绍一个最基本的镜像，叫做`scratch`，这是最基本的文件系统，不包含任何文件或者PATH等，因此运行只能通过绝对路径来运行，这通常在发布软件的时候非常有用，可以保证镜像极度轻量级。

### 构建镜像

这部分涉及命令：`RUN`

```dockerfile
RUN [选项] 命令1 ... && 命令2 ... && ... ## shell形式
RUN [选项] ["命令1", ... ] # exec形式

# 通常使用shell形式，可以分成多行写，比如
RUN <<EOF
apt-get update
apt-get install -y curl
EOF
# 或者
RUN apt-get update \
 && apt-get install -y curl
 
[选项]
	--mount
		
	--network
	
	--security
```

值得注意的是，一条`RUN`命令就会构建一层镜像，所以尽量少用

### 元数据

为镜像添加元数据：`LABEL`，什么是元数据了，比如文件的元数据就是文件创建时间、文件作者、文件权限等

```dockerfile
LABEL <key1>=<value1> ...
# 请参考https://github.com/opencontainers/image-spec/blob/main/annotations.md
```

### 匿名卷

```dockerfile
VOLUME <路径>
VOLUME ["<路径1>", "<路径2>", ...]

docker run -v <宿主机目录>:<虚拟机挂载点> # 也可以通过-v参数来指定运行的容器的挂载点，在VOLUME中指定是为了防止运行容器时忘记
```

`VOLUME`的作用是在容器上设定一个挂载点，这个挂载点可以持久化（也就是不随容器的终止而终止），并且会在宿主机上默认创建一个文件作为这个挂载点的物理位置，可以通过`docker inspect <image>`查看，其一般默认在`/var/lib/docker/volumes/`下面

### 端口

```dockerfile
EXPOSE 端口[/协议(tcp/udp)] ...
# EXPOSE只是起到一个声明作用，写这个镜像文件的人告诉编译这个镜像文件的人和运行容器的人，容器会用到这些端口
# 实际上端口映射需要通过docker run -p/-P 来进行
docker run -p <宿主机端口>:<容器端口>... # 将容器端口一一对应到宿主机端口
docker run -P # 开所有的容器端口，并将其随机一一映射到宿主机的端口，通过docker ps -a 查看
```

### 工作目录

```dockerfile
WORKDIR <path>
# 指定接下来的工作目录，可以指定多个，每次指定一个就换一个，如果没有指定，你运行容器的时候会在'/'目录，如果指定了，运行容器也会默认跳到指定的最后一个目录
```

### 指定用户

```dockerfile
USER <用户>
# 同WORKDIR一样，不过是指定用户而已，后续层也好，容器运行也好，都默认是这个用户，但是这个用户必须是存在的，所以需要用RUN来创建用户
# 如果需要更换用户来执行，就需要gosu

USER test
# 下载 gosu
RUN wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.12/gosu-amd64" \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true
```

### 设置shell

```dockerfile
SHELL ["shell程序", "参数"]
# 执行RUN ENTRYPOINT CMD的shell程序，默认是 /bin/sh -c 来执行
# 比如 RUN apt update 可以看成 /bin/sh -c "apt update"
```

### 其他命令

还有如下命令，可以在官网看：https://docs.docker.com/reference/dockerfile/

-   `HEALTHCHECK`
-   `MAINTAINER`
-   `ONBUILD`
-   `STOPSIGNAL`

