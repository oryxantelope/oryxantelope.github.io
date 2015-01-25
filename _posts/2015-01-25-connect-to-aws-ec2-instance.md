---
layout: post
title: "SSH 连接 AWS EC2"
description: ""
category: tech
tags: [AWS]
---
{% include JB/setup %}

AWS 创建实例时会提示创建一个key pair，此时需要下载该 xxx.pem的密钥文件到本地硬盘。

AWS Instance 默认用密钥对的方式来认证。

[TOC]

### 通过 SecureCRT 使用 .pem 登陆的方式

[How-to : Connect to Amazon EC2 Linux AMI using SecureCRT ](http://blog.skufel.net/2012/09/how-to-connect-to-amazon-ec2-linux-ami-using-securecrt/)

### 通过 SecureCRT 使用 .pub 登陆的方式

[Convert Amazon Web Services Private RSA Key For Use With SecureCRT](http://www.question-defense.com/2009/10/01/convert-amazon-web-services-private-rsa-key-for-use-with-securecrt)，具体步骤如下：  

1.上传 xxx.pem 文件到linux环境下，修改其权限为 600.

2.改写密钥格式为 OpenSSH.

	ssh-keygen -p -f xxx.pem

关于 ssh-keygen 参数说明见[ssh-keygen 中文手册](http://www.jinbuguo.com/openssh/ssh-keygen.html)

3.生成公钥 .pub 文件。

	ssh-keygen -e -f xxx.pem > xxx.pem.pub

4.下载生成的公钥文件到本地。

5.在 SecureCRT 新建连接（用户名为 ec2-user）。

6.修改该连接的属性，在认证方式（Authentication）中填入下载的公钥认证文件，注意 xxx.pem.pub 和 xxx.pem 要在g同一目录下。

7.打开连接即可登录该实例。  

### linux 环境下 SSH 命令可直接登录 AWS EC2 实例

命令如下：

	$ ssh -i xxx.pem ec2-user@{IP/hostname}


##Official References:

[1] [access aws ec2 instance in Linux](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AccessingInstancesLinux.html)

[2] [access aws ec2 instance by putty](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/putty.html)


