# Ubuntu + WSL

# 装wsl过程中总结的一些经验

- 替换`apt-get`源

一开始在网上随便找阿里的源进行覆盖，查找。后来发现每个版本都有对应的不同源，乱用的话很容易出现问题

[ubuntu镜像-ubuntu下载地址-ubuntu安装教程-阿里巴巴开源镜像站](https://developer.aliyun.com/mirror/ubuntu)

替换之前先备份

`sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak`

用vim打开文件

`vim /etc/apt/sources.list`

注释掉内容

然后按照上面的说明替换，每个版本都有不同的源

替换好源之后，可以进行源的更新

`apt update`   `apt upgrade`  `apt-get update` `apt-get upgrade`

- 安装最新版的git

发行版的ubuntu会提供比较旧的稳定版的git，我们可以进行替换  使用ppa方式安装（另一种方式是源代码安装）

```bash
sudo add-apt-repository ppa:git-core/ppa
sudo apt update
sudo apt install git
# 检查git版本
git --version
```

- 安装zsh

安装

`apt install zsh -y` 

改变默认的shell为zsh

`chsh -s  /bin/zsh`

- 安装 oh my zsh

[ohmyzsh/ohmyzsh](https://github.com/ohmyzsh/ohmyzsh)

参考官方文档最佳（因为如果有改变可以及时看到）

有两种安装方式

curl方式安装

`sh -c "$(curl -fsSL [https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh](https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh))"`

wget方式安装

`sh -c "$(wget -O- [https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh](https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh))"`

配置主题

`vim ~/.zshrc`

`ZSH_THEME="robbyrussell"`  修改为： `ZSH_THEME="agnoster" # (this is one of the fancy ones)`

- 安装java

`sudo apt-get install openjdk-8-jdk`

`java -version`

- 安装nvm

[nvm-sh/nvm](https://github.com/nvm-sh/nvm)

参考上面官方文档

可以通过两种方式安装

`curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash`

`wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash`

他会尝试自动添加如下脚本到(~/.bash_profile, ~/.zshrc, ~/.profile, 或 ~/.bashrc).

```bash
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

我们再添加一个变量，将node的源替换为国内阿里云的源

```bash
export NVM_NODEJS_ORG_MIRROR=http://npm.taobao.org/mirrors/node
```

- 替换npm源

```bash
# 设置为淘宝源
npm config set registry  http://registry.npm.taobao.org/
# 设置为官方源
npm config set registry http://www.npmjs.org/
```

设置`wsl 2`终端走代理

参考：

[WSL 2 中配置终端走主机代理 - Panda Wiki](https://wiki.imalan.cn/archives/WSL%202%20%E4%B8%AD%E9%85%8D%E7%BD%AE%E7%BB%88%E7%AB%AF%E8%B5%B0%E4%B8%BB%E6%9C%BA%E4%BB%A3%E7%90%86/)

- 获取主机ip地址

```bash
# 获取主机 IP
# 主机 IP 保存在 /etc/resolv.conf 中
export hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')
```

- 设置代理

```bash
export https_proxy="http://${hostip}:8888";
export http_proxy="http://${hostip}:8888";
export all_proxy="socks5://${hostip}:8889";
```

- 方便一点的设置

```bash
export hostip=$(cat /etc/resolv.conf |grep -oP '(?<=nameserver\ ).*')
alias setss='export https_proxy="http://${hostip}:8888";export http_proxy="http://${hostip}:8888";export all_proxy="socks5://${hostip}:8889";'
```

- 取消代理

```bash
unset ALL_PROXY
unset http_PROXY
unset https_PROXY
```

注意：

- 上面的端口号中，要改成自己ss的端口号，同时wsl2的话，ss软件要设置允许其他软件接入
- ping 网址的方式并不能测试代理通不通，而curl的方式可以

Windows中不能访问wsl监听的端口

情形：统一管理平台开发要开启两个服务，一个是前端的端口，一个是node端的端口，其中node端的端口监听到了ipv6那边，但是我们的host文件中并没有做好ipv6与域名的映射。

解决方法1：添加ipv6域名的映射

```bash
::1                 cfc-tech.admin.xiaomi.com
```

解决方法2：设置node应用启动的时候强制监听0.0.0.0，按理说如果没设置的时候默认就是0.0.0.0任意的ipv4地址的，但是不知为啥这里就要强制设置了

```jsx
server.listen(port, '0.0.0.0', () => {})
```

**配置c c++ 编译环境**

```bash
sudo apt-get install build-essential
# 查看gcc版本
gcc -v
```

安装redis

```bash
wget http://download.redis.io/redis-stable.tar.gz # 下载包
tar xvzf redis-stable.tar.gz  # 解压缩包

cd redis-stable    # 进入文件

# sudo apt-get install make  

# sudo apt-get install gcc

sudo apt-get install tcl             # 安装tcl

sudo apt-get install build-essential  # 安装gcc 环境

sudo apt-get update     

make   # 编译 

make test   # 测试
```