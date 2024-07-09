# 0 前言

居家办公，手头一台 Windows 工作本，由于连接的是自己的外网，需要借助 VPN 才能登陆到内网环境下的远程服务器进行开发工作。此前一直使用 VNC 来做远程桌面可视化，但由于 VPN 带宽有限，此时再使用 VNC 会遇到明显的画面卡顿问题。

为解决上述开发流程的痛点，我们可以使用本地 vscode + Remote-SSH 插件 + 远程 vscode-server 搭建一个远程开发环境，将代码可视化放到本地，无需再借助 VNC。

# 1 准备

在开始环境配置前，需要满足一些基础前提条件：
- 已安装 SSH（Git 自带，最好直接安装 Git）
- 本地电脑已连接网络
- 本地电脑已连接 VPN（若远程服务器在内网下）
- 远程服务器已连接网络

# 2 环境配置

本文中所使用的本地操作系统环境为 Windows 10，远程操作系统环境 Linux Ubuntu 18.04。

## 2.1 配置本地环境

- 安装 [vscode](https://code.visualstudio.com/)
- 安装 [Remote-SSH 插件](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)

可以将其它 Windows 环境中已安装好的 vscode、扩展及相应的 settings.json 配置文件直接拷贝到本地对应目录下，实现免安装即开即用。vscode 放置路径随意，扩展放到本地 `C:\Users\你的用户名` 目录下，即 Windows 的 `~` 目录，settings.json 配置文件放到 `C:\Users\你的用户名\AppData\Roaming\Code\User` 目录下。

## 2.2 配置远程环境

1. 查看 vscode commit id 并下载对应版本的 vscode-server

   ![查看 vscode 构建信息](https://image.shipengx.com/%E6%9F%A5%E7%9C%8B%20vscode%20%E6%9E%84%E5%BB%BA%E4%BF%A1%E6%81%AF.png)

   ![提取 vscode commit id](https://image.shipengx.com/%E6%8F%90%E5%8F%96%20vscode%20commit%20id.png)

   下载链接的形式为：

   ```
   https://update.code.visualstudio.com/commit:commit_id/server-linux-x64/stable
   ```

   本文中使用的 vscode 的 commit id 为 `e18005f0f1b33c29e81d732535d8c0e47cafb0b5`，故对应下载链接为：

   ```
   https://update.code.visualstudio.com/commit:e18005f0f1b33c29e81d732535d8c0e47cafb0b5/server-linux-x64/stable
   ```

   下载将得到 `vscode-server-linux-x64.tar.gz` 文件。

2. 将 vscode-server 部署到远程服务器
   登陆远程服务器，在 `~` 目录下创建 `.vscode-server/bin` 目录：
   ```
   mkdir -p ~/.vscode-server/bin
   ```

   将下载得到的 `vscode-server-linux-x64.tar.gz` 文件上传至上述新建的 `~/.vscode-server/bin` 目录：
   ```
   scp vscode-server-linux-x64.tar.gz user_name@server_ip:~/.vscode-server/bin
   ```

   登陆远程服务器，解压 `vscode-server-linux-x64.tar.gz`：
   ```
   cd ~/.vscode-server/bin
   tar -zxvf vscode-server-linux-x64.tar.gz
   ```

   解压后将在 `~/.vscode-server/bin` 目录下生成 `vscode-server-linux-x64` 目录，将其改名为上文中得到的 vscode 的 commit id，并删除 `vscode-server-linux-x64.tar.gz`：

   ```
   mv vscode-server-linux-x64 e18005f0f1b33c29e81d732535d8c0e47cafb0b5
   rm vscode-server-linux-x64.tar.gz
   ```

   在这个以 vscode commit id 命名的目录中创建名为 `0` 的文件：

   ```
   cd ~/.vscode-server/bin/e18005f0f1b33c29e81d732535d8c0e47cafb0b5
   touch 0
   ```

   vscode-server 完成远程部署后不包含扩展，可将其它已安装有 vscode 的 Ubuntu 机器的 `~/.vscode` 目录下的 `extensions` 目录放到上文中创建的远程服务器的 `~/.vscode-server` 目录下，实现扩展离线安装，或通过下文中将提到的方法直接将本地扩展全部或部分在线安装到远程服务器。

## 2.3 配置本地连接远程

1. 打开本地 vscode，点击左下角黄色图标打开远程窗口

   ![打开远程窗口](https://image.shipengx.com/%E6%89%93%E5%BC%80%E8%BF%9C%E7%A8%8B%E7%AA%97%E5%8F%A3.png)

2. 连接到主机

   ![连接到主机](https://image.shipengx.com/%E8%BF%9E%E6%8E%A5%E5%88%B0%E4%B8%BB%E6%9C%BA.png)

3. 配置 SSH 主机

   ![配置 SSH 主机](https://image.shipengx.com/%E9%85%8D%E7%BD%AE%20SSH%20%E4%B8%BB%E6%9C%BA.png)

4. 编辑配置文件

   ![编辑配置文件 1](https://image.shipengx.com/%E7%BC%96%E8%BE%91%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%201.png)
   
   ![编辑配置文件 2](https://image.shipengx.com/%E7%BC%96%E8%BE%91%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%202.png)

5. 重复执行步骤 1 和步骤 2，并连接到已配置好的远程服务器

   ![连接到已配置好的远程服务器](https://image.shipengx.com/%E8%BF%9E%E6%8E%A5%E5%88%B0%E5%B7%B2%E9%85%8D%E7%BD%AE%E5%A5%BD%E7%9A%84%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8.png)

6. 在新弹出的窗口中选择远程服务器操作系统类型

   ![选择远程服务器操作系统类型](https://image.shipengx.com/%E9%80%89%E6%8B%A9%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E7%B1%BB%E5%9E%8B.png)

7. 输入远程服务器密码并回车，若弹出失败窗口，点击 retry 重试，再次输入密码并回车

   ![输入远程服务器密码并回车](https://image.shipengx.com/%E8%BE%93%E5%85%A5%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AF%86%E7%A0%81%E5%B9%B6%E5%9B%9E%E8%BD%A6.png)
   
   ![已成功连接远程服务器](https://image.shipengx.com/%E5%B7%B2%E6%88%90%E5%8A%9F%E8%BF%9E%E6%8E%A5%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8.png)

8. 将本地扩展安装到远程服务器

   ![将本地扩展安装到远程服务器 1](https://image.shipengx.com/%E5%B0%86%E6%9C%AC%E5%9C%B0%E6%89%A9%E5%B1%95%E5%AE%89%E8%A3%85%E5%88%B0%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%201.png)
   
   ![将本地扩展安装到远程服务器 2](https://image.shipengx.com/%E5%B0%86%E6%9C%AC%E5%9C%B0%E6%89%A9%E5%B1%95%E5%AE%89%E8%A3%85%E5%88%B0%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%202.png)

9. 打开远程工作目录

   ![打开远程工作目录 1](https://image.shipengx.com/%E6%89%93%E5%BC%80%E8%BF%9C%E7%A8%8B%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95%201.png)
   
   ![打开远程工作目录 2](https://image.shipengx.com/%E6%89%93%E5%BC%80%E8%BF%9C%E7%A8%8B%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95%202.png)
   
   ![再次输入远程服务器密码并回车](https://image.shipengx.com/%E5%86%8D%E6%AC%A1%E8%BE%93%E5%85%A5%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%AF%86%E7%A0%81%E5%B9%B6%E5%9B%9E%E8%BD%A6.png)
   
   ![信任打开的工作目录](https://image.shipengx.com/%E4%BF%A1%E4%BB%BB%E6%89%93%E5%BC%80%E7%9A%84%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95.png)
   
   ![已成功连接到远程服务器并打开工作目录，可以开始远程开发](https://image.shipengx.com/%E5%B7%B2%E6%88%90%E5%8A%9F%E8%BF%9E%E6%8E%A5%E5%88%B0%E8%BF%9C%E7%A8%8B%E6%9C%8D%E5%8A%A1%E5%99%A8%E5%B9%B6%E6%89%93%E5%BC%80%E5%B7%A5%E4%BD%9C%E7%9B%AE%E5%BD%95%EF%BC%8C%E5%8F%AF%E4%BB%A5%E5%BC%80%E5%A7%8B%E8%BF%9C%E7%A8%8B%E5%BC%80%E5%8F%91.png)

## 2.4 配置 SSH 免密登陆

完成上述步骤后，我们已经可以实现快速远程开发的需求，但每次登陆远程或由于网络不稳定导致重连都会要求我们输入远程的登陆密码，不免麻烦。由于本地 vscode 与远程 vscode-server 的连接走的是 SSH 协议，因此我们可以通过配置 SSH 免密登陆来省去所有要求输入密码的过程。

1. 生成 SSH 密钥对（若不存在得话）

   ```bash
   ssh-keygen -C "your email address"
   ```

   生成的密钥对被保存在本地的 `C:\Users\你的用户名\.ssh` 目录下，生成的密钥对包括私钥 `id_rsa` 和公钥 `id_rsa.pub`，我们需要将公钥中的内容添加到远程服务器。

2. 将 SSH 公钥添加到远程服务器

   ```plaintext
   ssh-copy-id user_name@server_ip
   ```

   输入上述命令以及远程服务器的登陆密码，将在远程服务器的 `~/.ssh` 目录下创建 `authorized_keys` 文件（若不存在得话），并将本地 SSH 公钥中的内容添加到该文件中。此后，任何从本地发起的指向远程服务器的 SSH 连接请求（如登陆、scp 文件传输等）将不再需要输入登陆密码。

# 3 总结

通过本地 vscode + Remote-SSH 插件 + 远程 vscode-server 的配置，可以实现在远程服务器上进行开发，并将代码可视化放到本地进行编辑，提高开发效率。此外，还可以通过安装扩展将本地的开发环境完整迁移到远程服务器上，保持一致的开发环境。

需要注意的是，远程开发时需要保持网络连接和 VPN 连接（若远程服务器在内网），以确保正常的开发和调试工作。同时，根据实际情况，可根据上述步骤配置多个远程服务器，方便不同环境下的开发工作。