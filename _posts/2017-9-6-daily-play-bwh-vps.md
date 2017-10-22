---
layout: post
title: "[日常]和搬瓦工VPS一起玩耍的那些事"
description: "日常水文，主要记录了入手搬瓦工之后的配置工作"
categories: [daily]
tags: [daily, bwh, vps, ubuntu, ufw, docker]
redirect_from:
  - /2017/10/2/
---

> 日常水文，主要记录了入手搬瓦工vps之后所做的配置工作

* Kramdown table of contents
{:toc .toc}

1.
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ECDSA key sent by the remote host is
SHA256:qLJPkD/kvQ9LB2gyxgdGHURiwn8Gj90orMFlZeHygzg.
Please contact your system administrator.
Add correct host key in /home/jeneser/.ssh/known_hosts to get rid of this message.
Offending ECDSA key in /home/jeneser/.ssh/known_hosts:4
  remove with:
  ssh-keygen -f "/home/jeneser/.ssh/known_hosts" -R [66.112.214.183]:26064
ECDSA host key for [66.112.214.183]:26064 has changed and you have requested strict checking.
Host key verification failed.
```
```
gedit /home/jeneser/.ssh/known_hosts
```
删掉最后一行，保存

2.登录主机

```
jeneser@jeneser-X555LF:~$ ssh -p 26064 root@66.112.214.183
The authenticity of host '[66.112.214.183]:26064 ([66.112.214.183]:26064)' can't be established.
ECDSA key fingerprint is SHA256:qLJPkD/kvQ9LB2gyxgdGHURiwn8Gj90orMFlZeHygzg.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '[66.112.214.183]:26064' (ECDSA) to the list of known hosts.
root@66.112.214.183's password: 
Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-62-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

142 packages can be updated.
60 updates are security updates.


*** System restart required ***
root@ubuntu:~# 
```

3. 更新的问题

```
root@ubuntu:~# sudo apt-get update
Hit:1 http://us.archive.ubuntu.com/ubuntu xenial InRelease
Hit:2 http://us.archive.ubuntu.com/ubuntu xenial-updates InRelease      
Hit:3 http://security.ubuntu.com/ubuntu xenial-security InRelease       
Hit:4 http://us.archive.ubuntu.com/ubuntu xenial-backports InRelease
E: dpkg was interrupted, you must manually run 'sudo dpkg --configure -a' to correct the problem. 
root@ubuntu:~# sudo dpkg --configure -a

```

4. 防火墙问题
ufw是一个主机端的iptables类防火墙配置工具，比较容易上手。一般桌面应用使用ufw已经可以满足要求了。

http://wiki.ubuntu.org.cn/UFW%E9%98%B2%E7%81%AB%E5%A2%99%E7%AE%80%E5%8D%95%E8%AE%BE%E7%BD%AE

开启防火墙
```
root@ubuntu:~# sudo ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
root@ubuntu:~# sudo ufw allow 80
Rule added
Rule added (v6)
root@ubuntu:~# sudo ufw allow 8080
Rule added
Rule added (v6)
root@ubuntu:~# sudo ufw allow 26064
Rule added
Rule added (v6)
root@ubuntu:~# sudo ufw allow 55688
Rule added
Rule added (v6)
root@ubuntu:~# sudo ufw status
Status: active

To                         Action      From
--                         ------      ----
80                         ALLOW       Anywhere                  
8080                       ALLOW       Anywhere                  
26064                      ALLOW       Anywhere                  
55688                      ALLOW       Anywhere                  
80 (v6)                    ALLOW       Anywhere (v6)             
8080 (v6)                  ALLOW       Anywhere (v6)             
26064 (v6)                 ALLOW       Anywhere (v6)             
55688 (v6)                 ALLOW       Anywhere (v6)             

root@ubuntu:~# exit
logout
Connection to 66.112.214.183 closed.

```

5. warning: Setting locale failed.

vi /etc/locale.gen，找到# en_US.UTF-8 UTF-8，去掉前面的井号；已经去掉

```
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
	LANGUAGE = (unset),
	LC_ALL = (unset),
	LC_PAPER = "zh_CN.UTF-8",
	LC_ADDRESS = "zh_CN.UTF-8",
	LC_MONETARY = "zh_CN.UTF-8",
	LC_NUMERIC = "zh_CN.UTF-8",
	LC_TELEPHONE = "zh_CN.UTF-8",
	LC_IDENTIFICATION = "zh_CN.UTF-8",
	LC_MEASUREMENT = "zh_CN.UTF-8",
	LC_TIME = "zh_CN.UTF-8",
	LC_NAME = "zh_CN.UTF-8",
	LANG = "en_US.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to a fallback locale ("en_US.UTF-8").

```

```
root@ubuntu:~# vi ~/.bashrc
root@ubuntu:~# source ~/.bashrc

```

export LC_ALL=en_US.UTF-8

6. 安装shadowsocks-libev
```
docker run --name shadowsocks-app --detach --publish 55688:8338 shadowsocks/shadowsocks-libev -k "" -m "aes-256-cfb"
```

https://github.com/shadowsocks/shadowsocks-libev/tree/master/docker/ubuntu

7. 安装sudo pip install -U docker-compose

http://www.dockerinfo.net/docker-compose-%E9%A1%B9%E7%9B%AE