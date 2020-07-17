Docker笔记
====================

# 1、Docker 简介

## 1.1、什么是 Docker
Linux 容器是一种虚拟化技术，与虚拟机不同的是，他不是模拟一个完整的操作系统，而是对进程进行隔离。或者说，在正常进程的外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。

Docker 在容器的基础上，进行了进一步的封装，从文件系统、网络互联到进程隔离等，他提供简单易用的容器使用接口。Docker 将应用程序与该程序的依赖，打包在一个镜像文件里。运行这个文件，就会生成一个虚拟容器。程序在这个虚拟容器里运行，就好像在真实的物理机上运行一样。

## 1.2、为什么要用 Docker
由于容器是进程级别的，相比虚拟机有很多优势：
- 启动快
- 资源占用少
- 体积小

Docker 主要用途：
- 提供一次性的环境。比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。
- 提供弹性的云服务。因为 Docker 容器可以随开随关，很适合动态扩容和缩容。
- 组建微服务架构。通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。

## 1.3、Docker 的基本概念

### 1.3.1、镜像（Image）
操作系统分为内核和用户空间,对于 Linux 而言，内核启动后，会挂 root 文件系统为其提供用户空间支持。**Docker 镜像（Image），就相当于是一个 root 文件系统**。

Docker 镜像是保存运行时所需程序、库、资源、配置、参数等的静态文件，它不包含任何动态数据，其内容在构建之后也不会被改变。

#### 分层存储
镜像包含操作系统完整的 root	文件系统，Docker 在设计时充分利用	**UnionFS 的技术**，将其设计为**分层存储**的架构。Docker 镜像并不是像 ISO 那样的打包文件，镜像是一个虚拟的概念，**它由多层文件系统组成**。

镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。

在构建镜像的时候，每一层尽量只包含该层需要添加的东西，任何额外的东西应该在该层构建结束前清理掉，以免镜像后期变的庞杂，难以维护。

### 1.3.1、容器（Container）
镜像（``Image``）和容器（``Container``）的关系，就像是面向对象程序设计中的类和实例一样，**镜像是静态的定义，容器是镜像运行时的实体**。容器可以被创建、启动、停止、删 除、暂停等。

容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。因此容器可以拥有自己的 root	文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID	空间。容器内的进程是运行在一个隔离的环境里，使用起来就好像是在一个独立于宿主的系统下操作一样。

每一个容器运行时，会以镜像为基础层，在其上创建一个为容器运行时读写而准备的容器存储层。容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡，并且缓存在容器存储层的数据也会随之清空。

按照 ocker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高（不会丢失数据）。

### 1.3.1、仓库（Repository）
``Docker Registry`` 用于集中存储、分发镜像的服务。

一个 ``Docker	Registry`` 中可以包含多个仓库（``Repository``）；每个仓库可以包含多个标签（``Tag``）；每个标签对应一个镜像。

通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过	 <仓库名>:<标签> 的格式来指定具体是这个软件哪个版本的镜像。如果不给 出标签，将以	``latest`` 作为默认标签。例如：``ubuntu:16.04、ubuntu:latest``。

### Docker Registry	公开服务
Docker Registry	公开服务是开放给用户使用、允许用户管理镜像的	Registry 服务。一般公开服务允许用户免费上传、下载公开的镜像。常见的公开服务如：官方的 [Docker	Hub](https://hub.docker.com/) 、[网易云镜像服务](https://c.163.com/hub#/m/library/) 、[阿里云镜像库](https://cr.console.aliyun.com/) 。

### 私有 Docker Registry
用户还可以在本地搭建私有 Docker	Registry。Docker 官方提供了 Docker	Registry 镜像，可以直接使用做为私有	Registry 服务。

# 2、安装 Docker

## 2.1、Win10 家庭中文版安装 Docker

### 1.开启 Hyper-V

- 新建 ``hyperv.cmd`` 文件，内容如下：
```text
pushd "%~dp0"

dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt

for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"

del hyper-v.txt

Dism /online /enable-feature /featurename:Microsoft-Hyper-V-All /LimitAccess /ALL
```
- 以管理员身份执行 hyperv.cmd 文件。如果系统要你重启，便重启。

- 在控制面板->程序和功能->启用或关闭 Windows 功能打开 Hyper-V。

### 2.伪装成 Win10 专业版
以管理员身份打开 cmd，执行如下命令：
```text
REG ADD "HKEY_LOCAL_MACHINE\software\Microsoft\Windows NT\CurrentVersion" /v EditionId /T REG_EXPAND_SZ /d Professional /F
```

### 3.下载 Docker for Windows
- [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) 支持 64 位版本的 Windows 10 Pro，且必须开启 Hyper-V。

- 点击以下链接下载 Stable 或 Edge 版本的 Docker	for	Windows。
下载好之后双击 Docker for Windows Installer.exe	开始安装。

- Docker 安装完成后，执行 cmd 命令：docker version 确认安装成功。

### 4.配置国内镜像加速
国内从 Docker Hub 拉取镜像有时会遇到困难，此时可以配置国内镜像加速器。国内很多云服务商都提供了加速器服务，例如：

- [Azure 中国镜像](https://github.com/Azure/container-service-for-azure-china/blob/master/aks/README.md#22-container-registry-proxy)
- [阿里云加速器](https://cr.console.aliyun.com/cn-hangzhou/mirrors)
- [七牛云加速器](https://kirk-enterprise.github.io/hub-docs/#/user-guide/mirror)

由于镜像服务可能出现宕机，建议同时配置多个镜像。

配置步骤：
- 右键点击 Docker 图标 --> 选择 Settings --> 选择 Docker Daemon 标签页

- 修改 json，把<镜像加速地址>加入到 registry-mirrors 中
```text
{
  "registry-mirrors": [
    "https://naljryvl.mirror.aliyuncs.com",
    "https://reg-mirror.qiniu.com"
  ],
  "debug": true,
  "experimental": false
}
```

执行命令： ``docker info`` 查看加速器配置是否生效，若看到如下内容，说明配置成功：
```text
 Registry Mirrors:
      https://naljryvl.mirror.aliyuncs.com/
```

相关配置参数可参考：[daemon-configuration-file](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)

## 2.2、阿里云 Centos 安装 Docker

# 3、使用 Docker 镜像

## 3.1、从仓库获取镜像
### 获取
从 Docker	镜像仓库获取镜像的命令是 ``docker pull``。其命令格式为：
```text
docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]
```
例如：
```text
PS C:\Users\dj673\Desktop> docker pull ubuntu:16.04
16.04: Pulling from library/ubuntu
e80174c8b43b: Pull complete                                       d1072db285cc: Pull complete                                       858453671e67: Pull complete                                       3d07b1124f98: Pull complete                                       Digest:sha256:e1fcd1f4dc443415d791bc1e97e93d3d595156005bce2b529f6b64584c9f2f4d
Status: Downloaded newer image for ubuntu:16.04
docker.io/library/ubuntu:16.04
```

使用 ``docker	pull --help``	命令查看具体选项。

### 运行
``docker run	-it	--rm	ubuntu:16.04 bash``

### 退出
``exit``


## 3.2、管理本机镜像

## 3.3、镜像实现的基本原理





# 参考资料
- [Docker 官方文档](https://docs.docker.com/)
- [Docker — 从入门到实践](https://yeasy.gitbooks.io/docker_practice/content/)
- [鲸鱼云 Docker中文文档](https://www.kancloud.cn/jingyucloud/docker/216412)


