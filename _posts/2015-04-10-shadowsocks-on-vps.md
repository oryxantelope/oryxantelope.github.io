---
layout: post
title: "VPS 上搭建 shadowsocks 代理服务"
description: ""
category: tech 
tags: [VPS, shadowsocks]
---
{% include JB/setup %}

## 1. 安装 CentOS 7 

### 1.1. 配置启用 iptables 来代替 Firewalld

#### 1.1.1. Disable Firewalld Service.

    [root@rhel-centos7-tejas-barot-linux ~]# systemctl mask firewalld

#### 1.1.2. Stop Firewalld Service.

    [root@rhel-centos7-tejas-barot-linux ~]# systemctl stop firewalld

#### 1.1.3. Install iptables service related packages.

    [root@rhel-centos7-tejas-barot-linux ~]# yum -y install iptables-services

#### 1.1.4. Make sure service starts at boot:

    [root@rhel-centos7-tejas-barot-linux ~]# systemctl enable iptables

*If you do not want ip6tables, You can skip following command.*
    
    [root@rhel-centos7-tejas-barot-linux ~]# systemctl enable ip6tables

#### 1.1.5. Now, Finally Let’s start the iptables services.

    [root@rhel-centos7-tejas-barot-linux ~]# systemctl start iptables

*If you do not want ip6tables, You can skip following command.*

    [root@rhel-centos7-tejas-barot-linux ~]# systemctl start ip6tables

**Firewalld Service is now disabled and stop, You can use iptables.**

### 1.2. 更改时区

    [root@rhel-centos7]# cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

## 2. 安装 Denyhosts 服务

Denyhosts（[SourceForge](http://denyhosts.sourceforge.net/), [GitHub](https://github.com/denyhosts/denyhosts)）是一个Linux系统下阻止暴力破解SSH密码的软件，它的原理与DDoS Deflate类似，可以自动拒绝过多次数尝试SSH登录的IP地址，防止互联网上某些机器常年破解密码的行为，也可以防止黑客对SSH密码进行穷举。更多信息可见[README](https://github.com/denyhosts/denyhosts/blob/master/README.md)）。

### 2.1. 安装

可选的安装方式有源代码安装（Source Distribution）和二进制安装包安装（Binary Distribution），这里采用源代码安装。

首先下载并安装源代码：

    [root@rhel-centos7]# git clone https://github.com/denyhosts/denyhosts.git
    [root@rhel-centos7]# cd denyhosts

以 root 身份安装:

    [root@rhel-centos7]# python setup.py install

DenyHosts requires that a configuration file be created before it can function. The sample configuration file denyhosts.conf contains most of the possible settings and should be copied and then edited as such:

    [root@rhel-centos7]# cp denyhosts.conf /etc
    [root@rhel-centos7]# <edit> /etc/denyhosts.conf

The sample configuration file contains informational comments that should help you quickly configure DenyHosts. After you have edited your configuration file, save it.

Next, if you intend to run DenyHosts in daemon mode (recommended) copy the sample daemon-control.dist script as such:

    [root@rhel-centos7]# cp daemon-control-dist daemon-control

Edit the daemon-control file. You should only need to edit this section near the top:

    ###############################################
    #### Edit these to suit your configuration ####
    ###############################################
    
    DENYHOSTS_BIN   = "/usr/bin/denyhosts.py"
    DENYHOSTS_LOCK  = "/var/lock/subsys/denyhosts"
    DENYHOSTS_CFG   = "/etc/denyhosts.conf"

These defaults should be reasonable for many systems. You should customize these settings to match your particular system.

Once you have edited the configuration and daemon control files make sure that the daemon control script it **executable** (by root).

    [root@rhel-centos7]# chown root daemon-control
    [root@rhel-centos7]# chmod 700 daemon-control

### 2.2. Starting DenyHosts Automatically

#### Method 1 (preferred)

Create a symbolic link from /etc/init.d such as:

    [root@rhel-centos7]# cd /etc/init.d
    [root@rhel-centos7]# ln -s /usr/share/denyhosts/daemon-control denyhosts

If you have chkconfig installed you can then use it to ensure that DenyHosts runs at boot time:

    [root@rhel-centos7]# chkconfig --add denyhosts

If you do not have chkconfig (or similar) installed you can either manually create the symlinks in /etc/rc2.d, /etc/rc3.d, /etc/rc5.d but that is beyond the scope of this document.

#### Method 2

Add an entry into the /etc/rc.local file:

     /path-to-your-denyhosts-folder/daemon-control start

## 3. 安装 shadowsocks-go 服务

### 3.1. 安装Go所需要的依赖库

    [root@rhel-centos7]# yum install mercurial git gcc

### 3.2. 安装Go

    [root@rhel-centos7]# wget https://storage.googleapis.com/golang/go1.4.2.linux-amd64.tar.gz
    [root@rhel-centos7]# tar -zxvf go1.4.2.linux-amd64.tar.gz
    [root@rhel-centos7]# cd go

### 3.3. 添加 Go 的环境变量

编辑 /etc/profile 文件，在最后添加如下行:

    export GOROOT=/tools/go/go
    export PATH=$GOROOT/bin:$PATH
    export GOPATH=/tools/go/gopkg

保存之后运行 `source /etc/profile`

然后运行 `go version` 查看是否安装成功,如果出现 `go version go1.2 …` 表示安装成功。

### 3.4. 开始安装ShadowSocks-go版本

    [root@rhel-centos7]# go get github.com/shadowsocks/shadowsocks-go/cmd/shadowsocks-server

Both the server and client program will look for config.json in the current directory. You can use -c option to specify another configuration file.

Configuration file is in json format and has the same syntax with shadowsocks-nodejs. You can download the sample config.json, change the following values:

    server          your server ip or hostname
    server_port     server port
    local_port      local socks5 proxy port
    method          encryption method, null by default (table), the following methods are supported:
                    aes-128-cfb, aes-192-cfb, aes-256-cfb, bf-cfb, cast5-cfb, des-cfb, rc4-md5, rc4, table
    password        a password used to encrypt transfer
    timeout         server option, in seconds

sample `/etc/shadowsocks.json`:

    {
        "server": "0.0.0.0",
        "port_password": {
                "8387": "password",
                "8388": "password"
        },
        "method": "aes-256-cfb",
        "timeout": 600,
        "local_port": 1080
    }

创建自启动服务

    [root@rhel-centos7]# vi /etc/init.d/shadowsocks

实例代码如下：

    #!/bin/bash
    # Start/stop shadowsocks.
    #
    #=========================================================================
    # Run level information:
    # chkconfig: 2345 97 99
    # Description:       Modified from Linode's nginx fastcgi startup script
    # processname: shadowsocks
    #=========================================================================
    
    # Note: this script requires sudo in order to run shadowsocks as the specified
    # user.
    
    BIN=/root/go/pkg/bin/shadowsocks-server
    CONFIG_FILE=/etc/shadowsocks.json
    LOG_FILE=/var/log/shadowsocks.log
    #USER=nobody
    USER=root
    #GROUP=nobody
    GROUP=root
    PID_DIR=/var/run
    PID_FILE=$PID_DIR/shadowsocks.pid
    RET_VAL=0
    
    [ -x $BIN ] || exit 0
    
    check_running() {
      if [[ -r $PID_FILE ]]; then
        read PID <$PID_FILE
        if [[ -d "/proc/$PID" ]]; then
          return 0
        else
          rm -f $PID_FILE
          return 1
        fi
      else
        return 2
      fi
    }
    
    do_status() {
      check_running
      case $? in
        0)
          echo "shadowsocks running with PID $PID"
          ;;
        1)
          echo "shadowsocks not running, remove PID file $PID_FILE"
          ;;
        2)
          echo "Could not find PID file $PID_FILE, shadowsocks does not appear to be running"
          ;;
      esac
      return 0
    }
    
    do_start() {
      if [[ ! -d $PID_DIR ]]; then
        echo "creating PID dir"
        mkdir $PID_DIR || echo "failed creating PID directory $PID_DIR"; exit 1
        chown $USER:$GROUP $PID_DIR || echo "failed creating PID directory $PID_DIR"; exit 1
        chmod 0770 $PID_DIR
      fi
      if check_running; then
        echo "shadowsocks already running with PID $PID"
        return 0
      fi
      if [[ ! -r $CONFIG_FILE ]]; then
        echo "config file $CONFIG_FILE not found"
        return 1
      fi
      echo "starting shadowsocks" >>$LOG_FILE
      # sudo will set the group to the primary group of $USER
      # sudo -u $USER $BIN -c $CONFIG_FILE >>$LOG_FILE &
      $BIN -c $CONFIG_FILE >>$LOG_FILE &
      PID=$!
      echo $PID > $PID_FILE
      sleep 0.3
      if ! check_running; then
        echo "start failed"
        return 1
      fi
      echo "shadowsocks running with PID $PID" >>$LOG_FILE
      return 0
    }
    
    do_stop() {
      if check_running; then
        echo "stopping shadowsocks with PID $PID" >>$LOG_FILE
        kill $PID
        rm -f $PID_FILE
      else
        echo "Could not find PID file $PID_FILE"
      fi
    }
    
    do_restart() {
      do_stop
      do_start
    }
    
    case "$1" in
      start|stop|restart|status)
        do_$1
        ;;
      *)
        echo "Usage: shadowsocks {start|stop|restart|status}"
        RET_VAL=1
        ;;
    esac
    
    exit $RET_VAL

