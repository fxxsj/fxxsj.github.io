### 前言

使用VPS时，经常会遇到SSH爆破，一旦我们使用弱密码，无形中产生很多安全隐患。为了用户以及个人的信息安全，我推荐大家启用密钥登陆，同时关闭密码登陆。

其实强密码也可以达到密钥登陆的安全性，不过强密码带来的不便远远大于密钥方式，同时通过一键脚本开启密钥登陆比修改密码更快捷。

### 脚本来源

```
https://github.com/KiritoMiao/SSHKEY_Installer
```

### 准备

#### 首先我们需要生成一对密钥，通过Xshell生成是最简便的方式。

点击工具→用户密钥管理者→生成。
![](/img/jiaocheng/shell.png)
私钥我们需要妥善保存，不能传播出去，将公钥复制，导入Github中：https://github.com/settings/keys

#### 接下来我们运行脚本即可。

```
wget https://github.com/garhing/SSHKEY_Installer/raw/master/key.sh --no-check-certificate&& bash key.sh [你的Github用户名]
```

### 总结

开启方法非常简单，平时登陆也免除了输入密码的烦恼。

### 版权声明

>本文转载自博客[就是爱生活](https://www.94ish.me/1966.html)。
>
> 感谢原作者，以及开源世界。