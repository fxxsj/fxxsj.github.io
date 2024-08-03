### 前言
这个项目需要全新的服务器，这样会少点错误。

首先一键DD系统，使用的是以下项目 https://github.com/bin456789/reinstall

1. 下载脚本

```
curl -O https://raw.githubusercontent.com/bin456789/reinstall/main/reinstall.sh
```

2. 运行脚本DD  centos 9

```
bash reinstall.sh centos 9
```
使用debian 11 系统无法执行安装脚本，重新DD centos 9，然后发现这个项目仅适用于centos 7/8，只好后台重装。

3. 重启服务器

```
reboot
```
### 一、添加 PTR 记录
趁着这空闲，在服务商里设置一下ptr记录。
找到 VPS 后台，找到类似 `PTR Records`、`Reverse DNS` 等类似关键词的选项，设置反向解析。
![image](https://github.com/user-attachments/assets/14a18776-321d-437b-8590-942e48bc04e6)

### 二、设置 VPS

1. 关闭 Selinux

找到 VPS 文件 `/etc/sysconfig/selinux`

找到` SELINUX=enforcing` 改为 `SELINUX=disabled`

我这台本身就是关闭，但还检查一下好点。

2. 设置 Swap 分区
用的是低配小鸡，执行以下脚本
```
yum update
```
```
yum install curl
```
```
curl -O https://raw.githubusercontent.com/stilleshan/code/main/shell/swap.sh && chmod +x swap.sh && ./swap.sh
```
3. 设置 VPS 主机名
找到 VPS 文件：`/etc/hostname`

修改里面的内容为 `Mail`

找到 VPS 文件：`/etc/hosts`

添加以下内容：
```
127.0.0.1 mail.xxx.com smtp.xxx.com imap.xxx.com mail

# xxx.com 改为你的邮箱的主域名
```

至此，VPS 的设置完毕！

### 三、安装 EwoMail

1. 安装相关组件

```
yum  install wget
```

2. 查找安装命令
打开 [这个网站](http://www.ewomail.com/list-11.html) ，在里面填入你的域名（主域名），获取自动安装代码
![image](https://github.com/user-attachments/assets/9e0fe5f7-785d-47b0-84a3-0f61be147f73)
```
wget -c https://down.ewomail.com/install-04.sh && sh install-04.sh baidu.com
```

3. 安装完成请注意
若是你的 VPS 内存非常小，比如 512M，安装完毕以后，请立即执行：`systemctl stop clamd@amavisd` ，马上关闭杀毒软件，不然 VPS 很大概率无法响应！

安装 EwoMail 以后，会默认开启系统的防火墙，并开启以下的常用端口：
```
8000/tcp 8010/tcp 7000/tcp 7010/tcp 8020/tcp 993/tcp 995/tcp 
587/tcp 465/tcp 143/tcp 110/tcp 25/tcp 22/tcp 80/tcp 443/tcp
```
若是 SSH 登录端口并非 22 端口，或是其他软件需要放行端口，请参阅以下命令：
```
systemctl status firewalld   #状态
systemctl start firewalld  # 启动
systemctl stop firewalld   # 关闭
firewall-cmd --zone=public --list-ports       # 查看所有打开的端口
firewall-cmd --zone=public --add-port=80/tcp --permanent    # 永久开启80端口
firewall-cmd --reload      #重新载入防火墙
```
查看安装的域名和数据库密码（由于后期邮局数据的备份！！！！！）
```
cat /ewomail/config.ini
```

4. 设置 EwoMail
关闭邮件杀毒软件，可以有效的降低内存的占用，关闭后不影响垃圾邮件的检测。当然你的 VPS 内存大于 4G，可以忽略！

找到 VPS 文件：`/etc/amavisd/amavisd.conf`

在文件的最尾部加入以下代码：
```
@bypass_virus_checks_maps = (1);
```
找到 VPS 文件：`/usr/lib/systemd/system/amavisd.service`

在文件中找到 `Wants=clamd@amavisd.service` ，在前面加上 # 符号，注释掉这行代码

修改并保存完毕以后，依次在 VPS 执行以下命令：
```
systemctl daemon-reload
systemctl stop clamd@amavisd
systemctl disable clamd@amavisd
systemctl restart amavisd
```
### 四、域名解析

1. 整理 DKIM 信息

SSH 界面，输入以下命令：
```
amavisd -c /etc/amavisd/amavisd.conf showkeys
```
复制下图中红色框中内容
![image](https://github.com/user-attachments/assets/d3b18488-9443-4a6b-a749-306274ef6db6)

打开 [这个网站](http://www.ewomail.com/list-20.html) ，整理 DKIM 信息。
![image](https://github.com/user-attachments/assets/5269f403-dcc8-445f-a177-5d30ec76f3e1)
记录整理过的 DKIM 信息，解析的时候会用到！

2. 添加解析记录

来到 CF DNS 的解析页面，添加如下解析：（必须关闭黄色小云朵 – 代理状态关闭）,共计 八项 解析记录
![image](https://github.com/user-attachments/assets/b11d315d-274e-45ef-a1a2-12373942bc2f)

需要的字符串：
```
v=spf1 ip4:127.0.0.1 -all   #SPF 记录值 127.0.0.1 改为你的 VPS IP
dkim._domainkey             #DKIM 记录名称
_dmarc                      #DMARC 记录名称
v=DMARC1; p=none;           #DMARC 记录值
```

### 五、开启 SSL 访问和发送

1. 邮局前台开启 SSL 访问
找到 VPS 文件 `/ewomail/nginx/conf/vhost/rainloop.conf`  ，改个名字，备份它！

找到 VPS 文件` /ewomail/nginx/conf/vhost/rainloop.conf.ssl` ，改名为` rainloop.conf`

依次执行下面命令
```
yum install -y curl socat
```
```
curl https://get.acme.sh | sh
```
```
export CF_Key="请复制KEY到这里，不要删除引号"      # 设置 CF KEY
```
```
export CF_Email="xxx@xxx.com"                 # CF 的用户名
```
```
~/.acme.sh/acme.sh --register-account -m xxx@xxx.com
```
```
~/.acme.sh/acme.sh --issue --dns dns_cf -d *.baidu.com
```
```
~/.acme.sh/acme.sh --installcert -d *.baidu.com --key-file /etc/ssl/private/dovecot.pem --fullchain-file /etc/ssl/certs/dovecot.pem
```
```
service nginx restart
```
```
systemctl restart postfix dovecot
```
重启之后，刷新邮局，或是访问 https://mail.xxx.xxx:8000，这样就会开启 SSL 的访问。

2. 邮局后台开启 SSL 访问
和刚才一样，找到`/ewomail/nginx/conf/vhost/ewomail-admin-ssl.conf`  替换 `/ewomail/nginx/conf/vhost/ewomail-admin.conf `文件，特别需要注意里面的端口！

### 六、EwoMail 最后的设置

1. 检查服务

分别打开` https://mail.xxx.xxx:7010`（管理后台账号: `admin` 密码：`ewomail123`） 和 `https://mail.xxx.xxx:8000 `（邮局前台，账号密码在后台创建），看服务是否正常

### 鸣谢
本文摘录自 https://v2rayssr.com/ewomail.html