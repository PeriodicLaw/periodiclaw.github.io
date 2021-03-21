---
title: docker安装与配置
date: 2020-08-18 17:48:47
category: 技术工具
---

因为工作上的原因，时不时会遇到这样的情况：需要某些旧版本的库，或是文档里写的库在自己的软件仓库上找不到（说的就是你，Ubuntu）。
这个时候开一个虚拟机显然是不太方便的选择；使用docker，我们可以做到将库环境隔离开来，需要工作的时候切换到docker里即可，而不需要切换系统或是开个虚拟机。

这篇文章记录下docker的安装和配置的过程。

# 安装

首先安装docker并启动其服务。

```bash
sudo pacman -S docker
sudo systemctl start docker.service
sudo systemctl enable docker.service
```

然后，使用`sudo usermod -aG docker $USER`并重启。再输入`docker info`，如果能够看到一长串机器配置信息，那么说明安装完成。

# 配置

docker下载非常慢，所以我们需要使用阿里云镜像，见<https://blog.csdn.net/wohaqiyi/article/details/89335932>进行配置。

比如我们希望有一个独立的Ubuntu环境，在这个环境里安装一些软件包。我们输入`docker pull ubuntu`拉取镜像，默认是最新版也就是20.04。

输入`docker run -it --name=work ubuntu /bin/bash`，我们就创建了一个叫做work的容器，并且进入了它。Ctrl-P Ctrl-Q可以退出而不停止运行。

<!-- 第一步先是更换ubuntu的源。我们在<https://mirrors.tuna.tsinghua.edu.cn/help/ubuntu/>上复制`sources.list`并保存。
然后需要在`deb`后面加上`[trusted=yes]`，这是因为默认的docker镜像不带`ca-certificates`包，但不信任这些源的话就会需要这个包用以认证。
之后再输入`docker cp .../sources.list work:/etc/apt/sources.list`将其复制进入容器中。

接下来运行`docker start work && docker attach work`，进入该容器。然后更新软件环境，安装需要的包： -->

然后开始安装需要的库：

```bash
apt-get update
apt-get install ca-certificates # 如果后续想要更换源，必须安装这个包用来认证
apt-get install ...
```

安装完成之后，我们可以将其commit，从而把容器的状态保存下来。如果我们在主机上有个工作目录，希望docker的容器也能使用，那么我们需要配置volume，启动时直接带上参数即可：

```bash
docker commit ... work
docker run -it -v ...:... --name=work work /bin/bash
```

# 使用

此时我们已经可以进行使用了。不过，为了让我们的生活更美好，我们可以选择用VSCode远程连接到容器进行编程。

这里需要注意的是，我们应该使用微软发行的VSCode而不是OSS版本（但我们可以通过软链接使得二者配置相同），否则会报一些错误（这个可以加命令行参数解决），而且连接到远程以后无法自动安装vscode的服务端。

然后按照官方指南直接attach到正在运行的docker，打开文件夹即可，正常使用即可。

# 在docker里使用代理

如果在宿主机上已有代理，但运行docker时希望docker内的容器也有代理环境，那么需要配置容器使用宿主机上的网络环境。

在构建docker镜像时，使用`--network host --build-arg HTTP_PROXY=$HTTP_PROXY --build-arg HTTPS_PROXY=$HTTP_PROXY`参数；运行docker容器时，使用`--network host --env HTTP_PROXY=$HTTP_PROXY --env HTTPS_PROXY=$HTTPS_PROXY`参数。