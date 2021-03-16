# Linux

*Linux* 是一种自由和开源的类 UNIX 操作系统。Linux具有很多发行版（例如Ubuntu，centos，deepin等），但是其Linux内核都是一样的，只不过内核之上安装的软件，界面风格，以及文件系统的组织形式不一样而已。

安装图形化界面需要占用更多资源，所以Linux用作服务器时一般是没有界面的，都是通过命令行终端输入shell脚本进行操作，如果为远程服务器则通过ssh进行远程操控。本地机器都是内网IP，需要公网IP别人才能访问，如果购买云服务器一般会赠送一个公网IP，再购买一个域名就能映射使用了。

Linux中一切皆文件，并且严格区分大小写。



# yum

Yum（全称为 Yellow dog Updater, Modified）是一个在Fedora和RedHat以及CentOS中的Shell前端软件包管理器。基于RPM包管理，能够从指定的服务器自动下载RPM包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包，无须繁琐地一次次下载、安装。

yum是centos下的软件包管理工具，使用yum前需要先配置网络：https://www.cnblogs.com/passer101/p/9899202.html，在网络配置好后就可以使用yum下载软件。

```
yum install net-tools
```

yum 主要的命令如下:
`yum search name` 	搜索软件包
`yum install name` 	安装软件包
`yum remove name` 	移除软件包
`yum update `	更新Linux版本



# 文本编辑vim

vi为linux自带的文本编辑器，使用vi命令可以操作文本

```
vi ./1.txt
```

可以使用更方便的vim来代替vi。

```
yum install vim
```

安装之后可以使用vim来操作文本

```
vim ./1.txt
```





# Linux命令

## 查看ip

- ifconfig

  如果显示ifconfig：command not found则表示安装的Linux为mini版本未携带该命令，下载net-tools后就可以使用该命令

- ip addr

  





# shell脚本

shell是一种用于Linux的系统管理的脚本语言，shell有很多类型：sh、bash、ksh、rsh、csh等等， Linux中以.sh结尾文件是脚本文件，一般都是bash脚本。







