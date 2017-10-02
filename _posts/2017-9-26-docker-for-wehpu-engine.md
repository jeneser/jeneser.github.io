---
layout: post
title: "使用docker技术为wehpu构建高可用后端服务"
description: "使用容器化技术(docker)，负载均衡，mongo复制集，docker集群等技术为wehpu部署以express+mongo为基础的高可用后端服务"
categories: [docker]
tags: [wehpu, docker, mongodb, haproxy]
redirect_from:
  - /2017/09/26/
---

> 本文记述了我为wehpu构建高可用后端服务的过程，具有实验性，但最终将会用于实际的生产环境。使用到的技术主要包括:容器化技术(docker)，负载均衡(haproxy)，mongo复制集，docker集群(docker-compose)，express + mongodb等...

* Kramdown table of contents
{:toc .toc}

## 开篇

在以往的开发部署实践中，我是以服务器为单元的。每次有了新项目，都需要在服务器上做一些繁琐而无意义的配置。一段时间后，我可能有了第二个，第三个项目......

我仍然需要重复那些基础的枯燥的安装配置工作，生活还是一成不变。时间宝贵，我想，应该为此做些什么！

大多情况下，我们为了节省有限的服务器资源(money)，可能会将许多不同的项目&演示部署在同一台服务器上。但很快我们会发现，我们的服务器已经被搞的”面目全非“了。限于能力水平的欠缺与运营经验的匮乏，我们并不了解某些操作会潜在怎样的安全威胁。且不说这些，表面上我们有了一些更加棘手的问题:

*端口占用，服务冲突，CPU资源枯竭...*

这显然是有问题的。

## 旧方法的局限

以下是我在搬瓦工的VPS基本配置:

```
SSD: 10 GB RAID-10
RAM: 512 MB
CPU: 1x Intel Xeon
Transfer: 500 GB/mo
Link speed: 1 Gigabit
```

配置不高这是事实，所以，摆在我面前的是如何合理的分配这些有限的资源。在此之上我已经部署了`shadowsocks`，以及两个开源项目的后端服务。而接下来，也就是本次实践，我将会为wehpu小程序(河南理工大学微信小程序)部署后端服务。并决定使用新技术解决传统部署方式中所遇到的问题。

根据以往的部署经验，总结旧方法中的问题如下：

- 本地开发环境的搭建，有时候系统会被我们搞得崩溃，恢复备份或重装系统是很痛苦的。
- 部署或迁移一个服务，都要分别为每个应用运行一些相同的安装、配置命令，这会花费很多无意义的时间。
- 并不是每次安装和迁移都有相同的环境，本地调式环境和线上产品环境不统一或有细微的差别都可能带来不必要的麻烦。
- 配置不当影响到宿主系统所导致的潜藏的安全问题，我们可能全然不知。
- 有时候需要解决应用之间相互冲突的问题。端口占用比较麻烦，同时，开启过多的端口，安全威胁的几率也会随之增高。
- 过多的应用软件与服务占用内存，性能损耗严重。

## 引入docker

![docker](/assets/post-images/docker.png)

