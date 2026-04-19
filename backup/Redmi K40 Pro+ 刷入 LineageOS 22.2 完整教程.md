<h1 id="redmi-k40-pro+-刷入-lineageos-22.2-完整教程" data-pm-slice="0 0 []" style="font-size:1.8em;font-weight:700;margin:1em 0 .5em;padding-bottom:.3em;border-bottom:1px solid #eee;">Redmi K40 Pro+ 刷入 LineageOS 22.2 完整教程</h1><blockquote style="border-left:4px solid #ddd;padding-left:16px;margin:1em 0;color:#666;"><p style="margin:.5em 0;line-height:1.75;"><strong style="font-weight:600;">设备信息</strong></p><ul data-spread="false" style="padding-left:24px;margin:.5em 0;"><li data-label="•" data-list-type="bullet" data-spread="false" style="margin:.25em 0;"><p style="margin:.5em 0;line-height:1.75;">设备名称：Xiaomi Redmi K40 Pro+</p></li><li data-label="•" data-list-type="bullet" data-spread="false" style="margin:.25em 0;"><p style="margin:.5em 0;line-height:1.75;">代号：haydn</p></li><li data-label="•" data-list-type="bullet" data-spread="false" style="margin:.25em 0;"><p style="margin:.5em 0;line-height:1.75;">型号：M2012K11C（必须精确匹配）</p></li><li data-label="•" data-list-type="bullet" data-spread="false" style="margin:.25em 0;"><p style="margin:.5em 0;line-height:1.75;">SoC：Qualcomm Snapdragon 888</p></li><li data-label="•" data-list-type="bullet" data-spread="false" style="margin:.25em 0;"><p style="margin:.5em 0;line-height:1.75;">本教程基于 LineageOS 22.2，适用于 variant3（国行/海外版 M2012K11C）</p></li><li data-label="•" data-list-type="bullet" data-spread="false" style="margin:.25em 0;"><p style="margin:.5em 0;line-height:1.75;">刷机环境：Linux Mint 22.3 x86_64</p></li></ul></blockquote><hr style="border:none;border-top:2px solid #ddd;margin:2em 0;"><h2 id="一、前置准备" style="font-size:1.4em;font-weight:600;margin:1em 0 .5em;padding-bottom:.25em;border-bottom:1px solid #eee;">一、前置准备</h2><h3 id="1.-电脑端工具" style="font-size:1.2em;font-weight:600;margin:.8em 0 .4em;">1. 电脑端工具</h3><pre data-language="bash" style="background:#f6f8fa;padding:16px;border-radius:6px;overflow-x:auto;margin:1em 0;"><code style="background:none;padding:0;font-size:.875em;line-height:1.6;font-family:Menlo,Monaco,monospace;"># 安装 adb 和 fastboot（Debian/Ubuntu/Mint）
sudo apt install adb fastboot

# 验证安装
adb version
fastboot --version</code></pre><h3 id="2.-配置-usb-udev-规则（linux）" style="font-size:1.2em;font-weight:600;margin:.8em 0 .4em;">2. 配置 USB udev 规则（Linux）</h3><p style="margin:.5em 0;line-height:1.75;">小米设备在 Fastboot/Recovery 模式下需要 udev 规则才能被普通用户识别：</p><pre data-language="bash" style="background:#f6f8fa;padding:16px;border-radius:6px;overflow-x:auto;margin:1em 0;"><code style="background:none;padding:0;font-size:.875em;line-height:1.6;font-family:Menlo,Monaco,monospace;"># 添加小米 vendor ID 规则
echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", GROUP="plugdev"' | sudo tee /etc/udev/rules.d/51-android.rules

# 重载规则
sudo udevadm control --reload-rules
sudo udevadm trigger

# 将当前用户加入 plugdev 组（重新登录后生效）
sudo usermod -aG plugdev $USER</code></pre><blockquote style="border-left:4px solid #ddd;padding-left:16px;margin:1em 0;color:#666;"><p style="margin:.5em 0;line-height:1.75;"><strong style="font-weight:600;">Windows 用户</strong>：需要安装 <a href="https://en.miui.com/unlock" style="color:#0969da;text-decoration:none;">Mi Unlock 工具</a> 自带的驱动（driver_install_64.exe）。</p></blockquote><h3 id="3.-下载文件" style="font-size:1.2em;font-weight:600;margin:.8em 0 .4em;">3. 下载文件</h3><p style="margin:.5em 0;line-height:1.75;">从 <a href="https://wiki.lineageos.org/devices/haydn/variant3/" style="color:#0969da;text-decoration:none;">LineageOS Wiki - haydn</a> 页面获取以下文件：</p>
文件 | 说明 | 大小参考
-- | -- | --
dtbo.img | 设备树覆盖 | ~24MB
vendor_boot.img | Vendor 引导镜像 | ~96MB
boot.img | Lineage Recovery 镜像 | ~192MB
lineage-22.2-*-nightly-haydn-signed.zip | LineageOS 系统包 | ~1.5GB

