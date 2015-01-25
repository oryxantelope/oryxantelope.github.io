---
layout: post
title: "Linux 自启动服务脚本"
description: ""
category: tech
tags: [Linux, Shell]
---
{% include JB/setup %}

##init.d 脚本

Linux 系统中 **init.d** 目录包含许多系统各种服务的启动和停止脚本。/etc 目录下有许多 **rc#.d** 形式存在的目录（这里 # 代表一个指定的初始化级别，范围是 0 ~ 6 ）。在这些目录之下，包含了许多对进程进行控制的脚本。这些脚本要么以"K"开头，要么以"S"开头。"S" 代表 Start ; "K" 代表 Kill 。这些脚本放置的地方，将决定这些脚本什么时候开始运行。在这些目录之间，系统服务一起合作。

为了能够使用 init.d 目录下的脚本，你需要有 **root 权限或 sudo 权限**。每个脚本都将被作为一个命令运行，该命令的结构大致如下所示：

     /etc/init.d/command options

comand 是实际运行的命令，选项可以有如下几种：

*  start
*  stop
*  reload
*  restart
*  force-reload

常见的操作方式是以 `service command options` 的形式来操作服务，这也等价于上述直接执行命令，当然此处的 command 必须在 /etc/init.d 也就是 /etc/rc.d/init.d 目录下。

PS：**/etc/rc.local** 是在系统初始化级别脚本运行之后再执行的，因此可以安全地在里面添加你想在系统启动之后执行的脚本。常见的情况是你可以再里面添加 nfs 挂载 /mount 脚本。

在 **CentOS 7** 之前，自启动服务通过 **init.d** 脚本来实现，样例如下([原始链接](http://werxltd.com/wp/2012/01/05/simple-init-d-script-template/))：

    #!/bin/bash
    # chkconfig: 2345 20 80
    # description: myapp daemon
    # processname: myapp
    
    DAEMON_PATH="/home/pkusei/myapp"
    
    DAEMON=myapp
    DAEMONOPTS="-my opts"
    
    NAME=myapp
    DESC="My daemon description"
    PIDFILE=/var/run/$NAME.pid
    SCRIPTNAME=/etc/init.d/$NAME
    
    case "$1" in
    start)
            printf "%-50s" "Starting $NAME..."
            cd $DAEMON_PATH
            PID=`$DAEMON $DAEMONOPTS > /dev/null 2>&1 & echo $!`
            #echo "Saving PID" $PID " to " $PIDFILE
            if [ -z $PID ]; then
                printf "%s\n" "Fail"
            else
                echo $PID > $PIDFILE
                printf "%s\n" "Ok"
            fi
    ;;
    status)
            printf "%-50s" "Checking $NAME..."
            if [ -f $PIDFILE ]; then
                PID=`cat $PIDFILE`
                if [ -z "`ps axf | grep ${PID} | grep -v grep`" ]; then
                    printf "%s\n" "Process dead but pidfile exists"
                else
                    echo "Running"
                fi
            else
                printf "%s\n" "Service not running"
            fi
    ;;
    stop)
            printf "%-50s" "Stopping $NAME"
                PID=`cat $PIDFILE`
                cd $DAEMON_PATH
            if [ -f $PIDFILE ]; then
                kill -HUP $PID
                printf "%s\n" "Ok"
                rm -f $PIDFILE
            else
                printf "%s\n" "pidfile not found"
            fi
    ;;
    
    restart)
            $0 stop
            $0 start
    ;;
    
    *)
            echo "Usage: $0 {status|start|stop|restart}"
            exit 1
    esac

*   第 3 行 ` # chkconfig: 2345 20 80` 中 2345 表示**启动级别**，20 表示服务**启动顺序**，80 表示服务**停止顺序**。顺序由数字从小到大依次执行。

    通常服务脚本都会出现如下的几行：

        # chkconfig: 2345 20 80
        # description: Saves and restores system entropy pool for \
        #              higher quality random number generation.

    >Each service which should be manageable by chkconfig needs two or more commented  lines added to its init.d script. The first line tells chkconfig what runlevels the service should be started in by default,  as well as the start and stop priority levels. If the service should not, by default, be started in any runlevels, a - should be used  in  place of the runlevels list.  The second line contains a description for the service, and may be extended across multiple lines with backslash continuation.

    每个被 chkconfig 管理的服务脚本中都会有两行或者多行配置，第一行标明默认在何种启动级别下自启动，以及启动和停止的优先级顺序。如果在任何启动级别下都不会自启动，用 `-` 来代替启动级别列表。第二行是该服务的简要描述，可以用 `\` 来跨行。

    在大多数linux操作系统下一共有如下6个典型的运行级别：

    -  0 停机
    -  1 单用户，Does not configure network interfaces, start daemons, or allow non-root logins
    -  2 多用户，无网络连接 Does not configure network interfaces or start daemons
    -  3 多用户，启动网络连接 Starts the system normally.
    -  4 用户自定义
    -  5 多用户带图形界面
    -  6 重启

    具体到不同 Linux 发行版上有所不同。

*   第 21 行 `> /dev/null 2>&1` 表示将错误输出2 绑定给 正确输出 1，然后将 正确输出 发送给 /dev/null设备，/dev/null 这个设备，是 linux  中黑洞设备，什么信息只要输出给这个设备，都会给吃掉

    `$!` 表示 Shell 最后运行的后台Process的PID。

    常见的 Shell 脚本变量：

    - `$$`    Shell本身的PID（ProcessID） 
    - `$!`    Shell最后运行的后台Process的PID 
    - `$?`    最后运行的命令的结束代码（返回值） 
    - `$-`    使用Set命令设定的Flag一览 
    - `$*`    所有参数列表。
    - `$@`    所有参数列表。
    - `$#`    添加到Shell的参数个数 
    - `$0`    Shell本身的文件名 
    - `$1~$n`     添加到Shell的各参数值。`$1`是第 1 参数、`$2`是第 2 参数…。 

    `&` 符号：如果想把一条命令放到后台执行的话，则需要加上这个符号。通常用于命令运行时间非常长的情况。

