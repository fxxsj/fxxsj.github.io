今天发现服务器小鸡内存快用完，上去就是一顿操作。然后想到我的EFB好像很久没有更新了，一查看，是2019年7月部署，用得也是很稳定。但太久没有更新，总是有点手痒。

此次部署使用的是这个项目 https://github.com/wbbk/efb-update ，基本上按照Readme操作即可。这里简单记录一下。

### 克隆仓库

```shell
git clone https://github.com/wbbk/efb-update efb-update
```

### 编辑配置文件

1. 主配置文件 `profiles/default/config.yaml`
    - `middlewares` 定义了启用的转发通道和中间件
        - `catbaron.voice_recog` 语音转文字
        - `patch.PatchMiddleware` 手机微信标记已读
        - 默认启用两个插件，如果不需要某个插件，删除或注释对应的行即可
        - 如果两个都不需要，可以直接删除或注释 `middlewares` 小节

2. Telegram 配置 `profiles/default/blueset.telegram/config.yaml`
    - `token` Telegram 的 bot token
    - `admins` Telegram 账号的数字 ID
    - 详情可参考 [efb-wechat-docker 绑定过程记录 | HE-SB-技术栈](https://tech.he-sb.top/posts/usage-of-efbwechat-docker/)

3. wechat 配置 `profiles/default/blueset.wechat/config.yaml`
    - 可用的配置及含义参考插件仓库： [ehForwarderBot/efb-wechat-slave](https://github.com/ehForwarderBot/efb-wechat-slave?tab=readme-ov-file#%E5%AE%9E%E9%AA%8C%E5%8A%9F%E8%83%BD)

4. 插件 `catbaron.voice_recog` 配置 `profiles/default/catbaron.voice_recog/config.yaml`
    - 配置方法参考插件仓库： [catbaron0/efb-voice_recog-middleware](https://github.com/catbaron0/efb-voice_recog-middleware)

5. 插件 `patch.PatchMiddleware` 配置 `profiles/default/patch.PatchMiddleware/config.yaml`
    - `auto_mark_as_read` 是否自动在手机微信标记已读
    - `remove_emoji_in_title` 是否移除 Telegram 群组名称中的 emoji
    - 其他可用配置参考插件仓库： [ehForwarderBot/efb-patch-middleware](https://github.com/ehForwarderBot/efb-patch-middleware)

说明：由于之前部署过，配置文件直接复制粘贴过来了。插件 `catbaron.voice_recog` 因为要用api,我没有配置,在主配置文件里也注释了。其他的都是默认。

### 迁移

1.按备份说明，容器内所有数据都在 `efb-update/profiles` 路径下，我对比了一下，原先用我上一篇教程写的搭建的同样有这个文件夹，于是复制了日期较新的文件过去。

2.由于我用的是之前的配置文件，我觉得要先退出原来的进程，使用以下命令列出所有当前运行的 screen 会话：

```shell
screen -ls 
```

3.停止会话：

```shell
screen -S EHF -X quit
```
4.最后退出微信网页登录。

### 启动容器

1.本地构建后启动，我试过没有成功，想着有另外的方法也就没有深入尝试了。我用的是直接使用。

2.直接使用

2.1 编辑好配置文件后，进入所在文件夹

```shell
cd efb-update  
```

2.2启动镜像，这里原来的教程没有横杠，我使用时报错，后来GPT帮忙加上了

```shell
docker-compose up -d
```

2.3启动镜像没有报错的话输入下面这条命令

```shell
docker logs -f efb-wechat
```

### 更新成功

一路下来很顺利，稍等片刻终端显示二维码的连接，复制打开后用手机微信扫描，确认登陆即可。

之前设置好的群连接都在，就是使用命令 /link 时CPU占用100%,其他没什么问题。更新成功。






