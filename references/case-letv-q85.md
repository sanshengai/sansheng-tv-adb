# 实战复盘 · 乐视 85 吋巨幕电视（Android 12）硬核去开机广告与精简记

> **来源**：贡献者实机实测（[PR #1](https://github.com/sanshengai/sansheng-tv-adb/pull/1)，iskoldt-X，2026-10-20）。维护者未在本机复现。
>
> **案例**：一台 2024 年底固件、自称 Android 12、菜单极简的 85 吋乐视电视，历经「无法开开发者模式 → 抓出工厂菜单 → 发现 11MB 底层硬编码广告视频 → 逆向底层开关彻底消灭」的全过程。

---

## 一、 初始困境：看似矛盾的设备表象

用户手头有一台 85 吋乐视大屏电视，遇到的表象极其诡异：
1. **界面极度简陋**：不是当年乐视花哨的 EUI，系统设置里只有寥寥 4 项（系统信息、系统升级、恢复出厂设置、服务协议）。
2. **常规安卓入口全封死**：点进「系统信息」，页面只有一张静态信息卡片，光标只能停在「设备名称 AndroidTV」上，没有可以连按 7 次的「内部版本号（Build Number）」，无法通过常规方法开启开发者选项。
3. **老乐视暗码初试无效**：在 HDMI1 信号源全屏下按遥控器 `2580`，电视没有任何反应。

---

## 二、 关键转折 1：信号源浮动菜单 vs 全屏信号源

行业很多通用电视板卡（如 CVTE、MStar 等）在接收红外工厂暗码时，存在一个极细微的操作分水岭：
- **错误操作**：先切进 HDMI1 全屏画面，再按数字键（此时按键通常被 HDMI 通道消费，底层电视 UI 根本接不到输入）。
- **正确操作**：按下【信号源】键，**停留在屏幕浮现的信号源选择菜单浮窗状态下**，直接输入数字键！

实操验证：
1. 在信号源浮动菜单下输入 **`1147`** ➔ 屏幕立刻弹出了「酒店设置」，证明底层按键拦截通道完全通畅！
2. 随后在同一个信号源浮动菜单下输入 **`2580`** ➔ 屏幕左侧顺利弹出了蓝底的 **「工厂菜单（Factory Menu）」**！

---

## 三、 硬件底细：揭开真实身份

通过工程菜单顶部的一组关键元数据，这台电视的硬件底细彻底明朗：
- **板型**：`CV560-C75`
- **客户产品**：`Q85_CV560-C75_930MA_Letv_BRT`（确认是乐视正品 85 吋巨幕 Q85，定制高亮 930mA 背光板）
- **软件版本**：`2024-11-12 16:40:48`（非常新的固件）
- **Android 版本**：`Android_Version: 12/S`（货真价实的 Android 12）
- **芯片方案**：集成海思（HiSilicon）显示核心与 Cultraview 方案

在工厂菜单的 **【其他选项】** 中，顺利将 **【ADB 调试】** 设为 **ON**。电视断电重启后，电脑端 ADB 5555 端口成功连通！

---

## 四、 关键转折 2：为什么停用 APK 后，开机广告依然播放？

连上电脑后，工具自动执行了备份与回滚预演：
- 确认 Android 12 下 `pm disable-user --user 0` 完美可用（而 `pm hide` 触发 MANAGE_USERS 限制拒绝）。
- 第 1 批成功停用了 `com.stv.bootadmanager`（开机广告管理器）等 8 个冗余组件。

然而执行真重启（`adb reboot`）后，用户反馈：**开机广告依然播放了！**

### 逆向深挖：广告不在 APK 里，而在 Linux 原生服务层

通过 ADB shell 进入底层 Linux 文件系统进行关键词与大文件排查：
1. 在 `/system/product/media/` 下赫然发现了一个高达 **11 MB 的独立视频文件**：`bootvideo.mp4`！
2. 在 `/system/etc/init/bootvideo.rc` 与 `/system/bin/bootvideo` 中，发现了厂商专门编写的原生硬件启动服务：电视开机时由 init 进程直接拉起 `bootvideo`，强行硬解码播放这段 11MB 的 MP4 视频！
3. 我们将该文件通过 `adb pull` 提取到本地电脑，经用户双击播放确认：**正是每次开机折磨人的那段开机广告！**

---

## 五、 社区经验 · 高风险：修改系统持久属性（本工具不执行）

> **警告**：以下做法是贡献者的个人经验，改的是系统持久属性（`persist.*`），不是 `pm disable-user` 停用应用，超出本工具「只停用、每步可还原」的原则。本工具不会执行这些命令，入口库数据也不收录。属性含义来自贡献者对二进制的分析，维护者未复现；不同固件可能不同，误改可能导致开机异常，风险自负。

贡献者使用的命令：

```bash
adb shell setprop persist.sys.bootvideo.enable false
adb shell setprop persist.sys.bootanimation.type 0
adb shell setprop persist.letv.advertisement false
```

**动手前先记录原值**（还原时以记录为准）：

```bash
adb shell getprop persist.sys.bootvideo.enable
adb shell getprop persist.sys.bootanimation.type
adb shell getprop persist.letv.advertisement
```

**还原方法**（按贡献者机器的原值；如记录的原值不同，以记录为准）：

```bash
adb shell setprop persist.sys.bootvideo.enable true
adb shell setprop persist.sys.bootanimation.type 1
adb shell setprop persist.letv.advertisement true
adb reboot
```

贡献者反馈：执行后重启，开机视频广告不再播放，直接进入系统桌面。

---

## 六、 附带战果：桌面抢占问题与内存释放

1. **解决开机桌面抢占**：
   排查发现 `com.letv.lekids`（乐视儿童）在清单里注册了 `android.intent.category.HOME`，开机时会自动霸占前台拉起儿童模式。在第 2 批将其停用后，系统桌面自然纯净归位到官方桌面 `com.stv.deskplatform`。
2. **可用内存提升**：
   安全停用 12 个广告、演示及后台守护组件后，电视可用运存从 **2157 MB** 稳定提升到 **2263 MB ~ 2351 MB**，多释放了超过 100–200 MB 的宝贵运存。

---

## 七、 经验与沉淀

1. **暗码触发的物理层细节**：电视的工厂暗码（如 2580 / 1147）对界面上下文极其敏感，遇阻时务必分清“浮动菜单下输入”与“信号源全屏下输入”。
2. **开机广告可能不在 APK 里**：部分机型的开机视频由系统原生服务播放，仅停用 APK 无法去除；是否改系统属性需用户自行判断风险，本工具只做可还原的停用。
3. **沉淀入库**：本次实测的工厂菜单入口与停用清单已收录到本项目的 `data/brands/letv.json`，让后续同类机型用户免走弯路。
