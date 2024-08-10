### 前言

使用VPS时，经常会遇到SSH爆破，一旦我们使用弱密码，无形中产生很多安全隐患。为了用户以及个人的信息安全，我推荐大家启用密钥登陆，同时关闭密码登陆。

其实强密码也可以达到密钥登陆的安全性，不过强密码带来的不便远远大于密钥方式，同时通过一键脚本开启密钥登陆比修改密码更快捷。

### 脚本来源

```
https://github.com/KiritoMiao/SSHKEY_Installer
```

### 准备

#### 生成SSH密钥
   首先，您需要通过运行以下命令来生成SSH密钥对：
 ```
   ssh-keygen -t rsa -b 4096 -C “your_email@example.com”
```
该命令将生成一对密钥：私钥（`id_rsa`）和公钥（`id_rsa.pub`）。默认情况下，它们将保存在`.ssh`目录中。

#### 添加公钥到GitHub账户
登录GitHub账户后，导航到[“Settings”（设置）页面](https://github.com/settings/keys)。
然后点击`SSH and GPG keys`（SSH和GPG密钥）选项卡。选择`New SSH key`（新建SSH密钥），将公钥内容复制粘贴到`Key`（密钥）文本框中，并为这个密钥提供一个描述性的标题。最后，点击`Add SSH key`（添加SSH密钥）按钮。

#### 同步密钥到其他设备
   现在你已经在一个设备上生成了SSH密钥并将公钥添加到了GitHub账户中。要同步到其他设备，只需要将私钥（`id_rsa`）和公钥（`id_rsa.pub`）（可选）复制到其他设备中`.ssh`文件夹下，并配置好GitHub账户即可。

#### 配置GitHub账户
   在其他设备上配置GitHub账户，首次使用Git进行操作时需要进行配置。打开终端或命令行工具，输入以下命令：
```
   git config –global user.name “Your Name”
   git config –global user.email “your_email@example.com”
```
   其中，”Your Name”和”your_email@example.com”需要替换成你在GitHub上注册的用户名和邮箱。

至此，你已经成功同步了GitHub密钥。通过这种方法，你可以在不同的设备上使用相同的密钥进行GitHub操作，避免了重复生成和配置密钥的麻烦。

#### 测试连接
    最后，您可以通过运行以下命令来测试SSH连接是否正常工作：
```
   ssh -T git@github.com
```
 如果您正确同步了GitHub密钥，您将收到一条消息
```
“Hi {username}! You’ve successfully authenticated, but GitHub does not provide shell access.”
（你好{用户名}！您已成功进行身份验证，但GitHub不提供shell访问。）
```

### 免密登录
接下来在vps上运行脚本即可实现免密登录。

```
wget https://github.com/fxxsj/SSHKEY_Installer/raw/master/key.sh --no-check-certificate&& bash key.sh fxxsj 

```

### 总结

开启方法非常简单，平时登陆也免除了输入密码的烦恼。

### 版权声明
本文参考
>博客[就是爱生活](https://www.94ish.me/1966.html)。

> https://worktile.com/kb/ask/525351.html

> 感谢原作者，以及开源世界。