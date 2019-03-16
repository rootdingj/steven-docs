新搞的云服务器用SecureCRT不支持上传和下载，没有找到rz命令。记录一下如何安装rz/sz命令的方法。

    一、工具说明

    在SecureCRT这样的ssh登录软件里, 通过在Linux界面里输入rz/sz命令来上传/下载文件. 对于某些linux版本, rz/sz默认没有安装所以需要手工安装。
    sz: 将选定的文件发送(send)到本地机器;
    rz：运行该命令会弹出 一个文件选择窗口, 从本地选择文件上传到服务器(receive).

    下载安装包lrzsz-0.12.20.tar.gz : http://www.ohse.de/uwe/software/lrzsz.html

    二、软件安装

    首先通过sftp工具把安 装文件上传到tmp目录下.

     # cd tmp
     # wget http://www.ohse.de/uwe/releases/lrzsz-0.12.20.tar.gz
     # tar zxvf lrzsz-0.12.20.tar.gz && cd lrzsz-0.12.20
     # ./configure && make && make install

     上面安装过程默认把lsz和lrz安装到了/usr/local/bin/目录下, 下面创建软链接, 并命名为rz/sz:
     # cd /usr/bin
     # ln -s /usr/local/bin/lrz rz
     # ln -s /usr/local/bin/lsz sz

     三、使用说明

     打开SecureCRT软件 -> Options -> session options -> X/Y/Zmodem 下可以设置上传和下载的目录; 然后在用SecureCRT登陆linux终端的时候:
     # sz filename (发送文件到客户端,zmodem接收可以自行启动)
     # rz (从客户端上传文件到linux服务端)

     rz命令也可以在SecureCRT中使用UI来上传。

四、也可以使用yum来安装

#yum install lrzsz

     好了，这样就可以使用SecureCRT来上传和下载文件了。
