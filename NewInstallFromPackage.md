# 从包里安装

- 安装memcached二进制程序
- 依赖
- 从你的发行版安装
    - Ubuntu 和 Debian
    - Redhat/Fedora
    - FreeBSD
- 商业发行版
- 安装客户端
    - libmemcached
    - PEAR/CPAN/GEM/等

你安装的memcached版本对你能使用哪些功能有很大的影响。

老旧的版本缺乏bug修复，统计信息等。

有可能缺少命令。如果软件实在太旧，我们可能无法帮助你。

至少需要1.4.4或者更高的版本，如果这不难的话 :-)

## 依赖

memcached是C程序，依赖于最新的GCC版本以及最新的libevent。

安装的最好的方法是首先尝试你的发行版的包管理器。

如果包管理器中的版本太旧，你可能需要从backport中安装，甚至得从源码安装。

## 从发行版中安装

### Ubuntu与Debian

    apt-get install memcached


你还需要安装libevent，apt理应为你取得它。

要注意的是大部分的Ubuntu和debian版本拥有非常老旧版本的memcached包。

我们正在努力改善这个状况。

如果你是一位Debian用户，Debian的backports可能提供更新一些的版本。

### Redhat/Fedora

    yum install memcached

相当容易的说？但是遗憾的是你很可能得到一个老旧的版本。

### FreeBSD

    portmaster databases/memcached

（或者替换成你使用的任何其他的ports管理工具）

## 商业发行版

Membase公司在Membase服务器中提供了（免费提供）memcached能力。

它有一个基于用户界面的管理层。这个管理层提供了查看memcached统计信息的功能。这个管理层内建了代理功能。

它使用了开源bucket引擎为memcached内建了多重租赁。

## 安装客户端

一些流行的客户端很可能在你的发行版中有提供。

用apt或者yum搜索一下看看你能找到些什么！

### libmemcached

绝大多数的语言都有一个或两个主要的依赖于libmemcached的客户端。

这是用于访问使用memcached协议的服务器的标准C库。

某些客户端可能捆绑了一些兼容的版本，另一些可能要求独立安装libmemcached。


### PEAR/CPAN/GEM/等

别忘记检查一下你喜欢的语言的标准仓库。

安装一个客户端可能仅仅是一两条命令的事情。