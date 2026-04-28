一直都是用的 Chrome 自带的密码管理器，所有设备同步也很方便，但出于折腾，我还是换成了vaultwarden。
环境还是aaPanel加Docker，最近真是越来越喜欢Docker。

### 创建文件夹
我设置在 `/www/wwwroot/bitwarden` 所有的资料都在其中。

### 生成密钥

使用下列命令创建一个argon2密钥，将MySecretPassword替换为你想要设定的管理密码。

```
echo -n "MySecretPassword" | argon2 "$(openssl rand -base64 32)" -e -id -k 65540 -t 3 -p 4
```

你会得到一个类似下面的值
```
$$argon2id$$v=19$$m=19456,t=2,p=1$$UUZxK1FZMkZoRHFQRlVrTXZvS0E3bHpNQW55c2dBN2NORzdsa0Nxd1JhND0$$cUoId+JBUsJutlG4rfDZayExfjq4TCt48aBc9qsc3UI
```
如果提示argon2: command not found，则需要手动安装argon2。
### Docker 镜像部署

我们使用 Docker，仅需一个命令便可以方便地部署 Vault­war­den
```
docker run -d \
--name=vaultwarden \
-e WEBSOCKET_ENABLED=true \
-e ADMIN_TOKEN='$argon2id$v=19$m=65540,t=3,p=4$cmFGMGZOaUVhNzTVdMak1PKzV6M2lKSHVHcHBEYjVxbE95VnowNWgyVT0$HA8UXq1sarEFRaOi3AVV65W0nNrBN+Y7/VNvkoEL8tg' \
-p 6666:80 -p 3012:3012 \
-v /www/wwwroot/bitwarden/:/data/ \
--restart=always  \
vaultwarden/server:latest
```
### 反代网址
添加一个纯静态网站，配置反向代理 http://127.0.0.1:6666/

具体上篇说过了。

### 参考
1. https://www.ottoli.org/howto/bitwarden-vaultwarden
2. https://lot.pm/vaultwarden-selfhosted.html