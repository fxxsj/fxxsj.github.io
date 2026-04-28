参照官方文档也很容易搭建起来，这里简单记录一下。

我用的是aaPanel加Docker环境。

### 编译Docker镜像

1. 克隆仓库代码，我放在了/www/wwwroot/token_pay文件夹里了，方便后面的操作。

```sh
git clone https://github.com/LightCountry/TokenPay.git
```

2.由于美化支付界面，我首先替换了`/src/TokenPay/wwwroot/lib/bootstrap/dist/css/bootstrap.min.css`文件。

3.然后进入源码目录
```sh
cd TokenPay/src
```

4. 执行编译镜像
```sh
docker build -t token-pay .
```

5. 查看编译后的镜像
```sh
docker images
```
### Docker容器运行

1.我在/www/wwwroot/token_pay/目录新增了config文件夹，创建了一个`TokenPay.db`空白文件，将`appsettings.json`填写好，也放了进去。

2.执行以下命令
```sh
docker run -d -v /www/wwwroot/token_pay/config/appsettings.json:/app/appsettings.json -v /www/wwwroot/token_pay/config/TokenPay.db:/app/TokenPay.db --name token-pay    -p 5555:80    token-pay
```
3.如果一切正常的话，Telegram Bot会发送以下消息
```sh
你好呀~我是TokenPay通知机器人！
```
注意：如启动失败，可尝试将整个TokenPay目录循环设置777权限，再重新尝试启动

### 反代网址

1.添加一个纯静态网站，配置反向代理 http://127.0.0.1:5555

2.这里有个坑Proxy name如果忘记填写会报数据库名称字符不对。
![image](https://github.com/user-attachments/assets/1a12a0df-7cfe-4a92-a9bf-fbd154a2badd)

### 结束

存在问题：过期时间显示时区不对，我也不知道怎么处理。于是没有理会。

这样就可以愉快地收款了。enjoy!
