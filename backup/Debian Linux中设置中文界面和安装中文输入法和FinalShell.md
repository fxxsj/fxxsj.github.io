## 前言
之前我一直使用 CentOS 7，但由于已停止维护，遇到很多兼容性问题。多番选择后，决定改用 Debian。  
作为新手，我主要以 **root 用户** 进行操作（否则需要在命令前加 `sudo`）。

本文记录了 Debian 上 **中文环境、输入法、FinalShell、远程桌面、浏览器修复** 等常见配置。

---

## 一、Debian 中文化

### 1. 安装中文字体
```bash
apt install fonts-wqy-zenhei
````

### 2. 安装 locales 支持

```bash
apt install locales
```

### 3. 编辑 locale.gen

```bash
vi /etc/locale.gen
```

取消注释：

```
zh_CN.UTF-8 UTF-8
```

如果没有，就手动添加。

### 4. 创建/修改 locale.conf

```bash
vi /etc/locale.conf
```

写入：

```
LANG=zh_CN.UTF-8
```

### 5. 重新生成 locales

```bash
dpkg-reconfigure locales
```

### 6. 重启生效

```bash
reboot
```

---

## 二、安装中文输入法

Debian 默认支持多种输入法框架：`fcitx`、`ibus`、`uim`。这里选择 **ibus + rime**。

### 1. 安装 ibus-rime

```bash
apt install ibus-rime
```

### 2. 重启/重新登录

注销用户或重启系统后，在 **系统设置 → 输入法** 中添加 ibus-rime。

### 3. 导入词库

预置配置和词库位于：

```
/usr/share/rime-data/
```

可将自己备份的词库复制到该目录。

---

## 三、安装 FinalShell

FinalShell 是一款跨平台的 SSH 工具。
下载地址：[[FinalShell 官网](https://www.hostbuf.com/)](https://www.hostbuf.com/)

### 1. 下载 Linux X64 版本安装包

文件名示例：

```
finalshell_linux_x64.deb
```

### 2. 安装 deb 包

```bash
dpkg -i finalshell_linux_x64.deb
```

### 3. 修复依赖

```bash
apt install -f
```

---

## 四、开启远程桌面 (3389 / xrdp)

如果需要使用 Windows 自带的 **远程桌面 (mstsc)** 连接 Debian：

### 1. 安装轻量桌面 + xrdp

```bash
apt update
apt install -y xfce4 xfce4-goodies
apt install -y xrdp
```

### 2. 设置默认会话为 Xfce

```bash
echo "startxfce4" > ~/.xsession
chmod +x ~/.xsession
```

或者修改 `/etc/xrdp/startwm.sh` 文件，在结尾加：

```
exec startxfce4
```

### 3. 启动并设置开机自启

```bash
systemctl enable --now xrdp
systemctl status xrdp
```

### 4. 防火墙放行 3389 端口

```bash
ufw allow 3389/tcp
ufw reload
```

（若使用云服务器，还需在云平台安全组开放 3389）

### 5. Windows 端连接

在 Windows 的远程桌面里输入：

```
<服务器IP>:3389
```

使用 Debian 用户名和密码登录。

---

## 五、修复默认浏览器错误

有时会遇到以下报错：

```
Failed to execute default Web Browser.
Input/output error.
```

### 1. 安装浏览器

推荐安装 Firefox ESR：

```bash
apt install -y firefox-esr
```

或安装 Chromium：

```bash
apt install -y chromium
```

### 2. 设置默认浏览器

```bash
xdg-settings set default-web-browser firefox-esr.desktop
# 或者
xdg-settings set default-web-browser chromium.desktop
```

### 3. 检查配置

```bash
xdg-settings get default-web-browser
```

应返回 `firefox-esr.desktop` 或 `chromium.desktop`。

---

## 六、常见问题与排错

### 1. xrdp 黑屏 / 会话断开

* 检查 `~/.xsession` 是否包含 `startxfce4`
* 查看日志：

  ```bash
  journalctl -u xrdp -b --no-pager
  journalctl -u xrdp-sesman -b --no-pager
  ```

### 2. 输入法不生效

* 确认 `ibus-daemon` 已运行：

  ```bash
  ps aux | grep ibus-daemon
  ```
* 系统设置里选择正确的输入法框架。

### 3. FinalShell 启动不了

* 确认 Java 环境正常（FinalShell 自带，通常无需手动安装）。
* 如果报依赖错误，执行：

  ```bash
  apt install -f
  ```

### 4. 安全性建议

* 远程桌面 (3389) 暴露公网风险大，建议：

  * 配合防火墙限制来源 IP
  * 或使用 SSH 隧道转发 3389

---

## 总结

经过以上步骤，你的 Debian 系统将具备：

* 中文界面
* 中文输入法 (ibus-rime)
* 远程桌面 (3389 / xrdp)
* 默认浏览器 (Firefox/Chromium)
* FinalShell SSH 工具

非常适合新手从 CentOS 平滑过渡到 Debian 桌面使用与远程管理。

```