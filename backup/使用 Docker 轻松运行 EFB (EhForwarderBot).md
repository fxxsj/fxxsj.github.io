## 什么是 EFB？为什么要用 Docker？

EFB (EhForwarderBot) 是一个强大的工具，可以帮你把微信消息转发到 Telegram 等其他聊天平台，也可以让你在 Telegram 里直接回复微信消息。简单来说，就是让你在一个地方管理多个聊天工具的消息。

Docker 又是什么呢？你可以把它想象成一个"魔法集装箱"。我们把 EFB 和它运行需要的所有东西都打包到这个集装箱里。这样，无论你的服务器是什么环境，只要装上 Docker，就能轻松运行 EFB，省去了很多复杂的安装和配置步骤。

这篇教程会教你如何使用 Docker 来运行 EFB。

## 背景小故事

之前我用的微信网页版登录方式 (uos 补丁) 失效了，而且账号还收到了警告，所以我切换到了一个叫做 comwechat 的新方案。下面会介绍这两种方案的区别。

## 微信接入方案：comwechat vs wechat web

EFB 连接微信主要有两种方式，你需要根据自己的情况选择一种：

### wechat web (微信网页版)
✅ **优点**：
- 占用资源少，对服务器要求低，小内存 VPS 也能跑
- 模拟浏览器登录微信网页版

❌ **缺点**：
- 功能相对较少
- 只有部分老微信账号（大约 2017 年前注册）才能登录网页版

### comwechat
✅ **优点**：
- 功能更全，支持发送高清图片和 Telegram 贴纸
- 基于开源项目模拟 Windows 微信客户端

❌ **缺点**：
- 占用资源较多（内存建议 2GB 以上）

## 功能对比

| 功能               | wechat web (网页版) | comwechat (模拟客户端) |
|--------------------|-------------------|---------------------|
| 查看撤回消息       | ✅ 支持           | ❌ 不支持            |
| 回复消息样式       | 普通文本样式      | 部分支持原生样式    |
| 发送 Telegram 贴纸 | 可能会被压缩      | ✅ 支持             |
| 发送高清图片       | 大图会被压缩      | ✅ 支持             |

## 选择建议

- **选择 wechat web** 如果：
  - 微信账号还能登录网页版
  - 对资源占用敏感

- **选择 comwechat** 如果：
  - 无法登录网页版
  - 需要完整功能（高清图/贴纸）
  - 服务器资源充足（建议 2GB 内存）

## 第一步：安装 Docker

```bash
# 下载 Docker 官方安装脚本
curl -fsSL https://get.docker.com -o get-docker.sh

# 执行安装脚本
sudo sh get-docker.sh

# 删除安装脚本（可选）
rm get-docker.sh
```

## 第二步：下载 EFB 配置模板

```bash
git clone https://github.com/jiz4oh/ehforwarderbot.git ehforwarderbot
```

项目结构：
```
.
├── Dockerfile                  # Docker 构建镜像的说明书 (一般不用管)
├── README.md                   # 项目说明文件
├── docker-compose.web.yaml     # 用于启动 wechat web 版本的配置文件
├── docker-compose.yaml         # 用于启动 comwechat 版本的配置文件
├── entrypoint.sh               # Docker 容器启动时运行的脚本 (一般不用管)
├── profiles/                   # 核心配置文件夹！存放不同账号的配置
│   ├── comwechat/              # 第一个 comwechat 账号的配置
│   │   ├── ... (一些中间件配置，可以先忽略)
│   │   ├── blueset.telegram/
│   │   │   └── config.yaml     # !! Telegram 配置，需要修改 !!
│   │   ├── config.yaml         # EFB 主配置 (一般不用改)
│   │   └── honus.comwechat/
│   │       └── config.yaml     # comwechat 配置 (一般不用改)
│   ├── comwechat2/             # 如果你有第二个微信账号想用 comwechat，可以复制 comwechat 文件夹并改名
│   │   └── ...
│   └── default/                # wechat web 版本的配置 (网页版只需这一个)
│       ├── blueset.telegram/
│       │   └── config.yaml     # !! Telegram 配置，需要修改 !!
│       ├── blueset.wechat/
│       │   └── config.yaml     # 微信网页版配置 (可选修改)
│       └── config.yaml         # EFB 主配置 (一般不用改)
└── run2.py                     # (comwechat 辅助脚本，一般不用管)
```

## 第三步：修改配置

编辑 Telegram 配置文件：
```bash
nano profiles/comwechat/blueset.telegram/config.yaml
```

需要修改的字段：
```yaml
token: "你的Telegram Bot Token"
admins: 
  - 你的Telegram User ID
```

## 第四步：启动 EFB

comwechat 方案：
```bash
docker-compose up -d
```

wechat web 方案：
```bash
docker-compose -f ./docker-compose.web.yaml up -d
```

## 首次登录
```bash
# 查看登录二维码
docker-compose logs -f
```

## 常用命令
| 操作 | 命令 |
|------|------|
| 查看日志 | `docker-compose logs -f` |
| 停止服务 | `docker-compose down` |
| 更新配置 | `git pull && docker-compose pull` |
```