<hr style="border:none;border-top:2px solid #ddd;margin:2em 0;"><blockquote style="border-left:4px solid #ddd;padding-left:16px;margin:1em 0;color:#666;"><p style="margin:.5em 0;line-height:1.75;"><strong style="font-weight:600;">参考来源</strong>：<a href="https://wiki.lineageos.org/devices/haydn/variant3/" style="color:#0969da;text-decoration:none;">LineageOS Wiki - Xiaomi Redmi K40 Pro+ (haydn)</a></p><p style="margin:.5em 0;line-height:1.75;"><strong style="font-weight:600;">最后更新</strong>：2026-04-19（基于实际刷机操作整理）</p></blockquote># Redmi K40 Pro+ 刷入 LineageOS 22.2 完整教程

> **设备信息**
>
> * 设备名称：Xiaomi Redmi K40 Pro+
>
> * 代号：haydn
>
> * 型号：M2012K11C（必须精确匹配）
>
> * SoC：Qualcomm Snapdragon 888
>
> * 本教程基于 LineageOS 22.2，适用于 variant3（国行/海外版 M2012K11C）
>
> * 刷机环境：Linux Mint 22.3 x86\_64

***

## 一、前置准备

### 1. 电脑端工具

```bash
# 安装 adb 和 fastboot（Debian/Ubuntu/Mint）
sudo apt install adb fastboot

# 验证安装
adb version
fastboot --version
```

### 2. 配置 USB udev 规则（Linux）

小米设备在 Fastboot/Recovery 模式下需要 udev 规则才能被普通用户识别：

```bash
# 添加小米 vendor ID 规则
echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0666", GROUP="plugdev"' | sudo tee /etc/udev/rules.d/51-android.rules

# 重载规则
sudo udevadm control --reload-rules
sudo udevadm trigger

# 将当前用户加入 plugdev 组（重新登录后生效）
sudo usermod -aG plugdev $USER
```