什么是Docker? [Docker](https://www.docker.com/)是一个开源的用于实现轻量级的操作系统虚拟化的解决方案。它为程序开发人员和系统管理员来开发、部署、运行应用的一款虚拟化平台。Docker可以让你像使用集装箱一样快速的组合成应用，并且可以像运输标准集装箱一样，尽可能的屏蔽代码层面的差异。Docker 会尽可能的缩短从代码测试到产品部署的时间。

Docker使用了虚拟化技术来实现操作系统和资源的隔离，对于我们来说，容器技术相当于为我们的应用提供了一个隔离的沙箱环境。和平时用的虚拟机(VMware)类似，但不同，众所周知VMware极占内存，而docker是轻量级的。

我比较喜欢docker集装箱的概念。我们有一艘大货轮，用来运输大宗的货物。我们需要用到集装箱，各种各样的货物被装进标准化的集装箱，货物与货物之间不会互相影响。那么我就不需要专门的运送生活用品的船和专门运送工业用料的船了。只要这些货物在集装箱里封装的好好的，我们就可以直接用大船把他们一股脑都运走。docker就是这样，拿现在比较流行的云计算来说，云计算就好比大货轮，docker就是集装箱。

隐约中，我们看到了黎明的曙光。docker带给我们的是：快速的部署，敏捷的开发&测试、高效的系统利用率，以及大幅度的资源节约。那么，我们之前在旧方法中所积累的问题便迎刃而解了。我们需要docker！

## 准备工作

在开始使用docker部署我们的`RESTfull`服务之前，我们先做一些准备工作。

#### 安装docker

在[docker官方网站](https://www.docker.com/)为不同的系统选择相应的社区(docker CE)版本，然后按照说明进行安装。一般情况下，我会优先选择repository的方式，简单快捷。这里是[Docker-ce for Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#install-docker-ce)的参考链接。

#### 镜像加速

工欲善其事必先利其器，安装成功之后，鉴于国内的网络状况，我们需要为docker配置镜像加速地址以加快镜像拉取速度。我使用的是[Docker中国官方镜像加速](https://www.docker-cn.com/registry-mirror)，该镜像库只包含流行的公有镜像，有更快的下载速度和更强的稳定性。当然，也可以选择阿里云或DaoCloud的镜像加速。

使用如下方式进行镜像拉取：

```
$ sudo docker pull registry.docker-cn.com/library/ubuntu:16.04
```

可以将`ubuntu:16.04`换为想要拉取的镜像。拉取之后，镜像名为`registry.docker-cn.com/library/ubuntu:16.04`，有些长，并且使用起来不太方便。下面配置Docker守护进程，去掉拉取是指定的`registry.docker-cn.com`。

修改`/etc/docker/daemon.json`文件并添加上`registry-mirrors`键值。

```
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

保存之后，`sudo service docker restart`重启docker服务使之永久生效。

#### 安装Docker-compose

Dockerfile可以让用户管理一个单独的应用容器，而Docker-compose则允许用户在一个模板(YAML格式)中定义一组相关联的应用容器。例如本文所要构建的服务，由node RESTfull服务容器再加上后端的数据库服务容器以及负载均衡的Haproxy容器组成。

通过Python的pip进行安装：

```
$ sudo pip install -U docker-compose
```

#### 准备开始

然后我们需要稍微了解以下知识：
- `Dockerfile` - Docker可以通过Dockerfile中的指令来自动构建镜像。
- `Docker-compose` - Docker官方编排(Orchestration)项目之一，负责快速在集群中部署分布式应用。
- `Haproxy` - 提供高可用性、负载均衡，以及基于TCP和HTTP的应用程序代理。

我们的应用会分为几个模块分别来构建，最后使用Docker-compose将他们组合起来。下面正式开始。

## 构建node RESTfull服务

![docker](/assets/post-images/nodejs_and_docker.png)

我们来Dockerizing我们的Node.js RESTfull应用程序。为了方便，我把该服务命名为`wehpu-engine`。

关于使用Express.js搭建RESTfull应用程序我们将在其他文章中进行详细分析。在这里我们只需要知道，wehpu-engine使用的是express + mongodb的传统方案，使用npm作为其包管理工具，并且可以通过npm命令`npm start`来快速启动该服务。

构建的大致思路如下：

1. 我们需要一个基础的长期支持版(boron)的node镜像，该镜像可以通过[docker store](https://store.docker.com/)找到。拉取镜像`sudo docker pull node:boron`。
2. 通过`sudo docker run -it --name=wehpu-engine -v /code:/usr/src/app node:boron /bin/bash`命令运行并进入容器。其中`-v`将主机中/code目录中的内容挂载到容器的/usr/src/app目录下。
3. 进入容器之后，安装项目依赖。
4. `exit`退出容器，使用`sudo docker commit [容器id] node/wehpu-engine`命令提交本次镜像修改。方便下次我们直接使用刚才已经修改过的镜像。

我们将应用封在了“集装箱”中，很好的实现了隔离。但看起来好像是更麻烦了呢！相比之前我们还多运行了一些命令。那岂不是每次都要手动做这些工作，实在是不方便。有没有什么办法，只进行一次配置，然后就可以到处使用呢？

答案是肯定的。接下来引入Dockerfile。

引入Dockerfile之前，我们先来思考一下我们的镜像到底是怎么构成的？镜像是容器的基础，每次执行 `docker run`的时候都会指定哪个镜像作为容器运行的基础。而当这些镜像无法直接满足需求时，我们就需要修改这些镜像，并且希望能将其保存下来。我们对容器做的任何修改都会被记录于容器的存储层里，而Docker提供了一个`docker commit`命令，可以将容器的存储层保存下来成为镜像。换句话说，就是在原有镜像的基础上，再叠加上容器的存储层，并构成新的镜像。以后我们运行这个新镜像的时候，就会保留之前我们对容器所做的修改。

Dockerfile其实就是一个文本文件，其内包含了一条条的指令(Instruction)，每一条指令构建一层，因此每一条指令的内容，就是描述该层应当如何构建。Dockerfile清楚透明的描述了我们的镜像是如何构成的，并且结合docker提供的命令`docker build`，我们能自动的完成镜像的构建工作。

先来看看wehpu-engine的Dockerfile：

```
FROM node:boron

# Create app directory
WORKDIR /usr/src/app

# Install app dependencies
COPY package.json .

ARG DEBIAN_FRONTEND=noninteractive
RUN npm install
    # && apt-get update \
    # && apt-get install -y apt-utils \
    # && apt-get install -y tesseract-ocr \
    # && apt-get install -y graphicsmagick \
    # && apt-get install -y imagemagick

# Bundle app source
COPY . .

EXPOSE 3000

CMD [ "npm", "start" ]

```

上面的Dockerfile的说明如下：

- `FROM` 指令制定了该镜像的基础镜像为`node:boron`。
- `WORKDIR` 指定了该镜像的工作目录。其作用是，当我们拷贝文件或运行一些命名时，直接以本路径作为相对路径。
- `COPY` - 将本地的`package.json`文件拷贝到容器的`/usr/src/app`目录。
- `RUN` - 在容器中运行命令，安装项目依赖。
- `COPY` - 拷贝本地当前目录到容器的`/usr/src/app`目录。即是将项目代码拷贝至容器。
- `EXPOSE` - 对外部暴露端口3000
- `CMD` - 运行指定命令，启动项目。

上面每一条指令都会由docker容器执行然后提交为一个镜像，叠在原来的镜像层的上方，最后得到一个拥有许多镜像层叠加的最终镜像。

接下来，只需要用`docker build`命令就能构建出一个新的镜像：

```
$ sudo docker build -t wehpu-engine .
```

我们指定构建的镜像名为`wehpu-engine`，所需的Dockerfile文件在当前目录下`.`。注意，运行命令之后，我们将得到一个名为`wehpu-engine`的新镜像，可以使用如下命令运行该镜像。

```
$ docker run -d -p 4000:3000 wehpu-engine
```

想一想，我们在Dockerfile中直接指定了`COPY`命令，也就是在镜像构建过程中，我们直接将项目代码拷贝进了镜像中。如果项目代码有了更新，或需要修复bug，我们就需要重新构建镜像。当然，docker的理念是“集装箱”，应该讲求应用的隔离。一般情况下，线上代码是较为稳定的版本，在生产环境使用`COPY`命令直接将项目代码打进容器是比较推荐的。当需要更新项目代码时，就直接构建新的镜像。

在开发阶段，我们需要频繁的修改代码，此时，再使用`COPY`命令会很麻烦。推荐使用`VOLUME`数据卷。数据卷是一个可供一个或多个容器使用的特殊目录，可以在容器之间共享和重用数据。在用`docker run`命令的时候，使用`-v`标记来创建一个数据卷并挂载到容器里。在一次run中多次使用可以挂载多个数据卷。也可以在Dockerfile中使用`VOLUME`来添加一个或者多个新的卷到由该镜像创建的任意容器。使用`-v`参数示例如下:

```
$ docker run -d -p 4000:3000 -v /code:/usr/src/app wehpu-engine
```

运行起来之后，本机的4000端口被映射到容器的3000端口，访问`localhost:4000`来测试服务是否可用。

那么，当我们访问本机的4000端口时，本机与容器之间是怎样通信的呢？docker在启动的时候会在宿主机上创建一块名为docker0的网卡，docker容器与本机之间的通信是通过这个网卡进行的。docker0网卡在这里的作用相当于网桥或交换机，负责数据的转发。而端口的转发则是由nat规则负责。在运行容器时，本机端口的数据包通过转发到docker0网卡的网关，docker0再通过广播找到对应ip的目标容器，把数据包转发到容器的端口上便完成了一次通信。另外，容器与外部通信也是使用docker0网卡以及nat规则实现的。

至此我们的node服务已经构建完毕。

有一点瑕疵是，在安装依赖软件时，我遇到了以下Warning：

```
debconf: delaying package configuration, since apt-utils is not installed
```

`apt-utils`包的作用是，所要安装的软件包需要询问有关如何配置时使用到该程序。通过添加一条ARG命令`ARG DEBIAN_FRONTEND=noninteractive`并且安装该程序包`apt-get install -y apt-utils`进行解决，这样，只会报以一次Warning，并且不会对构建过程产生影响。

## 使用docker-compose

node RESTfull服务构建完成之后，每次我们都需手动启动它，接下来还会加入mongodb容器，以及haproxy容器，并且他们之间的联系将变得越来越复杂，每一个容器都要手动启动并与其他容器建立`LINK`的话，有些麻烦。我们希望引入docker-compose来帮我们做这些复杂的工作，我们只需要负责编写docker-compose文件就是了。

创建`docker-compose.yml`文件，开始编写。先将上一步中的wehpu-engine加入到配置中：

```
version: '3.3'
services:
  wehpu-engine1:
    build: .
    ports:
      - 4000:3000
    volumes:
      - .:/usr/src/app
    restart: always
```

简单说一下，我们取服务名为`wehpu-engine1`，使用当前目录下的Dockerfile进行`build`容器，映射本机4000端口到容器的3000端口，挂载本机当前路径到容器的`/usr/src/app`目录，设置为自动重启。通过如下命令运行：

```
$ sudo docker-compose up
```

访问`localhost:4000`验证服务是否启动。

## Mongodb复制集

![mongo replica set](/assets/post-images/mongodb.png)

什么是mongodb复制集？Mongodb复制集由一组Mongod实例(进程)组成，包含一个Primary节点和多个Secondary节点，Mongodb客户端的所有数据都写入Primary，Secondary从Primary同步写入的数据，以保持复制集内所有成员存储相同的数据集，提供数据的高可用。

为什用mongodb复制集？使用单一的节点就可以满足当前的应用，为什么还要用呢？MongoDB复制集可以将数据同步在多个容器中。复制提供了数据的冗余备份，在不同的容器中存储数据副本，提高了数据的可用性，并可以保证数据的安全性，同时复制还允许故障恢复。

怎样使用？接下来，我们将使用docker容器在单一主机上组建我们的复制集。

先来看一张经典的复制集架构图：

![mongo replica set](/assets/post-images/mongo_replica_set.svg)

三个实例节点：一个primary，两个secondary。primary负责处理客户端请求，secondary负责复制主节点上的数据。我们将使用这种简单的一主二从的模式。当然了从节点也不是越多越好，mongodb官方给出的限制是50个副本集。在这里，我们需要这三个就够了。

先来使用Dockerfile构建复制集镜像：

```
FROM mongo:latest

ADD startUp.sh /root/startUp.sh

CMD [ "bash", "/root/startUp.sh" ]
```

在上文中已经介绍了一些基本的Dockerfile命令。这里主要说一下`ADD`命令，将`startUp.sh`文件从当前目录拷贝到容器的`/root/startUp.sh`下。我们仍可以使用`COPY`命令，其实，`COPY`指令和`ADD`指令功能和使用方式类似，只是COPY指令不会做自动解压工作而已。

`startUp.sh`文件，顾名思义，我们用它来配置mongodb容器。其内容如下：

```
#!/bin/bash

set -x

MONGO_LOG="/var/log/mongodb/mongod.log"
MONGO="/usr/bin/mongo"
MONGOD="/usr/bin/mongod"

$MONGOD --fork --replSet wehpu --noprealloc --smallfiles --dbpath /data/db --logpath $MONGO_LOG

echo "Waiting for mongo to come up..."
sleep 15

if [ "$ROLE" == "mongodb-master" ]
then
$MONGO --eval "rs.initiate({
	_id: \"wehpu\",
	version: 1,
	members: [
		{
			_id: 0,
			host: \"mongodb-master:27017\",
			arbiterOnly: false,
			buildIndexes: true,
			hidden: false,
			priority: 1,
			tags: {},
			slaveDelay: 0,
			votes: 1
		},
		{
			_id: 1,
			host: \"mongodb-slave1:27017\",
			arbiterOnly: false,
			buildIndexes: true,
			hidden: false,
			priority: 1,
			tags: {},
			slaveDelay: 0,
			votes: 1
		},
		{
			_id: 2,
			host: \"mongodb-slave2:27017\",
			arbiterOnly: false,
			buildIndexes: true,
			hidden: false,
			priority: 1,
			tags: {},
			slaveDelay: 0,
			votes: 1
		}
	]
})"
fi
tailf /dev/null
```

`startUp.sh`配置了mongo的数据库目录`/data/db`，以及日志目录。并且指定复制集名字为`wehpu`。等到mongo容器启动完成，进行初始化副本集。在这里我直接为`rs.initiate()`传入配置参数来为Primary节点添加另外两个复制集容器。

新建`/builds/mongo`文件夹，将mongodb的`Dockerfile`和`startUp.sh`文件移至该目录下。紧接着更新`docker-compose.yml`文件，添加下列配置：

```
  mongodb-master:
    build: ./builds/mongo
    restart: always
    volumes:
      - /data/mongodb-wehpu/mongodb-master:/data/db
    environment:
      ROLE: mongodb-master
      SLAVE1: mongodb-slave1
      SLAVE2: mongodb-slave2
    hostname: mongodb-master
    container_name: mongodb-master

  mongodb-slave1:
    build: ./builds/mongo
    restart: always
    volumes:
      - /data/mongodb-wehpu/mongodb-slave1:/data/db
    hostname: mongodb-slave1
    container_name: mongodb-slave1

  mongodb-slave2:
    build: ./builds/mongo
    restart: always
    volumes:
      - /data/mongodb-wehpu/mongodb-slave2:/data/db
    hostname: mongodb-slave2
    container_name: mongodb-slave2
```

我们新建了三个容器，分别对应上图中的primary节点和两个secondary节点。通过`build`指定Dockerfile文件路径，使用`volumes`将本机的`/data/mongodb-wehpu/`路径挂载到容器的`/data/db`目录，也就是数据库目录。我们使用这种方式来达到持久化或备份数据到本机的目的。


## 连接mongo

## 测试可用性
并非最终架构

## Haproxy负载均衡

## 压力测试

## 架构思考

## demo

## 总结