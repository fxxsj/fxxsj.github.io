### 上传代码

#### 创建Repository

首先在github上注册一个帐号，创建自己的Repository，记住新建仓库的https地址。

#### 创建Readme自述文件

```code
echo "# Test" >> README.md
```

#### 建立git仓库

在本地项目的目录里执行以下命令

```code
git init
```

#### 添加文件

将项目的所有文件添加到仓库中

```code
git add .
```

```
git add README.md
```

#### 提交到仓库

```
git commit -m "注释语句"
```

#### 关联仓库

将本地的仓库关联到GitHub，后面的https改成刚刚自己的地址

```
git remote add origin https://github.com/fxxsj/Test.git
```

#### 上传代码

上传github之前pull一下

```
git pull origin master
```

上传代码到GitHub远程仓库

```
git push -u origin master
```

中间可能会让你输入Username和Password，你只要输入github的账号和密码就行了。执行完后，如果没有异常，等待执行完就上传成功了。

### 更新代码

#### 查看当前的git仓库状态

```
git status
```

#### 更新全部

```
git add *
```

#### 提交更新说明

```
git commit -m "更新说明"
```

#### 拉取当前分支最新代码

```
git pull
```

#### 同步代码

```
git push origin master
```

如何没有报错，代码已经同步到GitHub了。