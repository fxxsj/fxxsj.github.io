
### 前言
我对Linux系统不熟悉，之前一直用cnetos7，最近停止维护了，遇到很多问题。多番选择之后，决定改用Debian。

### Debian汉化


由于是新手，多用户都玩不转，默认使用`root`用户，否则以下命令需在前面加上`sudo`。

1、安装字体：


```
apt install fonts-wqy-zenhei

```
2、安装`locales`

```
apt install locales
```
3、设置`/etc/locale.gen`


有很多教程都使用`nano`,我没有接触过，于是我用`vi`，至少知道`:wq`。


```
vi /etc/locale.gen

```
将`zh_CN.UTF-8 UTF-8`前面的注释去掉，或者将其填入。

4、设置`/etc/locale.conf` (没有这个文件就创建一个)

```
vi /etc/locale.conf  #在这里写入 LANG=zh_CN.UTF-8

```
5、运行`dpkg-reconfigure locales`  ，注意要`sudo` 运行。

```
dpkg-reconfigure locales
 ```
等待设置完成

```
reboot
 ```
退出Debian 系统，重新进入就会看到中文。

 

### 安装中文输入法：

系统自带了几个输入法平台：fcitx、ibus，uim，我选择使用ibus框架实现的rime输入法。
1. 使用 ibus 版本很简单：
```
apt install ibus-rime
```
之后，重启或者注销当前用户对话，在系统内添加 ibus 。

2.导入词库
预置的配置文件和词库均位于`/usr/share/rime-data/`当中,导入自己备份的词库文件即可。

### 安装Fanelshell

1. 在[FinalShell官网](https://www.hostbuf.com/t/988.html)下载Linux X64版FinalShell安装包：[finalshell_linux_x64.deb](http://www.hostbuf.com/downloads/finalshell_linux_x64.deb)

2. 在安装包所在目录打开终端：

```
dpkg -i finalshell_linux_x64.deb

```
如果提示错误，再执行下列命令：

```
apt install -f
```