# 从源代码安装

- 为什么要从源代码开始构建
- 从源代码构建
    - 前提要求
    - 获取
    - 配置
    - make与install
- 要构建一个包还是make install？
    - 构建一个RPM
    - 构建一个deb

- 构建客户端
    - PEAR/CPAN/GEM/等

## 为什么要从源代码开始构建

在你开始从源代码构建之前，想清楚为什么。

如果你有一个最近版本的完美的优秀的包，你最好还是采用这个包比较好。

## 从源代码构建

### 前提要求

你很可能需要安装libevent的发开包。
 
- Ubuntu：`apt-get install libevent-dev`
- Redhat/Fedora：`yum install libevent-devel`

### 获取

    wget http://memcached.org/latest
    tar -zxvf memcached-1.x.x.tar.gz
    cd memcached-1.x.x

### 配置

#### 可选的安装目的地

如果你从源代码编译，你可能想要自定义安装目录。替换 /usr/local/memcached，将其换成任何你喜欢的目录。

    ./configure --prefix=/usr/local/memcached

### make与install

    make && make test
    sudo make install

如果你想编译成有SASL支持的版本，确保cyrus-sasl库已经编译，然后运行 ./configure --enable-sasl。想要深入了解SASL请移步[SASLHowto](https://code.google.com/p/memcached/wiki/SASLHowto)。

## 要构建一个包还是make install？

如果你是想在多于一个以上的服务器上部署memcached，最好还是打个包。这样的话，你能更干净地升级，轻松地卸载，轻松地重新安装，未来安装等等。make install是给开发者和笨蛋用的。

### 构建一个RPM包

memcached源码tarball包含有一个可以工作的.spec文件。要使用它，需要创建一个目录用于RPM，使用以下的命令编译memcached。不要以root身份运行这些命令，因为测试不会通过。

    echo "%_topdir /home/you/rpmbuild" >> ~/.rpmmacros
    mkdir -p /home/you/rpmbuild/{SPECS,BUILD,SRPMS,RPMS,SOURCES}
    wget http://memcached.org/latest
    rpmbuild -ta memcached-1.x.x.tar.gz

你需要先安装gcc与libevent-devel。（`yum install gcc libevent libevent-devel`）
然后就可以用标准的RPM安装命令 `rpm -Uvh memcached-etc.rpm`来安装了。

### 构建一个deb包

待办事项：本节内容需要完善

## 构建客户端

注意到许多客户端依赖于libmemcached库。

它们要么在源代码里包含了libmemcached，要么需要一个外部的构建。

你同样可以依照上面所述的实例来获取并安装libmemcached。

## PEAR/CPAN/GEM/等

如果你是从源代码开始构建，特别要注意大部分语言都有一个发布系统，这可以使安装变得更容易些。