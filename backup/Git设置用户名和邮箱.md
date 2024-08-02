### 前言
提交git记录前需要先设置用户名和邮箱，记录一下设置方式。

最常用的方式就是全局设置，当不方便全局设置时，可以在项目路径下单独设置。


### 全局设置

```
git config --global user.name yourname
```

```
git config --global user.email email@yourmail.com
```
查看可使用以下命令

```
git config -l
```

### 单独设置

1. 方式一

在项目根目录执行如下命令

```
git config --local user.name yourname
```
```
git config --local user.email email@yourmail.com
```


查询命令
```
git config --local -l
```

2. 方式二

直接在项目配置文件`git/config`中设置

```
[user]
    name = yourname
    email = email@yourmail.com
```