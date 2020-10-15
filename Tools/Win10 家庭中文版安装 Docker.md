Win10 家庭中文版安装 Docker
====================
## 1 开启 Hyper-V
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

## 2 伪装成 Win10 专业版
以管理员身份打开 cmd，执行如下命令：
```text
REG ADD "HKEY_LOCAL_MACHINE\software\Microsoft\Windows NT\CurrentVersion" /v EditionId /T REG_EXPAND_SZ /d Professional /F
```

## 3 下载 Docker for Windows
- [Docker for Windows](https://docs.docker.com/docker-for-windows/install/) 支持 64 位版本的 Windows 10 Pro，且必须开启 Hyper-V。

- 点击以下链接下载 Stable 或 Edge 版本的 Docker for Windows。
下载好之后双击 Docker for Windows Installer.exe	开始安装。

- Docker 安装完成后，执行 cmd 命令：docker version 确认安装成功。

## 4 配置国内镜像加速
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


# 参考资料
- [Docker 官方文档](https://docs.docker.com/)