*  第 47 行，HUP 信号 代号是 1，常见的其他信号如下：
        
        Signal     Value     Action   Comment
        ---------------------------------------------------------------------
        SIGHUP        1       Term    Hangup detected on controlling terminal
                                      or death of controlling process
        SIGINT        2       Term    Interrupt from keyboard
        SIGQUIT       3       Core    Quit from keyboard
        SIGILL        4       Core    Illegal Instruction
        SIGABRT       6       Core    Abort signal from abort(3)
        SIGFPE        8       Core    Floating point exception
        SIGKILL       9       Term    Kill signal
        SIGSEGV      11       Core    Invalid memory reference
        SIGPIPE      13       Term    Broken pipe: write to pipe with no
                                      readers
        SIGALRM      14       Term    Timer signal from alarm(2)
        SIGTERM      15       Term    Termination signal
        SIGUSR1   30,10,16    Term    User-defined signal 1
        SIGUSR2   31,12,17    Term    User-defined signal 2
        SIGCHLD   20,17,18    Ign     Child stopped or terminated
        SIGCONT   19,18,25    Cont    Continue if stopped
        SIGSTOP   17,19,23    Stop    Stop process
        SIGTSTP   18,20,24    Stop    Stop typed at terminal
        SIGTTIN   21,21,26    Stop    Terminal input for background process
        SIGTTOU   22,22,27    Stop    Terminal output for background process

##systemctl 脚本 

RHEL/CentOS 7.0 中一个最主要的改变，就是切换到了 systemd 。它用于替代红帽企业版Linux前任版本中的 SysV 和 Upstart，对系统和服务进行管理。systemd 兼容 SysV 和 Linux 标准组的启动脚本。

Systemd 是一个 Linux 操作系统下的系统和服务管理器。它被设计成向后兼容 SysV 启动脚本，并提供了大量的特性，如开机时平行启动系统服务，按需启动守护进程，支持系统状态快照，或者基于依赖的服务控制逻辑。

先前的使用 SysV 初始化或 Upstart 的红帽企业版 Linux 版本中，使用位于 /etc/rc.d/init.d/ 目录中的 bash 初始化脚本进行管理。而在 RHEL 7/CentOS 7 中，这些启动脚本被服务单元取代了。服务单元以 .service 文件扩展结束，提供了与初始化脚本同样的用途。要查看、启动、停止、重启、启用或者禁用系统服务，你要使用 systemctl 来代替旧的 service 命令。

>注：为了向后兼容，旧的 service 命令在 CentOS 7 中仍然可用，它会重定向所有命令到新的 systemctl 工具。

每一个服务以 .service 结尾，一般会分为3部分：[Unit]、[Service] 和 [Install]。

实例代码如下：

    [Unit]
    Description=app
    After=network.target remote-fs.target nss-lookup.target
     
    [Service]
    Type=forking
    PIDFile=/node.js/pid
    ExecStart=/usr/local/bin/app start /node.js/xxx/bin/www
    ExecReload=/usr/local/bin/app restart /node.js/xxx/bin/www
    ExecStop=/usr/local/bin/app stop /node.js/xxx/bin/www
    PrivateTmp=true
     
    [Install]
    WantedBy=multi-user.target

[Unit] 部分主要是对这个服务的说明，内容包括Description和After，Description用于描述服务，After用于描述服务类别。

[Service] 部分是服务的关键，是服务的一些具体运行参数的设置，这里 `Type=forking` 是后台运行的形式，PIDFile 为存放PID的文件路径，ExecStart 为服务的具体运行命令，ExecReload 为重启命令，ExecStop 为停止命令，`PrivateTmp=True` 表示给服务分配独立的临时空间，注意：[Service] 部分的启动、重启、停止命令全部要求使用绝对路径，使用相对路径则会报错！

[Install] 部分是服务安装的相关设置，可设置为多用户的.

服务脚本按照上面编写完成后，以 754 的权限保存在 /usr/lib/systemd/system 目录下，这时就可以利用 systemctl 进行配置了.

常用命令：

    systemctl start httpd.service
    systemctl stop httpd.service
    systemctl restart httpd.service
    systemctl status httpd.service
    systemctl enable httpd.service
    systemctl disable httpd.service

systemd 参考说明：

- http://www.freedesktop.org/software/systemd/man/systemd.unit.html
- https://wiki.archlinux.org/index.php/systemd

##reference

\[1\] [Service Scripts: /etc/init.d](http://litux.nl/Reference/index.html?page=books/7213/ddu0141.html)