Once you have edited the configuration and daemon control files make sure that the daemon control script it **executable** (by root).

    [root@rhel-centos7]# chown root /etc/init.d/shadowsocks
    [root@rhel-centos7]# chmod 700 /etc/init.d/shadowsocks

If you have chkconfig installed you can then use it to ensure that shadowsocks runs at boot time:

    [root@rhel-centos7]# chkconfig --add shadowsocks

If you do not have chkconfig (or similar) installed you can either manually create the symlinks in /etc/rc2.d, /etc/rc3.d, /etc/rc5.d but that is beyond the scope of this document.

### 3.4. 打开防火墙相应端口

编辑 /etc/sysconfig/iptables 文件，在适当位置添加如下行：

    -A INPUT -p tcp -m state --state NEW -m tcp --dport 8387 -j ACCEPT
    -A INPUT -p tcp -m state --state NEW -m tcp --dport 8388 -j ACCEPT

重启防火墙：

    [root@rhel-centos7]# service iptables restart

## 4. 启动所有相关服务

如果 VPS 可以暂时关机，则执行 `reboot` 重启即可。

    [root@rhel-centos7]# reboot

否则应手动启动相应的 denyhosts 服务以及 shadowsocks 服务。

    [root@rhel-centos7]# service denyhosts start
    [root@rhel-centos7]# service shadowsocks start

## 5. 其他 shadowsocks 公共代理的必要设置

见 [shadowsocks 公共代理的必要设置](https://gist.github.com/fqrouter/95c037f9a3ba196fd4dd).

## reference

\[1\] [Read more: RHEL 7 / CentOS 7 : Disable Firewalld and use iptables](http://www.tejasbarot.com/2014/08/02/rhel-7-centos-7-disable-firewalld-and-use-iptables/#ixzz3WtS7oXLH)

\[2\] [DenyHosts](https://github.com/denyhosts/denyhosts)

\[3\] [shadowsocks-go](http://shadowsocks.github.io/shadowsocks-go/)

