### 说明：

`EH Forwarder Bot`是一个可扩展的聊天隧道框架，允许用户一次发送和接收来自多个`IM`平台的消息，并最终远程管理他们的帐户，目前可以实现的`Telegram`收发`QQ`、微信、`Facebook Messenger`等消息，你也可以同时一起收发`N`个微信、`N`个`QQ`等，这里就说下`Telegram`收发微信/`QQ`消息的手动安装及`Docker`安装。



### 收发微信

**项目地址：**<https://github.com/blueset/ehForwarderBot>

所使用的模块地址：

```
#Telegram模块
https://github.com/blueset/efb-telegram-master
#微信模块
https://github.com/blueset/efb-wechat-slave
```

其他模块地址→[传送门](https://github.com/blueset/ehForwarderBot/wiki/Channels-Repository)，包括`Facebook Messenger`等模块，有兴趣的可以看下。

**环境要求：**`Python 3.6+`、`EH Forwarder Bot 2.0+`、`ffmpeg`、`libmagic`、`libwebp`

手动教程适用于`Debian`、`CentOS`、`Ubuntu`，如果你想用`Ubuntu`的话，最好使用`18.04+`版本。

#### 1、安装依赖

```
#CentOS系统
yum install file-devel libwebp-tools git screen -y

#Debian/Ubuntu系统
apt install libwebp-dev libmagic-dev git screen -y
```

#### 2、安装Python3.6

```
#CentOS系统
wget https://www.moerats.com/usr/shell/Python3/CentOS_Python3.6.sh && sh CentOS_Python3.6.sh
#Debian系统
wget https://www.moerats.com/usr/shell/Python3/Debian_Python3.6.sh && sh Debian_Python3.6.sh
#Ubuntu系统
apt update
apt install python3-pip python3-setuptools python3-dev -y
```

#### 3、安装ffmpeg

```
#下载ffmpeg二进制
wget https://www.moerats.com/usr/down/ffmpeg/ffmpeg-git-$(getconf LONG_BIT)bit-static.tar.xz
#解压文件
tar xvf ffmpeg-git-*-static.tar.xz
#移动ffmpeg可执行文件
mv ffmpeg-git-*/ffmpeg  ffmpeg-git-*/ffprobe /usr/bin/
#删除文件
rm -rf ffmpeg-git-*
```

#### 4、安装框架

```
#安装稳定版
pip3 install ehforwarderbot

#安装开发版，建议安装开发版，bug修复快些，功能也新
pip3 install git+https://github.com/blueset/ehforwarderbot.git
```

#### 5、安装TG和微信模块

```
pip3 install efb-telegram-master efb-wechat-slave
```

#### 6、启用模块

先新建配置文件夹和配置文件`config.yaml`，使用命令：

```
#default为配置文件默认的文件夹，你也可以命名为其它的，不会玩的就默认
mkdir -p ~/.ehforwarderbot/profiles/default
nano ~/.ehforwarderbot/profiles/default/config.yaml
```

参考代码为：

```
#请根据实际情况修改
master_channel: foo.demo_master
slave_channels:
- foo.demo_slave
- bar.dummy
middlewares:
- foo.null
```

以上对应的均为模块名称，模块参考→[传送门](https://github.com/blueset/ehForwarderBot/wiki/Channels-Repository)，比如这里博主只用了`Telegram`和`WeChat`模块，所以大致配置为：

```
master_channel: blueset.telegram
slave_channels:
- blueset.wechat
```

然后使用`Ctrl+x`、`y`保存退出。

这只是登录一个微信号，如果你要同时登录多个微信号，那么配置文件就需要改为：

```
#比如我要同时登录并收发3个微信号
master_channel: blueset.telegram
slave_channels:
- blueset.wechat
- blueset.wechat#moe123
- blueset.wechat#rats321
```

只需要在后面使用`#`指定一个`ID`号，该`ID`号只能有字母，数字和下划线，即正则表达式`[a-zA-Z0-9_]+`，想登录几个账户就加几个。如果你使用`QQ`、`Facebook Messenger`模块的话，设置方法也一样。

#### 7、建立TG配置文件

建立配置文件前需要先获取`Telegram`的`Token`和`Userid`，获取方法如下：

```
#Telegram的Token获取
1、在Telegram关注@BotFather
2、再到对话框依次输入：/start=>/newbot，然后会要你给机器人命名(如：MoeratsBot)，命名完成会给你一个Token。

#Telegram群Userid获取
1、先和你的机器人聊天，随便发一句话。
2、在浏览器输入https://api.telegram.org/botxx:xx/getUpdates(其中xx:xx为Token)，然后chat后面的id即为你的userid。
```

再新建一个`Telegram`模块配置文件夹和配置文件`config.yaml`，使用命令：

```
#同样的也建在default文件夹，如果你上面更改了default文件夹，那这里也要更改
mkdir ~/.ehforwarderbot/profiles/default/blueset.telegram
nano ~/.ehforwarderbot/profiles/default/blueset.telegram/config.yaml
```

填入以下代码：

```
token: "12345:moerats" 
admins:
- 765432 
```

然后使用`Ctrl+x`、`y`保存退出。上面所对应的参数分别为`Token`和`Userid`。关于`Telegram`模块的更多玩法可以参考→[传送门](https://github.com/blueset/efb-telegram-master)。

#### 8、启动

```
#该命令会默认从default文件夹读取信息，如果你之前建的是moerats文件夹，那命令应该为ehforwarderbot -p moerats
ehforwarderbot
```

这时候会给一个微信二维码或者二维码链接你，放到浏览器打开扫描登录即可，如果你设置了同时登录多个账户，那设置几个就需要登录几个。

然后使用`Ctrl+C`断开运行，再使用命令后台运行：

```
screen -dmS EHF ehforwarderbot
```

最后你的微信消息会通过机器人发送给你，你也可以通过机器人将消息发送给指定好友。



### 收发QQ消息

```
提示：这里随便提了下，了解下就行了，建议使用下面Docker方式安装。
```

所使用的模块地址：

```
#Telegram模块
https://github.com/blueset/efb-telegram-master
#QQ模块
https://github.com/milkice233/efb-qq-slave
```

由于方法写的很大概，所以需要你把收发微信的方法看懂，这里`EH Forwarder Bot`只支持`酷Q`客户端，一般采用`Docker`的方法在`Linux`上安装酷`Q`，方法很久以前就说过了，参考→[传送门](https://www.moerats.com/archives/802/)，不过启动命令变了下，也就是安装`wine-coolq`的命令。

#### 安装`TG`和`QQ`模块：

```
pip3 install efb-telegram-master efb-qq-slave
```

#### 安装`wine-coolq`：

```
mkdir coolq  #包含CoolQ程序文件
docker run -ti --rm --name cqhttp-test --net="host" \
     -v $(pwd)/coolq:/home/user/coolq     `#mount coolq folder` \
     -p 9000:9000                         `#网页noVNC端口` \
     -p 5700:5700                         `#酷Q对外提供的API接口的端口` \
     -e VNC_PASSWD=MAX8char               `#请修改VNC密码！！！！` \
     -e COOLQ_PORT=5700                   `#酷Q对外提供的API接口的端口` \
     -e COOLQ_ACCOUNT=123456              `#在此输入要登录的QQ号，虽然可选但是建议填入` \
     -e CQHTTP_POST_URL=http://127.0.0.1:8000   `#efb-qq-slave监听的端口/地址用于接受传入的消息` \
     -e CQHTTP_SERVE_DATA_FILES=yes       `#允许以HTTP方式访问酷Q数据文件` \
     -e CQHTTP_ACCESS_TOKEN=ac0f790e1fb74ebcaf45da77a6f9de47  `#Access Token` \
     -e CQHTTP_POST_MESSAGE_FORMAT=array  `# 回传消息时使用数组(必选)` \
     richardchien/cqhttp:latest
```

然后使用`ip:9000`访问`noVNC`登录`酷Q`即可。

#### 新建`QQ`模块配置文件：

```
mkdir ~/.ehforwarderbot/profiles/default/milkice.qq
nano ~/.ehforwarderbot/profiles/default/milkice.qq/config.yaml
```

填入的代码大致如下:

```
Client: CoolQ                         #指定要使用的QQ客户端（此处为CoolQ）
CoolQ:
    type: HTTP                        #指定efb-qq-slave与酷Q通信的方式 现阶段仅支持HTTP
    access_token: ac0f790e1fb74ebcaf45da77a6f9de47
    api_root: http://127.0.0.1:5700/  # 酷Q API接口地址/端口
    host: 127.0.0.1                   # efb-qq-slave所监听的地址用于接收消息
    port: 8000                        # 同上
    is_pro: false                      # 若为酷Q Pro则为true，反之为false
    air_option:                       # 包含于air_option的配置选项仅当is_pro为false时才有效
        upload_to_smms: true          # 将来自EFB主端(通常是Telegram) 的图片上传到sm.ms服务器并以链接的形式发送到QQ端
```

最后使用`ehforwarderbot`命令启动即可。



### Docker安装

这里选择`2`个最新的`Docker`镜像，也是官方推荐的，项目地址：

```
#Telegram收发QQ消息
https://github.com/Earth-Online/efb-qq-coolq-docker
#Telegram收发微信消息
https://www.github.com/Mikubill/efb-wechat-docker
```

#### 1、安装Docker

```
#CentOS 6
rpm -iUvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum update -y
yum -y install docker-io
service docker start
chkconfig docker on

#CentOS 7、Debian、Ubuntu
curl -sSL https://get.docker.com/ | sh
systemctl start docker
systemctl enable docker
```

#### 2、Telegram收发QQ消息

安装`docker-compose`：

```
curl -L https://github.com/docker/compose/releases/download/1.23.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
#验证是否安装成功
docker-compose --version
#返回以下信息即安装成功
docker-compose version 1.23.2, build 1110ad01
```

拉取`Docker`源码：

```
git clone https://github.com/Earth-Online/efb-qq-coolq-docker.git
cd efb-qq-coolq-docker
#编辑config.yaml配置文件
nano ehforward_config/profiles/default/blueset.telegram/config.yaml
```

修改如下：

```
#token和userid参数获取方法查看上面的手动安装教程
token: "你的TG机器人Token"
admins:
- 你的Userid
```

然后再编辑`docker-compose.yml`文件：

```
nano docker-compose.yml
```

修改如下：

```
- VNC_PASSWD=你的密码
- COOLQ_ACCOUNT=你的qq账号
```

后台启动：

```
#第一次启动会构建镜像，所以会慢点
docker-compose up -d
```

然后打开`ip:9801`登陆`novnc`后完成`coolq`登陆操作。如果该地址打不开，请检查下防火墙。

#### 3、Telegram收发微信消息

```
#拉取源码
git clone https://github.com/mikubill/efb-wechat-docker.git
#构建镜像
cd efb-wechat-docker && docker build -t mikubill/efbwechat .
#启动镜像，TOKEN为TG机器人Token、ADMIN为你的Userid，获取方法查看上面的手动安装教程
docker run -d -t --name "efbwechat" -e TOKEN=xxxx -e ADMIN=xxxx mikubill/efbwechat
```

最后获取微信登录验证码，使用命令：

```
docker logs -f efbwechat 
```

扫描登录即可。

最后这里都没有给微信添加额外的配置文件，直接使用默认的微信配置，如果想扩展微信功能的可以参考→[传送门](https://github.com/blueset/efb-wechat-slave#配置文件例)，不过由于模块使用的微信网页版，所以支持的功能是有限的，比如：没有朋友圈、不能发语音、位置等等，一般来说也够用了，至于`QQ`的话，功能肯定受`酷Q`限制，暂时不能处理好友请求处理，加群请求处理，语音发送/接收等，对于`Facebook Messenger`模块的话，有需求的可以自己试试安装配置。

### 版权声明

>本文转载自[Rat's Blog](https://www.moerats.com/archives/931/)。
>
> 感谢原作者，以及开源世界。