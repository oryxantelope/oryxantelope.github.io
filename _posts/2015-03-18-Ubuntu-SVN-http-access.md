---
layout: post
title: "Ubuntu 搭建 SVN 服务器 http 方式访问"
description: ""
category: tech 
tags: [SVN, Apache]
---
{% include JB/setup %}

1. Ubuntu 下安装 SVN

sudo apt-get install subversion

2. http 方式访问

2.1. 安装包

    sudo apt-get install apache2 libapache2-svn

2.2. 创建版本仓库

    sudo svnadmin create /path-to-your-repository

2.3. 授权 apache 读写

    sudo chown -R www-data:www-data /path-to-your-repository

2.4. 修改 apache 相应配置文件

    sudo vim /etc/apache2/mods-available/dav_svn.conf

修改如下内容：

    # dav_svn.conf - Example Subversion/Apache configuration
    #
    # For details and further options see the Apache user manual and
    # the Subversion book.
    #
    # NOTE: for a setup with multiple vhosts, you will want to do this
    # configuration in /etc/apache2/sites-available/*, not here.
    
    # <Location URL> ... </Location>
    # URL controls how the repository appears to the outside world.
    # In this example clients access the repository as http://hostname/svn/
    # Note, a literal /svn should NOT exist in your document root.
    <Location /svn>
    
      # Uncomment this to enable the repository
      DAV svn
    
      # Set this to the path to your repository
      SVNPath /var/lib/svn
      # Alternatively, use SVNParentPath if you have multiple repositories under
      # under a single directory (/var/lib/svn/repo1, /var/lib/svn/repo2, ...).
      # You need either SVNPath and SVNParentPath, but not both.
      #SVNParentPath /var/lib/svn
    
      # Access control is done at 3 levels: (1) Apache authentication, via
      # any of several methods.  A "Basic Auth" section is commented out
      # below.  (2) Apache <Limit> and <LimitExcept>, also commented out
      # below.  (3) mod_authz_svn is a svn-specific authorization module
      # which offers fine-grained read/write access control for paths
      # within a repository.  (The first two layers are coarse-grained; you
      # can only enable/disable access to an entire repository.)  Note that
      # mod_authz_svn is noticeably slower than the other two layers, so if
      # you don't need the fine-grained control, don't configure it.
    
      # Basic Authentication is repository-wide.  It is not secure unless
      # you are using https.  See the 'htpasswd' command to create and
      # manage the password file - and the documentation for the
      # 'auth_basic' and 'authn_file' modules, which you will need for this
      # (enable them with 'a2enmod').
      AuthType Basic
      AuthName "Subversion Repository"
      # AuthUserFile /etc/apache2/dav_svn.passwd
      AuthUserFile /var/lib/svn/conf/passwd
    
      # To enable authorization via mod_authz_svn (enable that module separately):
      #<IfModule mod_authz_svn.c>
      #AuthzSVNAccessFile /etc/apache2/dav_svn.authz
      #</IfModule>
    
      # The following three lines allow anonymous read, but make
      # committers authenticate themselves.  It requires the 'authz_user'
      # module (enable it with 'a2enmod').
      #<LimitExcept GET PROPFIND OPTIONS REPORT>
        Require valid-user
      #</LimitExcept> 
    
    </Location>

相关修改部分含义如注释所述。

特别的，Location是指访问地址： http://your-ip-address/svn

配置文件最后一部分禁止匿名用户访问。

2.5. 添加 SVN 用户

    sudo htpasswd -c /var/lib/svn/conf/passwd username

在相应文件中可以看到加密后的密码。如果没有安装 htpasswd, 需要按照提示安装 htpasswd。

2.6 重启 apache

    sudo service apache2 restart