> **Windows 用户**：需要安装 [[Mi Unlock 工具](https://en.miui.com/unlock)](https://en.miui.com/unlock) 自带的驱动（driver\_install\_64.exe）。

### 3. 下载文件

从 [[LineageOS Wiki - haydn](https://wiki.lineageos.org/devices/haydn/variant3/)](https://wiki.lineageos.org/devices/haydn/variant3/) 页面获取以下文件：

| 文件                                        | 说明                  | 大小参考    |
| ----------------------------------------- | ------------------- | ------- |
| `dtbo.img`                                | 设备树覆盖               | \~24MB  |
| `vendor_boot.img`                         | Vendor 引导镜像         | \~96MB  |
| `boot.img`                                | Lineage Recovery 镜像 | \~192MB |
| `lineage-22.2-*-nightly-haydn-signed.zip` | LineageOS 系统包       | \~1.5GB |

如需 Google 服务，额外下载：

| 文件                                | 说明        |
| --------------------------------- | --------- |
| `MindTheGapps-15.0.0-arm64-*.zip` | GApps 附加包 |

下载地址：

* LineageOS 系统：<https://download.lineageos.org/devices/haydn>

* dtbo/vendor\_boot/boot(recovery)：Wiki 安装页面提供的链接

* GApps：<https://wiki.lineageos.org/gapps/>

### 4. 手机端准备

* [ ] 在 **设置 → 关于手机** 中确认型号为 **M2012K11C**

* [ ] 使用原厂系统至少开机一次，测试收发短信、通话、WiFi、LTE 功能正常

* [ ] **确保固件版本为 Android 14**，如果不是，先升级/降级到 Android 14 的官方系统

* [ ] 删除所有 Google 账号（避免 FRP 锁）

* [ ] 开启 USB 调试：**设置 → 关于手机 → 连续点击 MIUI 版本** 开启开发者选项 → **设置 → 额外设置 → 开发者选项 → 开启 USB 调试**

***

## 二、解锁 Bootloader

> **解锁会清除所有数据！** 请提前备份手机中的重要文件。\
> **此步骤需要 Windows 7+ 电脑**，使用小米官方 Mi Unlock 工具。

### 国行设备额外要求（2023年11月8日后的 HyperOS）

如果你的设备使用的是 2023年11月8日之后的中国版 HyperOS，需要先通过小米社区 App 申请解锁资格：

1. 在小米社区 App 注册中国区账号并完成实名认证
2. 达到社区 Level 5
3. 通过安卓知识测试（每次测试间隔至少 7 天）
4. 通过后 14 天内完成解锁

### 解锁步骤

1. 在 [[小米官网](https://account.xiaomi.com/)](https://account.xiaomi.com/) 注册 Mi 账号并绑定手机号

   * 一个账号每年最多解锁 4 台设备，同一设备每 30 天只能解锁一次
2. 手机插入 SIM 卡
3. **设置 → 额外设置 → 开发者选项 → Mi Unlock 状态 → 绑定设备和账号**
4. 下载 [[Mi Unlock 工具](https://en.miui.com/unlock)](https://en.miui.com/unlock)（推荐 v7.6.727.43 或更新版本）
5. 运行 `driver_install_64.exe` 安装驱动
6. 手机关机，**按住 音量下 + 电源** 进入 Fastboot 模式，用 USB 连接电脑
7. 运行 Mi Unlock，按提示操作

   * 可能需要等待最多 30 天

   * 建议每天午夜（GMT+8）尝试，小米每日解锁名额有限
8. 解锁成功后手机会恢复出厂设置，**重新开启 USB 调试**

***

## 三、刷入额外分区

手机进入 Fastboot 模式（关机后按住 **音量下 + 电源** 直到出现 "FASTBOOT"），用 USB 连接电脑。

验证连接：

```bash
fastboot devices
# 应显示设备序列号
```

刷入 dtbo 和 vendor\_boot：

```bash
cd ~/下载    # 或文件所在目录

fastboot flash dtbo dtbo.img
fastboot flash vendor_boot vendor_boot.img
```

重启回 Bootloader：

```bash
fastboot reboot bootloader
```

等待设备重新出现在 `fastboot devices` 中后继续下一步。

***

## 四、刷入 Lineage Recovery

```bash
# 同时刷入 boot_a 和 boot_b，避免因启动槽位不同导致 recovery 无法启动
fastboot flash boot_a boot.img
fastboot flash boot_b boot.img
```

重启进入 Recovery：

```bash
fastboot reboot recovery
```

> **验证**：手机应显示 LineageOS logo。如果没有，说明 recovery 刷入失败，需要重新从第三步开始。

***

## 五、安装 LineageOS

### 5.1 格式化数据

在 Recovery 中操作：

1. 选择 **Factory Reset → Format data / factory reset**
2. 确认执行（会清除所有数据并移除加密）
3. 完成后返回主菜单

### 5.2 Sideload 系统包

在 Recovery 中选择 **Apply update → Apply from ADB**，然后电脑端执行：

```bash
adb -d sideload /path/to/lineage-22.2-*-nightly-haydn-signed.zip
```

> 刷完后可能停在 47% 并显示 `adb: failed to read command: Success`，这是正常现象，表示刷入已成功。

***

## 六、安装 GApps（可选）

> GApps 必须在首次启动 LineageOS 之前安装！

如果需要 Google 服务（Play Store、Gmail 等）：

1. Recovery 中选择 **Apply update → Apply from ADB**
2. 电脑端执行：

```bash
adb -d sideload /path/to/MindTheGapps-15.0.0-arm64-*.zip
```

1. 手机弹出 **"Signature verification failed"** 时，选择 **Yes**（GApps 没有 LineageOS 签名是正常的）

***

## 七、首次启动

在 Recovery 主菜单中：

1. 点击左上角 **返回箭头**
2. 选择 **Reboot system now**

首次启动通常需要 **3-15 分钟**，耐心等待。如果超过 15 分钟仍未进入系统，可能遗漏了某个步骤。

***

## 八、刷机后注意事项

* **设备完整性**：LineageOS 无法通过 Play Integrity 等设备完整性检测，部分银行 App、游戏等可能无法正常运行

* **每次开机警告**：解锁 Bootloader 后每次开机会有警告画面，这是正常的，无法消除

* **不要随意删除系统应用**：LineageOS 已只包含最少的应用集，删除可能导致系统异常

* **Root**：LineageOS 官方不提供 Root 支持，自行 Root 后不在官方支持范围内

* **系统更新**：可以通过 OTA 更新到同版本的更新构建，也可以通过 recovery sideload 方式升级

***

## 九、常用命令速查

```bash
# 进入 Fastboot 模式（手机关机状态）
# 按住 音量下 + 电源

# 进入 Recovery 模式（手机关机状态）
# 按住 音量上 + 电源

# 验证设备连接
fastboot devices
adb devices

# 刷入分区
fastboot flash <分区名> <镜像文件>

# Sideload 刷机包
adb -d sideload /path/to/package.zip

# 重启
fastboot reboot              # 重启到系统
fastboot reboot bootloader   # 重启到 Fastboot
fastboot reboot recovery     # 重启到 Recovery
```

***

## 十、问题排查

| 问题                                     | 解决方法                                                              |
| -------------------------------------- | ----------------------------------------------------------------- |
| `fastboot devices` 无设备                 | 检查 USB 连接、udev 规则、驱动是否安装                                          |
| `adb devices` 显示 unauthorized          | 查看手机屏幕是否有授权弹窗，确认允许                                                |
| 刷入 recovery 后仍进入 Fastboot              | 尝试同时刷入 boot\_a 和 boot\_b                                          |
| `adb: failed to read command: Success` | 正常现象，sideload 已完成                                                 |
| Signature verification failed          | GApps 附加包的正常提示，选 Yes 继续                                           |
| 首次启动超过 15 分钟                           | 可能遗漏步骤，重新检查或到 [[LineageOS 社区](https://reddit.com/r/LineageOS)](https://reddit.com/r/LineageOS) 寻求帮助 |

***

> **参考来源**：[[LineageOS Wiki - Xiaomi Redmi K40 Pro+ (haydn)](https://wiki.lineageos.org/devices/haydn/variant3/)](https://wiki.lineageos.org/devices/haydn/variant3/)
>
> **最后更新**：2026-04-19（基于实际刷机操作整理）