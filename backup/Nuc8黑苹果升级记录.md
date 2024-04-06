我的Nuc8在20年5月底购于闲鱼，买来的时候上个机主已装好了macOS Catalina 10.15.5，具体有没有硬改过我也不清楚。这两年用mba比较多，这小东西就被我束之高阁了。
昨天突然想起，打算将其重装为windows11。在网上看了一些内容，弄好U盘启动盘后开干，发现磁盘格式不对，手头又没有工具，于是放弃了这一想法。
后来想想，能不能升级一下macOS系统呢？于是一整个下午都在查询资料，当中还在两个群里发起提问，没人理，哈。直到今天下午，终于发现了可以执行的指导文章。再次开干。

### 一、迁移到 OC

按我查询到的内容，上个机主使用的应该是 Clover 方式引导，而现在更主流的方式其实是 OpenCore（简称OC），反正我搜索的文章基本上都是基于OC，遂迁移。

#### 1.替换文件

这个步骤用到了一个 `Clover Configurator` 的程序，谷歌一下，我在 https://mackie100projects.altervista.org/download-clover-configurator/ 找到了它。

![01](https://telegraph-image-c7v.pages.dev/file/ef1cdef4debe1b6bca65e.png)

首先，打开 Clover Configurator，挂载本机 EFI 分区，然后打开分区。

![02](https://telegraph-image-c7v.pages.dev/file/9004411d19b4f4a097c09.png)

先备份一下原EFI文件夹，拷贝一份到桌面即可，我当时为了保险起见，还复制了一份到U盘（其实没有一点用）。
这里用到一个关键的EFI文件夹。我在 [github](https://github.com/Jiangmenghao/NUC8i5BEH) 找到了它。

我的Nuc8是单系统，当时EFI文件夹里有APPLE、BOOT和CLOVER三个文件夹，指导文章里没有提到APPLE文件夹，让我好一阵困惑，看了截图及对比文件，最后我决定保留它。然后我删除了BOOT和CLOVER两个文件夹，将刚刚下载的EFI文件夹里的BOOT和OC两个文件夹丢进去，同时删除OC文件夹里的`config-双系统.plist`文件。

![03](https://telegraph-image-c7v.pages.dev/file/d6d06dc1e53efca9b3bce.png)

#### 2.迁移三码

这个步骤用到了一个`三码迁移工具（MacSerialTransporter）` ，我在 [github](https://github.com/haoke123/MacSerialTransporter) 找到了它。

左边选择刚才备份的/EFI/CLOVER/config.plist，右边打开挂载EFI分区的/EFI/OC/config.plist，最后点击中间的>>符号完成迁移。

![04](https://telegraph-image-c7v.pages.dev/file/e07f3538dc7565c81505c.png)

#### 3.修复启动

修改完成后就可以重启，按开机键后，连续点击 ESC 键进入 OC 引导界面，按空格显示隐藏选项，接着选中最后一项「Reset NVRAM」，回车键执行。这里我选完后还要选一下启动盘才会进入系统。

![05](https://telegraph-image-c7v.pages.dev/file/96c3541f57ed72c0ae0bd.png)

一切非常顺利，我成功进入了系统。

### 二、升级系统

接下来我尝试了一下从macOS Catalina 10.15.5升级到10.15.7，————昨天我其实已经试了，没替换EFI时升级失败。

大约30分钟左右，我达到了自己预期的结果，升级成功。

于是开始准备跨版本升级。当前可升级的最高版本是macOS Sonoma，但我查询到的资料说会有wifi问题，于是我查了升级到指定版本的办法，最后从App Store里下载了macOS Ventura。

如之前小版本升级一样，我达到了自己预期的结果，升级成功。

### 三、总结

在操作过程里，我一直没有提到什么备份，是因为我比较懒，而且觉得如果不成功，抹掉了也不会有什么可惜。但是没有想到，升级过程如此顺利，简直就是无痛升级。神奇的 OpenCore 引导，让NUC8这台黑苹果可以直接从 Catalina 升级到最新的 Ventura，我非常满意。


### 版权声明

>本文参考[谢週五-NUC8 x 黑苹果抄作业续集，快速迁移 OpenCore + 升级 Big Sur](https://www.zhihu.com/tardis/zm/art/296365439?source_id=1003)。
>
> 感谢原作者，以及开源世界。