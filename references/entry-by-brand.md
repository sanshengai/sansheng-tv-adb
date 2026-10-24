# 各品牌开发者模式 / ADB 入口总表（由 data/brands 生成，勿手改）

证据等级：本机实测 > 官方 > 多源 > 单源。「不适用」品牌只给替代动作。

## Google TV 播放设备（Chromecast with Google TV / Google TV Streamer / onn 4K） · 适用

这几款都是原生 Android TV / Google TV，开发者选项和 ADB 全开，`pm disable-user --user 0` 可用可回滚。🔴 唯一的分水岭是 Android 版本：Android 12 及以前打开 USB 调试就能 `adb connect IP:5555` 直连；升到 Android 13/14 之后必须走「无线调试 + 配对码」，端口随机、重启失效。另外不改任何包也能先做一步：Google 官方的「仅应用模式（Apps only mode）」能关掉个性化推荐。

**怎么认机型**

- system_name_shown_at：设置 → 系统（System）→ 关于（About）→ 「Android TV OS 版本 / Android TV OS Build」。Chromecast with Google TV 的设置入口在首页右上角的头像里（按遥控中键打开通知/设置面板再选设置）。
- about_page_fields：设备名称（Chromecast / Google TV Streamer / onn 4K 等）；Android 版本（10 / 11 / 12 / 14 — 决定用哪套 ADB 流程）；Android TV OS 版本 / Build（连点这一行 7 次开开发者模式）；Android 安全补丁级别；Google TV 版本 / 内部版本号
- model_naming_rule：Google 自家产品按代际命名：Chromecast with Google TV (4K) 2020、Chromecast with Google TV (HD) 2022、Google TV Streamer 4K 2024。Walmart 的 onn 用「onn. 4K / 4K Pro / 4K Plus + 年份」，不带字母编码。`adb shell getprop ro.product.model` 和 `ro.build.version.release` 能一次读出机型和 Android 版本。
- sticker_location：Chromecast 的型号印在机身背面（椭圆形吊坠状本体）；onn 盒子在底部贴纸；Google TV Streamer 在底面。但最可靠的还是关于页里的 Android 版本 — 型号定不了流程，Android 版本才定。
- year_ranges：2020（Chromecast with Google TV 4K）至今。2025 年这批设备普遍被推到 Android 14，原来能用的 5555 直连大面积失效。

### Chromecast with Google TV（4K，2020）/（HD，2022）— Android 10 / 11 / 12 阶段（2020–2024（升到 Android 14 之前），android 出厂 Android 10（4K 款）/ Android 12（HD 款），期间升到 Android 12）

- 认：椭圆形吊坠状本体 + 一支小巧的语音遥控（顶部有麦克风，底部有 YouTube 和 Netflix 专属键），遥控器没有数字键。关于页里 Android 版本显示 10、11 或 12 — 这一段就走 5555 直连。首页是 Google TV 界面：顶部一大块推荐横幅，下面「为你推荐 / 应用 / 影视库」几行。
- 值不值：🔴 收益很大。CCwGTV 4K 只有 2 GB 内存 + 8 GB 存储（HD 款更低），装三四个流媒体 App 就开始提示存储不足、切换变慢，是这一组里最值得动手的机器。三个信号：内存小（成立）、广告多（首页整块推荐横幅，成立）、机龄 4–6 年（成立）。

**Android 12 及以下的 Chromecast with Google TV** · 证据：多源
1. 首页右上角选头像 / 按遥控中键打开面板 → 设置（Settings）
2. 设置 → 系统（System）→ 关于（About）
3. 滚到「Android TV OS 版本 / Android TV OS Build」，用遥控中键连点 7 次 → 「您现在是开发者」
4. 返回：设置 → 系统 → 开发者选项（Developer options）
5. 在 Debugging 一段里打开「USB 调试（USB debugging）」，弹确认框选「确定 / 允许」
6. 在 设置 → 网络和互联网 里查本机 IP
7. 电脑执行 `adb connect <IP>:5555`
8. 电视上弹「允许 USB 调试吗」，选「允许」并勾「一律允许来自此计算机」
- 授权弹窗：有；端口 5555
- ⚠ 这台设备的「USB 调试」开关同时管着网络那一路 — Android 13 以前打开它，adbd 会在 USB 和 TCP/IP 两边一起监听，所以不插线也能连。升级到 Android 13/14 之后这个行为就没了，要改走下面那条无线调试流程。
- 来源：[A Comprehensive Guide to Enabling and Using ADB on Google TV Streamer and Chromecast with Google TV — 明确说明 Android 13 之前 USB debugging 会同时开启 TCP/IP 监听](https://allaboutchromecast.com/using-adb-on-google-tv-streamer) 
- 来源：[How to enable developer options in Chromecast with Google TV and Google TV Streamer?](https://allaboutchromecast.com/enable-developer-options-in-chromecast-with-google-tv) 
- 来源：[r/Chromecast：How to connect to CCwGTV via ADB using only WiFi](https://www.reddit.com/r/Chromecast/comments/s96moi/how_to_connect_to_ccwgtv_via_adb_using_only_wifi) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（原生 Android TV 行为：`adb shell pm disable-user --user 0 <包名>` 有效，`pm enable --user 0 <包名>` 回滚；`pm uninstall -k --user 0` 也可用，恢复用 `pm install-existing`。XDA 的 Google TV 换 launcher 教程明确给出了成对的 disable / enable 命令。）
- 禁停（4）：`com.google.android.apps.tv.launcherx`、`com.google.android.tungsten.setupwraith`、`com.android.systemui`、`com.google.android.inputmethod.latin`
- 可停（7）：`com.google.android.tvrecommendations`、`com.google.android.leanbacklauncher.recommendations`、`com.google.android.backdrop`、`com.google.android.feedback`、`com.google.android.tv.bugreportsender`、`com.google.android.backuptransport`、`com.google.android.partnersetup`
- 看你用不用（6）：`com.google.android.katniss`、`com.google.android.gms`、`com.android.vending`、`com.google.android.videos`、`com.google.android.play.games`、`com.google.android.tts`
- 坑：🔴 换第三方 launcher 时 launcherx 和 setupwraith 必须一起禁：只禁 launcherx 的话，setupwraith 会自动把 Google TV 桌面重新启用。搞砸黑屏了就 `adb shell pm enable --user 0 com.google.android.apps.tv.launcherx` 加 `... com.google.android.tungsten.setupwraith` 一起恢复 — 这条恢复命令一定要在动手前先记下来。
- 坑：第三方 launcher 要用 `adb install` 装，不要从 Play 商店或文件管理器装 — 从 Play 装的 launcher 在 Google TV 上拿不到需要的权限。
- 坑：不动任何包也能先减一半噪音：设置 → 账号与登录（Accounts & Sign In）→ 选自己的 Google 账号 → 「仅应用模式（Apps only mode）」→ 打开。首页会变成一排 App 图标。代价是搜索和 Google 助理用不了，而且 Google 自己说明：赞助内容和热门影视预告仍然会出现，顶部那条横幅也还在。
- 坑：社区有解锁 bootloader / 刷 LineageOS 的讨论，本项目不采用，也不记录方法。

### Chromecast with Google TV / Google TV Streamer / onn — Android 13 及以上（含 2025 年推送的 Android 14）（2024 至今（老 Chromecast 在 2025 年被推到 Android 14 后也归入这一档），android Android 13 / 14（部分设备已到 Android TV OS 15））

- 认：判据只有一条：设置 → 系统 → 关于 里的 Android 版本是 13 或 14（或 Android TV OS 15）。另一个特征是开发者选项里出现了独立的「无线调试（Wireless debugging）」条目，而不再只有「USB 调试」。Google TV Streamer 4K 是一台扁平的浅色小盒子（不再是吊坠形），带以太网口。
- 值不值：看具体机器：Google TV Streamer 4K 是 4 GB 内存 / 32 GB 存储，性能不紧张，做它主要是关推荐行和遥测；而升上 Android 14 的老 Chromecast（2 GB / 8 GB）本来就紧，新系统只会更吃资源，收益依然大。注意这一档的操作成本明显更高 — 每次重启都要重新配对。

**Android 13 / 14 的 Google TV 设备，走 Wi-Fi 连接时（这是绝大多数情况）** · 证据：多源
1. 首页右上角头像 → 设置 → 系统（System）→ 关于（About）
2. 「Android TV OS 版本 / Build」连点 7 次 → 「您现在是开发者」
3. 返回：设置 → 系统 → 开发者选项
4. 找到 Debugging 段里的「无线调试（Wireless debugging）」→ 打开 → 弹窗选「允许」
5. 在无线调试页面里选「使用配对码配对设备（Pair device with pairing code）」，屏幕上会显示一个 6 位配对码和一个 IP:配对端口 — 这一页不要退出
6. 电脑执行 `adb pair <IP>:<配对端口>`，按提示输入 6 位配对码
7. 配对成功后回到无线调试主页面，上面显示的是另一个「IP 地址和端口」（调试端口，和配对端口不同）
8. 电脑执行 `adb connect <IP>:<调试端口>`，再 `adb devices -l` 确认已连上
- 授权弹窗：有；需配对码
- ⚠ 🔴 端口和配对码每次都不一样，而且设备重启后无线调试会自动关闭、端口重新随机 — 每次要用都得重新配一遍。像 Home Assistant 那种需要长期保持 ADB 连接的场景，在 Android 14 上基本没法自动化。做完一次性清理就关掉无线调试。
- 来源：[A Comprehensive Guide to Enabling and Using ADB on Google TV Streamer and Chromecast with Google TV — 明确 Android 13 起无线调试与 USB 调试分离，2025 年两款设备都升到 Android 14](https://allaboutchromecast.com/using-adb-on-google-tv-streamer) 
- 来源：[Home Assistant 社区：Chromecast wGTV 4K 升 Android 14 后 USB 调试失效，adb pair → adb connect 的完整步骤](https://community.home-assistant.io/t/android-debug-bridge-on-chromecast-wgtv-4k-after-android-14-update/864037) 
- 来源：[r/AndroidTV：Chromecast Android 14 ADB — Wireless Debugging → Pair device with pairing code](https://www.reddit.com/r/AndroidTV/comments/1pxv69g/working_fix_chromecast_android_14_adb_port_5555) 
- 来源：[How to Enable and Use Wireless Debugging on Google TV Streamer 4K | ADB Over Wi-Fi Setup Guide（实机演示）](https://www.youtube.com/watch?v=iFzz744quJ4) 

**Android 13 / 14 设备，走 USB 线连接时** · 证据：多源
1. 同上打开开发者选项
2. 开发者选项 → Debugging → 打开「USB 调试（USB debugging）」
3. 弹出确认框选「确定 / 允许」
4. 用 USB 线连接电脑（Chromecast / Streamer 需要支持数据的 USB-C 线或带 USB 口的供电底座）
5. `adb devices` 确认；电视上弹「允许 USB 调试吗」，选「允许」并勾「一律允许」
- 授权弹窗：有
- ⚠ Android 14 上 USB 调试只管 USB 那一路，不再顺带开 TCP/IP 监听 — 也就是说开了 USB 调试之后 `adb connect IP:5555` 依然连不上，这是很多人以为「ADB 被砍了」的真实原因。
- 来源：[All About Chromecast：按连接方式选 USB 调试或无线调试](https://allaboutchromecast.com/using-adb-on-google-tv-streamer) 
- 来源：[A Complete Guide to Android ADB Wireless Debugging — 两种调试方式的分工](https://gadgetguideonline.com/android/android-adb-wireless-debugging) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（连上之后包管理行为和 Android 12 阶段一样：`pm disable-user --user 0` / `pm enable --user 0` 成对可用，`pm uninstall -k --user 0` 也可用。变的只是「怎么连上去」，不是「连上去能干什么」。）
- 禁停（5）：`com.google.android.apps.tv.launcherx`、`com.google.android.tungsten.setupwraith`、`com.android.systemui`、`com.google.android.inputmethod.latin`、`com.mediatek.tv.oneworld.tvcenter`
- 可停（5）：`com.google.android.tvrecommendations`、`com.google.android.leanbacklauncher.recommendations`、`com.google.android.backdrop`、`com.google.android.feedback`、`com.google.android.tv.bugreportsender`
- 看你用不用（7）：`com.google.android.katniss`、`com.google.android.gms`、`com.android.vending`、`com.google.android.videos`、`com.google.android.play.games`、`com.netflix.ninja`、`com.google.android.youtube.tv`
- 坑：🔴 Android 14 上插着网线时「无线调试」开关打不开 — 一打开就自动弹回关闭。要配对只能改走 Wi-Fi，或者先拔网线配对完再插回去。这是 XDA 上明确记录的 2024 款 Chromecast 行为。
- 坑：🔴 无线调试的端口和配对码每次重启都变，且无线调试开关在开机时默认关闭。一次性清理没问题，但想长期保持 ADB 连接（Home Assistant 之类）在 Android 14 上基本做不到自动化。
- 坑：🔴 onn 4K Plus 升到 Android 14 之后有一条额外限制：侧载的 App 要想拿到「无障碍（Accessibility）」权限，必须用 `adb install` 安装 — 从文件管理器或 Play 装的拿不到。Projectivy Launcher、TVQuickActions 这类依赖无障碍权限的工具直接受影响。
- 坑：🔴 不要照抄通用 Android TV 包清单。MediaTek 方案的 Google TV 电视上 com.mediatek.tv.oneworld.tvcenter 看着像 bloat，实际是 HDMI 信号源切换器 — 这类误杀的症状（切不了信号源、某个 App 打不开）很难反查回是清理造成的。动手前先在本机跑 `pm list packages` 现抓一份，别套别人的表。
- 坑：同 Android 12 阶段：换 launcher 前先记下恢复命令 `adb shell pm enable --user 0 com.google.android.apps.tv.launcherx` + `... com.google.android.tungsten.setupwraith`；第三方 launcher 必须用 `adb install` 装。

### onn. 4K / 4K Pro / 4K Plus（Walmart 自有品牌）（2021 至今，android 按批次不同：早期 Android 10 / 11，2023 款 Android 12，2025 款 onn 4K Plus 已到 Android 14）

- 认：Walmart 自有品牌，机身印 onn. 字样（小写加一个点），外形是白色 / 黑色小方盒或电视棒；遥控器上有 Walmart 生态的快捷键。界面和 Chromecast 完全一样（都是 Google TV），所以不看界面看机身 logo。关于页的 Android 版本决定用哪套 ADB 流程 — onn 各批次版本跨度很大，必须实地看。
- 值不值：onn 4K（2021）2 GB 内存、onn 4K Pro（2024）3 GB 内存。这一系列本来就是低价机，内存和存储都不宽裕，做的收益不错；而且 onn 对侧载相对友好（有 YouTube 教程直接说「Google TV 设备对侧载很友好」）。但 2025 年的 onn 4K Plus 升 Android 14 后多了无障碍权限限制，要提前告诉用户。

**onn 全系（先在关于页确认 Android 版本再决定走哪套流程）** · 证据：多源
1. 设置 → 系统（System）→ 关于（About）
2. 滚到最底，「Android TV OS 版本 / Build」连点 7 次 → 「您现在是开发者」
3. 返回：设置 → 系统 → 开发者选项
4. Android 12 及以下：打开「USB 调试」，然后 `adb connect <IP>:5555`
5. Android 13 / 14：打开「无线调试」→「使用配对码配对设备」→ `adb pair <IP>:<配对端口>` 输码 → `adb connect <IP>:<调试端口>`
6. 两种方式首次连接都会在电视上弹「允许 USB 调试吗」，选「允许」并勾「一律允许」
- 授权弹窗：有；端口 5555
- ⚠ onn 各批次的 Android 版本差别很大（同一个「onn 4K」名字下从 Android 10 到 14 都有），port=5555 只对 Android 12 及以下成立 — 一定要先在关于页看 Android 版本，别按型号名猜。
- 来源：[How to Sideload the Onn 4K stick — 演示 设置 → 系统 → 关于 → 点 TV OS build 开开发者选项](https://www.youtube.com/watch?v=3hpAJuBXO3o) 
- 来源：[Full walkthrough: Walmart onn UHD streaming box — Settings → Device Preferences → About → Build 点 7 次 → Developer Options → USB Debugging](https://www.youtube.com/watch?v=vLR-b2pZ8Sw) 
- 来源：[[Guides] 2023 Walmart ONN Google TV (4K YOC) — XDA](https://forum.xda-developers.com/t/guides-2023-walmart-onn-google-tv.4610749) 
- 来源：[AFTVnews：Onn 4K Plus（Android 14）仍可解锁开发者选项并侧载](https://www.aftvnews.com/onn-4k-plus-runs-android-tv-14-and-its-accompanying-issues-no-signs-of-ai-upscaling-yet) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（XDA 的 2023 Walmart ONN Google TV 指南直接给出了 `pm disable-user --user 0` 的批量命令和对应的 `pm enable --user 0` 恢复命令，覆盖 launcher、Play 游戏、Play 影视、Netflix、YouTube 等。）
- 禁停（4）：`com.google.android.apps.tv.launcherx`、`com.google.android.tungsten.setupwraith`、`com.android.systemui`、`com.google.android.inputmethod.latin`
- 可停（4）：`com.google.android.tvrecommendations`、`com.google.android.leanbacklauncher.recommendations`、`com.google.android.backdrop`、`com.google.android.feedback`
- 看你用不用（6）：`com.google.android.play.games`、`com.google.android.videos`、`com.netflix.ninja`、`com.google.android.youtube.tv`、`com.google.android.katniss`、`com.google.android.gms`
- 坑：🔴 同一个「onn 4K」名字下，2021 / 2023 / 2024 / 2025 各批次的 Android 版本从 10 跨到 14，ADB 连接方式完全不同。必须先在关于页看 Android 版本，不能按型号名或购买年份猜。
- 坑：🔴 Android 14 版 onn 上，侧载 App 想拿到无障碍权限必须用 `adb install` 装。要给用户装 Projectivy Launcher 或 TVQuickActions 的，别让他们从 Play / 文件管理器装。
- 坑：有用户反馈 onn 4K 升级到 Android 14 之后出现无法保持联网的问题。这与 ADB 清理无关，但接手一台「升级后就不正常」的 onn 时要先排除系统本身的问题，别把锅算到清理上。
- 坑：社区有解锁 bootloader / 刷 LineageOS 的教程（早期批次可解，后来的强制更新会封掉解锁能力），本项目不采用，也不记录方法。

**待验证 · 欢迎投稿**

- Chromecast with Google TV 4K / HD 各自的内存和存储确切规格。本轮沿用的是社区普遍说法（4K 款 2 GB / 8 GB），没有取到 Google 官方规格页；Google TV Streamer 4 GB / 32 GB 同样未取官方页。
- onn 4K（2021）2 GB、onn 4K Pro（2024）3 GB 的内存数字同样来自社区说法，未取到 Walmart 官方规格页。
- Google TV 设备上 `pm hide` 是否可用？本轮所有来源用的都是 disable-user / uninstall -k --user 0。
- Google TV 首页顶部那条推广横幅（Google 官方社区说它是 what's new 而不是个性化推荐）对应的是哪个包？关掉 tvrecommendations 之后横幅是否还在？本轮没有定位到。
- Android TV OS 15 在这些设备上的行为（无线调试流程是否又有变化）本轮没有取到实机证据，只在一条视频标题里出现过 Android TV OS 15 字样。
- onn 4K Pro（2024）出厂 Android 版本是 12 还是 14？本轮没有取到明确来源，models_examples 里未标注版本。
- Google TV Streamer 是否也有和 onn 4K Plus 一样的「侧载 App 拿不到无障碍权限」限制？AFTVnews 那条只针对 onn，作者自己也在猜是不是所有 onn 设备通用。

## 康佳 / KKTV · 适用

能做：康佳的入口是「切到模拟电视信源 → 按菜单键 → 输入 2008 → 工厂菜单里打开 ADB」，开了之后 pm uninstall --user 0 很好使，社区有成熟的包名清单。最大的坎不是系统而是遥控器——近几年的蓝牙遥控器没有数字键，输不了 2008，得配一个蓝牙键盘。值不值得：2019–2023 年的易柚机型收益最大；2025 年起的易AI 机型没有任何实测记录。

**怎么认机型**

- remote_has_digits：这是康佳最关键的一条：2008 这个口令必须用数字键输入。老款红外遥控器带完整数字键盘；近几年的蓝牙语音遥控器（KKTV 及 R5 等系列）没有数字键，只能外接一个蓝牙键盘敲 2008。先翻遥控器再动手。
- system_name_shown_at：设置 →「关于本机」/「系统信息」；页面上写「易柚 YIUI x.x」或「me柚 OS」，近两年新机写「易AI」
- about_page_fields：产品型号（如 B50U、75R5、LED55M2）；系统版本（YIUI 易柚 x.x / me柚 OS / 易AI x.x）；软件版本号；MAC 地址
- model_naming_rule：没有找到康佳官方的型号编码规则文档。从实际机型看常见形态是：老机型 LED + 尺寸 + 系列字母数字（LED55M2、LED42K1），近年机型 尺寸 + 系列字母 + 数字（75R5、B50U、D6S、S50U、X8、70U5D），2025 年新品走单字母+两位数（K10、G10、R10）；KKTV 子品牌另用 AK50 这类编号。所以不要靠型号猜系统代际，以「关于本机」里写的系统名为准。
- sticker_location：机身背面铭牌贴纸印型号与序列号
- year_ranges：易柚 YIUI 2.0 ≈ 2014 起，YIUI 7 = 2019-01，新版易柚 = 2019-11，YIUI 8.5 = 2021-02，me柚 OS 见于 R5 系列，易AI = 2025-07 起

### 易柚 YIUI 早期（2.0 – 5.x）（2014–2018，android Android 4.x–5.x（社区反馈中还见过标着「YIUI 易柚 3.5」的在用机型））

- 认：「关于本机」写「YIUI 易柚 2.x / 3.x / 5.0」。桌面是 Metro 风格的方块磁贴（易柚早期主打多套主题皮肤，方正色块是标志）；遥控器是带完整数字键盘的红外遥控。
- 值不值：值得做但要先评估：这代机器 1GB 运存居多、机龄 8–12 年，清完确实快，但很多已经卡到连 adb 连接都不稳；如果电视本身已经开机要两分钟，清理救不回来，该换机就换机。

**易柚早期机型通用：菜单键 + 2008 进工厂菜单** · 证据：多源
1. 遥控器把信号源切到「模拟电视 / TV」
2. 按一下菜单键（有的遥控器上是三道横杠的那个键）
3. 画面弹出普通功能设置菜单后，用数字键输入 2008
4. 稍等一下，屏幕出现 Factory Menu（工厂菜单）
5. 在工厂菜单里找 ADB 开关并打开（各机型位置不同，见下面两条）
- 授权弹窗：无；端口 5555
- ⚠ 只改 ADB 那一项。工厂菜单里是屏幕色差、老化模式等硬件参数，原帖写明「仅适用于有技术功底的玩家」，误改有花屏/变砖风险。必须先切到模拟电视信源，在别的信源下按菜单键弹的不是同一个菜单。
- 来源：[康佳智能电视打开ADB调试，开启工程模式工厂模式方法介绍](https://www.znds.com/tv-646692-1-1.html) 2017-02-22
- 来源：[康佳电视机怎么开启ADB模式？](https://jingyan.baidu.com/article/92255446f1d74fc41748f43d.html) 2020-02-07
- 来源：[汇总13款安卓电视机开启Adb（康佳段落）](https://zhuanlan.zhihu.com/p/1901349166480880333) 

**工厂菜单里 ADB 开关在「高级设置」、要密码 123456 的一类机型** · 证据：单源
1. 按上面的方法进入工厂菜单
2. 选择「高级设置」
3. 输入密码「123456」
4. 找到 ADB 开关并打开
- 授权弹窗：有；端口 5555
- ⚠ 只改 ADB 这一项。⚠️ 原帖点名的型号（42K1 / 42K1T / 42K1Y / 42K1TY / 55K1 / 55K1T）归属存疑，看本系列 pitfalls 的第一条——把这些型号当成「康佳机型」去找是找不到的，但「高级设置 + 密码 123456」这个菜单形态本身在康佳机上被多篇二手教程沿用。
- 来源：[康佳智能电视打开ADB调试，开启工程模式工厂模式方法介绍](https://www.znds.com/tv-646692-1-1.html) 2017-02-22

**工厂菜单里没有 ADB 开关、只有「Adbd Enable」参数项的一类机型** · 证据：单源
1. 按上面的方法进入工厂菜单
2. 选择「其它设置」
3. 找到「Adbd Enable」这一项
4. 把参数值改成 1
- 授权弹窗：无；端口 5555
- ⚠ 只改「Adbd Enable」这一项。同样注意型号归属存疑，见 pitfalls。
- 来源：[康佳智能电视打开ADB调试，开启工程模式工厂模式方法介绍（40K1Y/49K1Y/49K1C 段落）](https://www.znds.com/tv-646692-1-1.html) 2017-02-22

**另一条工厂菜单口令：菜单键后连点 5 次「回看」键（不需要数字键）** · 证据：单源
1. 按一下遥控器的 MENU / 菜单键，画面弹出普通功能设置菜单
2. 在这个菜单上连续点按 5 次「回看」键
3. 打开康佳工厂模式菜单
- 授权弹窗：无；端口 5555
- ⚠ 只改 ADB 那一项。这条的价值在于不需要数字键，遥控器没有数字键盘时可以先试它；但只有一个来源、也没说适用型号范围，按不出来就换别的路子，别反复乱按。
- 来源：[各品牌智能电视进入工程模式方法汇总（恩山无线论坛 111 楼）](https://www.right.com.cn/forum/thread-8275404-1-1.html) 2023-10-03
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（这一代没有找到落到具体机型的 pm 命令实测记录，不照搬后面几代的结论。）
- 坑：🔴 网上到处流传的「康佳机型清单：42K1 / 42K1T / 42K1Y / 42K1TY / 55K1 / 55K1T 用密码 123456，40K1Y / 49K1Y / 49K1C 改 Adbd Enable，40U1 / 58U1 没有 ADB 选项」很可能是串品牌的。证据：同一位作者（ZNDS 用户「费老湿」）在 2017-02-22 同一天分别发了创维帖和康佳帖，两帖的这段型号清单一字不差；而 42K1 / 42K1Y / 55K1 / 40U1 在电商和刷机包页面上都是酷开（创维子品牌）的产品。后来知乎、百度经验、微信公众号的汇总文都从这两篇里抄，所以两边都能搜到同一串型号。结论：这串型号按酷开机型处理更合理；菜单形态（高级设置+123456 / 其它设置+Adbd Enable）在康佳机上仍可参考，但不要拿型号去对号入座。

### 易柚 YIUI 7 / 8.x（含 8.5，KKTV 同步升级）（2019-01 发布 YIUI 7；2019-11 新版易柚上机（APHAEA-A5、R3、丘比特系列）；2021-02 升级到 YIUI 8.5；该代持续到约 2023，android 未查到官方公布的 AOSP 版本区间）

- 认：「关于本机」写「YIUI 易柚 7.x / 8.x / 8.5」。YIUI 7 起桌面改成 feed 流（一屏一屏往下滚的推荐流），YIUI 8.5 把原来的「首页」改名叫「精选」，并多了「热点资讯」「会员特权」版块——看到「精选 + 热点资讯 + 会员特权」这三个 Tab 基本就是 8.5。这一代开始主推 AI 远场语音，遥控器上有麦克风键。
- 值不值：值得做：2–3GB 运存，机龄 2–7 年，开关机广告和 feed 流推荐最密；社区的康佳包名清单基本都是在这代机器上跑出来的，成功率最高。

**B50U 等 2020 年后机型：工厂菜单里的「远程调试」二级菜单** · 证据：多源
1. 把信号源切到模拟电视
2. 按菜单键，用数字键输入 2008，进入底层菜单
3. 找到「远程调试」这一项（B50U 上它在倒数第 2 项）
4. 进入这个二级菜单
5. 把「ADB 调试」和「ADBD 调试」两个开关都打开（B50U 上分别在第 1 项和第 3 项）
- 授权弹窗：无；端口 5555
- ⚠ 只开「远程调试」里那两个开关。菜单文案从老机型的「ADB」改成了「远程调试」，位置也从一级菜单挪到二级，找的时候按名字找不要按位置数——原帖给的「倒数第 2 项」只对 B50U 成立。
- 来源：[如何使用ADB工具卸载电视预装app、去广告、修改默认桌面（以康佳B50U为例）](https://www.znds.com/tv-1250596-1-1.html) 2024-07-11
- 来源：[如何通过ADB修改康佳电视桌面，解决安装应用问题](https://blog.csdn.net/weixin_41961749/article/details/145452480) 2025-02-07

**工厂菜单里叫「软件调试」的一类机型** · 证据：单源
1. 按菜单键 + 2008 进入工厂模式
2. 选择「软件调试」
3. 依次打开「adb」和「允许以 root 的方式调试」两项
- 授权弹窗：无；端口 5555
- ⚠ 只开这两项。注意第二项叫「允许以 root 的方式调试」，那是厂商工厂菜单自带的调试开关，不是给系统刷 root——本项目只用它把 adb 连上，不做任何 root/解锁相关操作，用完建议关掉。
- 来源：[康佳电视更换系统桌面记录](https://wangduola.com/archives/227.html) 2024-05-26

**工厂菜单里 ADB 在「其他选项」，且之后还要在系统设置里再开一次开发者选项的一类机型** · 证据：单源
1. 切到模拟电视信源
2. 按菜单键 + 2008，选中「其他选项」
3. 打开 ADB 的开关
4. 退出后进入「设置」
5. 选择「通用设置」→「开发者选项」
6. 把开关打开
- 授权弹窗：无；端口 5555
- ⚠ 只改这两处开关。这一类机型两步都做完才生效，只开工厂菜单那一个连不上。
- 来源：[康佳电视机怎么开启ADB模式？](https://jingyan.baidu.com/article/92255446f1d74fc41748f43d.html) 2020-02-07

**X8 等较新机型：不进工厂菜单，走连点版本号** · 证据：单源
1. 设置 →「关于本机」（或「系统信息」）
2. 连续点击「版本号」7 次，出现「开发者模式已开启」提示
3. 返回设置，进入新出现的「开发者选项」
4. 打开「USB 调试」
- 授权弹窗：有；端口 5555
- ⚠ 这条是标准安卓路径，不进工厂菜单，风险最低，所以值得先试。但它在康佳上的实际覆盖范围没被多源确认——同为康佳近年机型的 75R5 就明确没有这个选项（见 me柚 OS 系列），所以试不出来是正常的，不要为了找它去翻设置里的其它开关。
- 来源：[康佳电视x8如何进入系统开发模式](https://www.informat.cn/qa/120685) 2024-11-09
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（证据落在 B50U 和 70U5D 这两台易柚机型上：非 root 状态下 pm disable-user 和 pm uninstall --user 0 都能用。已被记录成功的具体命令有 adb shell pm disable-user com.konka.livelauncher（冻结原厂桌面）、adb shell pm uninstall --user 0 com.konka.livelauncher（彻底删原厂桌面用于换第三方桌面）、adb uninstall -k --user 0 com.konka.kkweather、adb uninstall -k --user 0 com.konka.systemadvert。误删的恢复命令是 adb shell pm install-existing --user 0 <包名>。注意这和创维/酷开相反——酷开上 disable-user 对系统应用会直接报 SecurityException，康佳这一代不报。）
- 禁停（5）：`com.konka.livelauncher`、`com.droidlogic`、`com.android.vpndialogs`、`com.android.proxyhandler`、`com.android.location.fused`
- 可停（10）：`com.konka.systemadvert`、`com.konka.adverttool`、`com.konka.upgrade`、`com.konka.appupgrade`、`com.konka.message`、`com.konka.fourkshow`、`com.konka.hotelmenu`、`com.tvtb.kj`、`com.konka.kkweather`、`com.bajintech.karaok`
- 看你用不用（11）：`com.konka.market.main`、`com.konka.account`、`com.konka.kksmarthome`、`com.konka.cloudsearch`、`com.konka.downloadcenter`、`com.konka.childrenlock`、`com.konka.apkhall.edu`、`com.konka.yilife`、`com.cibn.tv`、`com.ixigua.android.tv.wasu`、`com.tencent.qqmusictv`
- 坑：冻结或卸载原厂桌面 com.konka.livelauncher 之前，必须先装好第三方桌面、按一次 Home 键选「永远运行」，并在电视自带的「电视管家」里把「开机启动桌面」改成新桌面。顺序反了就是按 Home 无响应或黑屏。
- 坑：有人执行清单命令时报 Unknown option: --user，说明那台机器的 pm 版本不支持 --user 参数（安卓版本偏老）。这时不要硬试别的写法，改用 pm disable-user，或者干脆放弃这台。
- 坑：较新的康佳系统默认屏蔽 U 盘直接安装第三方 APK（提示「有风险」拒装），所以 ADB 侧装基本是唯一路子。
- 坑：操作失误的兜底：① 设置里「清除数据、恢复出厂设置」+ 清缓存双清；② 关机拔电源等 10 秒，插电后按住【音量下】+【开机键】进 recovery 双清；③ adb reboot recovery。非硬件故障基本都能救回来。

### me柚 OS（Smartisan 深度定制，R5 系列）（未查实具体起止；已知实测帖发于 2025-06，机型为康佳 75R5，android 未查到）

- 认：「关于本机」里系统名写「me柚 OS」（不是易柚）；界面是 Smartisan（锤子）团队深度定制的风格，默认首屏是「影音库」；随机是蓝牙语音遥控器，**没有数字键**。看到 me柚 + 蓝牙遥控器这个组合，就知道要准备蓝牙键盘。
- 值不值：值得做，但主要不是为了性能：实测帖作者的动机是默认桌面/首屏对老人不友好、找不到想看的应用。这代机器性能不差，收益在「把界面改简单」和「去掉开机广告」。

**康佳 75R5（me柚 OS + 蓝牙遥控器）：靠外接蓝牙键盘输入 2008** · 证据：单源
1. 先在电视的蓝牙设置里配对一个蓝牙键盘（因为随机的蓝牙遥控器没有数字键，输不了 2008）
2. 把信号源切到「模拟电视」
3. 按遥控器上的菜单键（三个横杠）
4. 在蓝牙键盘上输入 2008
5. 选择底部的「其他选项」，找到「打开 ADB」，用遥控器点击开启
6. 到网络设置里记下电视的 IPv4 地址，电脑 adb connect <IP> 连接
- 授权弹窗：无；端口 5555
- ⚠ 只开「打开 ADB」这一项，「其他选项」里的其它条目不要动。这一代明确没有「连点版本号 7 次」的开发者选项（原帖作者翻遍论坛也没找到），别在设置里瞎点浪费时间。
- 来源：[纯小白为康佳75R5电视机开启adb删除广告经验分享（me柚os+蓝牙遥控器版本）](https://www.right.com.cn/forum/thread-8431622-1-1.html) 2025-06-09
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=yes（75R5 上 pm uninstall --user 0 对整套 com.konka.* 预装清单可用（作者原样执行了易柚代的同一份清单）；disable-user / hide 在这台机器上没有被测过。）
- 坑：75R5 的遥控器方向键用一阵子就会「连键」——按上下左右会跳到别的方向；原帖作者售后换了一个、网上又买了一个都没解决。如果用户抱怨方向键乱跳，那是硬件问题，不是你清理清出来的，事先说清楚。
- 坑：KKTV 的蓝牙遥控器同样没有数字键，2008 输不进去，这是康佳/KKTV 近年机型最常见的卡点（恩山帖下就有人直接问「KKTV 遥控器没有数字按键，怎么输入 2008」）。解法是配一个蓝牙键盘。

### 易AI 系统（2025-07 正式推出；2025-11 的 R10 搭载「易AI 1.6」，android 未公开）

- 认：「关于本机」写「易AI」或「易AI 1.x」，不再写易柚；官网产品页上这些机型的卖点标注是「易AI 系统 | 远程协助 | 视频通话」。主打多模态（语音 + 图片）交互和「易桌面」两步直达。
- 值不值：先别做：2025 年之后的新机，性能和广告状况都不构成清理理由，而且没有任何 ADB 入口的实测记录。要先把广告关掉的话走系统设置里的推荐/个性化开关，别上 ADB。
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（无任何实测记录。）
- 坑：易AI 是 2025 年才推出的新系统，网上搜到的「康佳开 ADB」教程全部是易柚/me柚时代的，菜单文案和层级都可能已经变了；照着老教程在新机的工厂菜单里翻找是有风险的。

**待验证 · 欢迎投稿**

- 🔴 广为流传的康佳机型清单（42K1 / 55K1 / 40K1Y / 49K1Y / 49K1C / 40U1 / 58U1）极可能是 2017 年同一位作者把创维/酷开那篇的段落复制到康佳帖里造成的串品牌，已在「易柚早期」系列的 pitfalls 里写明证据。要彻底定案，需要找到一台实体康佳机对上这些型号，本轮没做到。
- 易AI 系统（2025 年起，K10 / G10 / R10）没有任何 ADB 或开发者模式的实测记录；不确定它是延续「菜单键 + 2008」工厂菜单，还是改成了别的口令，或者已经封死。
- 康佳的无线调试是否有独立入口（Android 11+ 的配对码机制），还是必须先有线 adb tcpip 切换——没有查到康佳专属证据，现有资料全是 adb connect <IP>:5555。
- 康佳的「必须保留」包名只确认到桌面（com.konka.livelauncher）和几个被误列的 AOSP 组件；信号源、遥控器服务、输入法、工厂菜单本体这几类的康佳专属包名没有找到可靠来源，不像创维那样有完整的拆机清单。
- 康佳 LED55M2 有一篇 2020 年的 ZNDS 帖提到用无线鼠标「连续点击 系统 → 关于 → 生产…」开 Wi-Fi ADB，但摘要不全、没能取到完整步骤，本文件没有收录。
- 「菜单键后连点 5 次回看键」这条工厂菜单口令只有一个来源，也没给适用型号范围；它的价值在于不需要数字键，值得后续核实。
- 康佳近两年是否有 Google TV / 谷歌认证机型、其开发者选项是否已统一为标准「连点版本号 7 次」，缺官方或多源确认。

## 乐视 / Letv / LeEco · 适用

能做：乐视 Q85（CV560-C75 板卡，Android 12）经贡献者实机实测，可在信号源浮动菜单输 2580 呼出工厂菜单开启 ADB，并用 disable-user 停用广告与预装包。该机开机视频广告由系统原生服务播放，仅停用 APK 无法去除（社区经验见 references/case-letv-q85.md，属高风险操作，本工具不执行）。老款 EUI 机型暂无可核实来源，待投稿。

**怎么认机型**

- remote_has_digits：关键注意点：信号源菜单下按2580必须用数字键输入。老款红外遥控器自带数字键；新款简易蓝牙遥控器若无数字键，可在电视USB口外接一个电脑USB键盘敲数字2580。
- system_name_shown_at：设置 →「系统信息」或「关于本机」；老机型标明 EUI 5.x/6.x，新板卡机型由于系统极简，仅显示设备名称为 AndroidTV、安卓版本 12
- about_page_fields：设备名称（AndroidTV）；安卓版本（12 / 5.x / 6.x）；客户产品（如 Q85_CV560-C75_930MA_Letv_BRT）；板型（如 CV560-C75）；MAC 地址；WIFI MAC 地址
- model_naming_rule：乐视整机型号以尺寸与系列字母组合，如超3/超4代际（X40/X43/X50/X55/X65、Max 70），近年新机型如 Q85（85英寸巨幕QLED）。在工厂菜单「客户产品」字段可看到精确硬件标识。
- sticker_location：机身背部铭牌印有完整型号与生产批次；工厂菜单顶部也会直接显示准确板型与客户产品型号
- year_ranges：2013-2016 初代/超2/超3（Android 4.x-5.x）；2016-2020 超4/超5/全面屏系列（Android 6.x-7.x）；2021-2024 近代大屏系列（如贡献者实测的 2024-11 固件 Q85）

### 乐视 Q85 / CV560 板卡系列（Android 12，海思方案）（2021–2024，android Android 12）

- 认：设置菜单极简（仅系统信息、系统升级、恢复出厂设置、服务协议四项），系统信息显示设备名称为 AndroidTV、安卓版本 12。在信号源浮窗下按 2580 可呼出工厂菜单，顶部显示客户产品 Q85_CV560-C75_..._Letv_BRT。
- 值不值：值得做：3GB 运存，贡献者实测用 disable-user 停用广告与后台冗余组件后可用内存增加约 100–200 MB。开机视频广告不在 APK 内，停用后仍会播放。

**CV560-C75 及同代板卡乐视电视（信号源菜单按 2580）** · 证据：本机实测
1. 开机后在任意界面按遥控器【信号源】键，呼出信号源浮动菜单（注意：停在浮动菜单上，切勿切入全屏信号源）
2. 在信号源浮动菜单弹窗显示时，依次按下数字键「2580」
3. 屏幕左侧弹出蓝底「工厂菜单」
4. 按遥控器【方向下键】进入【其他选项】（若没有则进入【常用选项】）
5. 找到【ADB】或【ADB调试】，将其设置为「开 / ON」
6. 按返回键退出工厂菜单，将电视关机断电，重新通电开机生效
7. 电脑终端执行：adb connect 电视IP:5555
- 授权弹窗：无；端口 5555
- ⚠ 只改 ADB 一项。工厂菜单中严禁进入「屏参调整」（会导致花屏/倒屏）和「老化模式」（红绿蓝纯色闪烁锁死）。
- 来源：[贡献者实机实测（PR #1）：乐视 Q85，CV560-C75，2024-11-12 固件，Android 12](https://github.com/sanshengai/sansheng-tv-adb/pull/1) 2026-10-20
- 命令能力：disable-user=yes · hide=no · uninstall-user0=unknown（Android 12 原生行为：pm disable-user --user 0 完美可用且可原样还原；pm hide 会报 MANAGE_USERS 权限异常不可用。）
- 禁停（5）：`com.stv.deskplatform`、`com.cultraview.setting`、`android.cultraview.tv.service`、`com.hisilicon.tv.service`、`com.cultraview.tvboot`
- 可停（8）：`com.stv.bootadmanager`、`com.cultraview.retail`、`com.cultraview.screensaver`、`com.android.printspooler`、`com.ctv.autotest.helper`、`com.le.lepay.tv`、`com.letv.tvos.intermodal`、`com.stv.appdaemon`
- 看你用不用（4）：`com.aispeech.tvui`、`com.stv.voice`、`com.letv.lekids`、`com.letv.tvos.appstore`
- 坑：信号源按 2580 的触发时机：必须在按信号源键后屏幕浮出信号源菜单列表时输入；如果已经切入 HDMI1 等全屏信号源画面后再按无效。
- 坑：开机视频广告（/system/product/media/bootvideo.mp4，约 11MB）由系统原生服务 /system/bin/bootvideo 播放，停用 com.stv.bootadmanager 等 APK 后仍会出现。贡献者通过修改 persist 系统属性关闭，属于改系统持久属性，超出本工具「只用 pm disable-user、每步可还原」的范围，本工具不执行；如需了解见 references/case-letv-q85.md「社区经验 · 高风险」一节，风险自负。
- 坑：com.letv.lekids 抢占开机桌面：该包注册了 HOME category 过滤器，开机会自动抢占前台拉起 VipHomeActivity。停用后系统桌面自然归位至官方纯净桌面 com.stv.deskplatform。

**待验证 · 欢迎投稿**

- 经典 EUI 系列（超3/超4/X 系列，Android 4.x–7.x）的工厂菜单 ADB 入口（信号源界面输 2580）与可停包名，缺可核实的具体来源链接，欢迎投稿
- 部分老款 Android 4.x 乐视电视是否存在免 root 禁用系统 APK 的替代命令

## NVIDIA Shield TV · 适用

三代 Shield（2015 / 2017 / 2019）都是原生 Android TV，开发者选项和 ADB 完全开放，而且最后全部升到了 Android 11（Shield Experience 9.x），`pm uninstall -k --user 0` 可用、可回滚。Shield 本身不卡也没有 Amazon 那种广告位，做它主要是关 2021 年 Google 塞进首页的推荐 / 广告行，以及去掉 NVIDIA 和 Google 的一堆遥测服务。

**怎么认机型**

- system_name_shown_at：设置 → 设备偏好设置（Device Preferences）→ 关于（About）。页面底部有「SHIELD Android TV 软件版本」（Shield Experience 版本号，如 9.2.x）和「Android 版本」两行，两个都要看：硬件代际看不出来，但 Shield Experience 版本能确认已升到 Android 11。
- about_page_fields：设备名称（SHIELD Android TV / SHIELD TV Pro）；Android 版本（最终都是 11）；SHIELD Android TV 软件版本（Shield Experience，如 9.0.0 / 9.2.x）；内部版本号 / Build（连点这一行 7 次开开发者模式）；Android 安全补丁级别
- model_naming_rule：NVIDIA 不用型号字母编码，按发售年份和 Pro/非 Pro 区分：2015 款（含 2015 Pro）、2017 款（含 2017 Pro）、2019 款有两个形态 — 圆柱形的 Shield TV（俗称 Tube，2 GB 内存）和方形的 Shield TV Pro（3 GB 内存）。`adb shell getprop ro.product.model` 能直接读出机型字符串。
- sticker_location：机身底部贴纸印有型号和序列号；2019 Tube 是圆柱体，贴纸在底面。
- year_ranges：2015 / 2017 / 2019 三代；2019 款至今仍在售。

### Shield TV / Shield TV Pro（2015 款）（2015–2017 发售，软件支持延续至今，android 出厂 Android 5.1，通过 Shield Experience 逐代升级，最终到 Android 11（Shield Experience 9.0 起））

- 认：扁平的三角形黑色盒子，正面有一条绿色发光条；标配的是游戏手柄而不是小遥控器（遥控器当年是另买的）。关于页里 Android 版本经过更新后会显示 11。
- 值不值：机龄 10 年上下，但 3 GB 内存在电视盒子里仍然够用，卡不卡主要看装了多少 App。做它的三个信号里「广告」最成立：2021 年 Android TV 桌面更新后首页多了推荐行和横幅推广位，而且关不干净。机龄信号成立、内存信号不成立。

**2015 款（升到 Shield Experience 9.x / Android 11 之后）** · 证据：多源
1. 设置 → 设备偏好设置（Device Preferences）→ 关于（About）
2. 滚到最底下的「内部版本号 / Build」，用遥控确认键连点 7 次，出现「您现在是开发者」
3. 返回：设置 → 设备偏好设置 → 开发者选项（Developer options）
4. 走网线 / Wi-Fi 远程连：打开「网络调试（Network debugging）」，页面上会直接显示 IP:端口
5. 用 USB 线连：改为打开「USB 调试（USB debugging）」
6. 电脑执行 `adb connect <IP>:5555`，电视上会弹「允许 USB 调试吗」，选「一律允许」
- 授权弹窗：有；端口 5555
- ⚠ 🔴 NVIDIA 开发者论坛明确更正过一个流传很广的错误：USB 调试和网络调试不要同时打开，按连接方式二选一。另外打开 USB 调试时，USB 口上原来接的存储或键鼠要先拔掉。
- 来源：[How to Use the New SHIELD TV in MTP or ADB mode — NVIDIA 官方支持文章（Settings→About→Build 按 7 次）](https://nvidia.custhelp.com/app/answers/detail/a_id/4367/~/how-to-use-the-new-shield-tv-in-mtp-or-adb-mode) 
- 来源：[NVIDIA 开发者论坛：Network debugging 与 USB debugging 的正确用法](https://forums.developer.nvidia.com/t/nvidia-shield-tv-how-to-enable-usb-adopted-storage-using-adb-complete-step-by-step-guide-verification/362774) 
- 来源：[r/ShieldAndroidTV：adb connect Shield TV over Network 实操](https://www.reddit.com/r/ShieldAndroidTV/comments/qupv9c/adb_connect_shield_tv_2019_tube_over_network) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（原生 Android TV，`adb shell pm uninstall -k --user 0 <包名>` 和 `pm disable-user --user 0 <包名>` 都可用；`-k` 保留数据，恢复用 `adb shell pm install-existing <包名>`。多篇 Shield 专门的 debloat 指南用的都是 uninstall 路线并给了成对的恢复命令。）
- 禁停（4）：`com.google.android.tvlauncher`、`com.android.systemui`、`com.android.tv.settings`、`com.google.android.inputmethod.latin`
- 可停（16）：`com.nvidia.stats`、`com.nvidia.enhancedlogging`、`com.nvidia.SHIELD.Platform.Analyser`、`com.nvidia.diagtools`、`com.nvidia.feedback`、`com.nvidia.shield.registration`、`com.nvidia.shieldbeta`、`com.nvidia.benchmarkblocker`、`com.nvidia.developerwidget`、`com.google.android.tvrecommendations`、`com.google.android.leanbacklauncher.recommendations`、`com.google.android.backdrop`、`com.google.android.feedback`、`com.google.android.tv.bugreportsender`、`com.google.android.backuptransport`、`com.google.android.partnersetup`
- 看你用不用（11）：`com.google.android.katniss`、`com.google.android.gms`、`com.android.vending`、`com.nvidia.ota`、`com.nvidia.shield.remote.server`、`com.nvidia.hotwordsetup`、`com.nvidia.shieldtech.hooks`、`com.nvidia.shieldtech.proxy`、`com.nvidia.ocs`、`com.google.android.videos`、`com.google.android.tts`
- 坑：🔴 首页广告关不干净。2021 年 Google 推的 Android TV 桌面更新给 Shield 首页加了横幅推广位，NVIDIA 把责任推给 Google，Google 论坛给出的唯一「官方」办法是回滚 Android TV Home 的更新（清数据 + 卸载更新 + 关自动更新），会一直落后版本。真正干净的做法是换第三方 launcher。
- 坑：不装第三方 launcher 也能减掉一部分：设置 → 主屏幕（Home screen）→ 自定义频道（Customize channels）里把不想要的频道全关，再关掉「启用视频预览」和「启用音频预览」，首页就只剩一排 App 图标。这是零风险的第一步，应该先做完再考虑动包。
- 坑：🔴 Shield 上换 Projectivy 等第三方 launcher 要两处设置同时开才生效：① launcher 自己的设置里打开「覆盖当前启动器 / override current launcher」；② 设置 → 设备偏好设置 → 无障碍（Accessibility）里给这个 launcher 打开权限。只做一处 Home 键不会跳转，这是最常见的卡点。
- 坑：`pm uninstall -k --user 0` 只是对当前用户卸载，系统分区里的包还在；恢复用 `adb shell pm install-existing <包名>`。但 Shield Experience 系统更新之后部分包可能会回来，做完记得留一份自己动过的包名清单。
- 坑：🔴 NVIDIA 官方更正：USB 调试和网络调试不要同时打开，按连接方式二选一。网上不少教程（包括一份被广泛引用的 PDF）写成两个都开，那是错的。
- 坑：社区有 root / 解锁 bootloader / 刷开发者版固件的讨论，本项目不采用，也不记录方法。

### Shield TV（2017 款）（2017–2019 发售，android 出厂 Android 7，最终升到 Android 11（Shield Experience 9.0 起））

- 认：外形和 2015 款几乎一样（扁三角形 + 绿色光条），但盒子里标配的是小遥控器而不是手柄，遥控器是细长的三角柱形、顶部有麦克风孔。关于页 Android 版本经更新后显示 11。
- 值不值：和 2015 款一样：3 GB 内存不紧张，做它是为了去首页推荐位和遥测，不是救卡顿。机龄 8 年左右，还在正常吃 Shield Experience 更新。

**2017 款** · 证据：多源
1. 设置 → 设备偏好设置 → 关于
2. 「内部版本号 / Build」连点 7 次 → 「您现在是开发者」
3. 返回：设置 → 设备偏好设置 → 开发者选项
4. 远程连打开「网络调试（Network debugging）」，页面会显示 IP 和端口；USB 线连则打开「USB 调试」（二选一，不要同开）
5. 电脑 `adb connect <IP>:5555`，电视上选「一律允许」
- 授权弹窗：有；端口 5555
- ⚠ 同 2015 款：USB 调试与网络调试二选一；USB 调试打开时 USB 口不能同时接存储/键鼠。
- 来源：[How to Use the New SHIELD TV in MTP or ADB mode — NVIDIA 官方](https://nvidia.custhelp.com/app/answers/detail/a_id/4367/~/how-to-use-the-new-shield-tv-in-mtp-or-adb-mode) 
- 来源：[r/ShieldAndroidTV：Shield 支持通过 Wi-Fi 或以太网走 adb](https://www.reddit.com/r/ShieldAndroidTV/comments/hrq7yo/is_there_any_way_to_access_adb_for_nvidia) 
- 来源：[NVIDIA GeForce 论坛：How to enable ADB network debugging](https://www.nvidia.com/en-us/geforce/forums/shield-tv/9/423710/how-to-enable-adb-network-debugging) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（与 2015 款同一套 Shield Experience 系统，包管理行为一致。）
- 禁停（3）：`com.google.android.tvlauncher`、`com.android.systemui`、`com.android.tv.settings`
- 可停（9）：`com.nvidia.stats`、`com.nvidia.enhancedlogging`、`com.nvidia.SHIELD.Platform.Analyser`、`com.google.android.tvrecommendations`、`com.google.android.leanbacklauncher.recommendations`、`com.google.android.backdrop`、`com.nvidia.shield.registration`、`com.nvidia.shieldbeta`、`com.nvidia.diagtools`
- 看你用不用（5）：`com.google.android.katniss`、`com.google.android.gms`、`com.android.vending`、`com.nvidia.ota`、`com.nvidia.shield.remote.server`
- 坑：2017 款和 2015 款外形几乎一样，靠盒子里配的是手柄还是遥控器来分；但对 ADB 操作来说两代没有差别，不必纠结。

### Shield TV（2019 Tube）/ Shield TV Pro（2019）（2019 至今在售，android 出厂 Android 9，升到 Android 11（Shield Experience 9.0 起））

- 认：2019 Tube 是一根圆柱体、两头接线，遥控器是三角柱形带背光按键和内置寻物蜂鸣器；2019 Pro 是方形黑盒子、带两个 USB 口。关于页 Android 版本 11、Shield Experience 9.x。
- 值不值：🔴 2019 Tube 只有 2 GB 内存（Pro 是 3 GB），是三代 Shield 里内存最紧的一台 — 装满流媒体 App 之后切换会明显变慢，遥测和推荐服务的常驻开销在这台上最值得清。2019 Pro 不缺内存，做它纯粹是去广告位。两款都还在吃更新，机龄信号不成立。

**2019 Tube 与 2019 Pro** · 证据：多源
1. 设置 → 设备偏好设置（Device Preferences）→ 关于（About）
2. 滚到底，「内部版本号 / Build」连点 7 次 → 「您现在是开发者」
3. 返回：设置 → 设备偏好设置 → 开发者选项
4. 打开「网络调试（Network debugging）」，开关下面会直接显示可连的 IP:端口 — 记下它
5. 电脑执行 `adb connect <IP>:5555`
6. 电视上弹出「允许 USB 调试吗」，选「允许」并勾「一律允许来自此计算机」
- 授权弹窗：有；端口 5555
- ⚠ Shield 到 Android 11 为止仍然是传统的 5555 端口直连，没有改成 Android 13+ 那套「无线调试 + 配对码」流程 — 这一点和同期的 Chromecast / Google TV 设备不同，不要把两边的步骤搞混。
- 来源：[r/ShieldAndroidTV：adb connect Shield TV 2019 (Tube) over Network — 需先开 Network debugging，首次连会弹授权](https://www.reddit.com/r/ShieldAndroidTV/comments/qupv9c/adb_connect_shield_tv_2019_tube_over_network) 
- 来源：[NVIDIA 开发者论坛：Shield TV Pro 2019 走 ADB over Network 的完整步骤](https://forums.developer.nvidia.com/t/nvidia-shield-tv-how-to-enable-usb-adopted-storage-using-adb-complete-step-by-step-guide-verification/362774) 
- 来源：[How to Use the New SHIELD TV in MTP or ADB mode — NVIDIA 官方](https://nvidia.custhelp.com/app/answers/detail/a_id/4367/~/how-to-use-the-new-shield-tv-in-mtp-or-adb-mode) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（XDA 上的 Shield debloat 帖明确标注「Tested on Nvidia Shield 2019」，用的就是 ADB 禁用 / 卸载两条路线；shield_optimizer 工具也在 Shield 2019 上跑，并提供一键 Recovery 把所有被禁包重新启用。）
- 禁停（4）：`com.google.android.tvlauncher`、`com.android.systemui`、`com.android.tv.settings`、`com.google.android.inputmethod.latin`
- 可停（16）：`com.nvidia.stats`、`com.nvidia.enhancedlogging`、`com.nvidia.SHIELD.Platform.Analyser`、`com.nvidia.feedback`、`com.nvidia.diagtools`、`com.nvidia.shield.registration`、`com.nvidia.shieldbeta`、`com.nvidia.benchmarkblocker`、`com.nvidia.developerwidget`、`com.nvidia.shield.nvcustomize`、`com.nvidia.shield.appselector`、`com.google.android.tvrecommendations`、`com.google.android.leanbacklauncher.recommendations`、`com.google.android.backdrop`、`com.google.android.tv.bugreportsender`、`com.google.android.feedback`
- 看你用不用（10）：`com.google.android.katniss`、`com.google.android.gms`、`com.android.vending`、`com.nvidia.ota`、`com.nvidia.shield.remote.server`、`com.nvidia.hotwordsetup`、`com.nvidia.shieldtech.hooks`、`com.nvidia.shieldtech.proxy`、`com.google.android.videos`、`com.google.android.tts`
- 坑：🔴 2019 Tube 只有 2 GB 内存。清理之后确实会宽松一些，但如果用户装了 6–8 个流媒体 App，切换卡顿的根因是内存不够，清遥测服务只能缓解不能根治 — 别把预期说成「清完就不卡了」。
- 坑：Shield 到 Android 11 为止仍是 5555 直连，没有配对码。如果在 Shield 上找不到「无线调试」选项，那是正常的，要找的是「网络调试（Network debugging）」。
- 坑：同 2015/2017 款：换 Projectivy 等 launcher 要「launcher 内的覆盖开关」+「设置 → 设备偏好 → 无障碍里给它授权」两处同时开；只做一处 Home 键不跳转。

**待验证 · 欢迎投稿**

- `pm hide` 在 Shield 上是否可用？本轮所有来源用的都是 pm uninstall -k --user 0 和 pm disable-user，没有一条用 pm hide。
- com.nvidia.shieldtech.hooks / com.nvidia.shieldtech.proxy / com.nvidia.ocs 到底管什么？三份社区 debloat 清单都把它们列为可删，但没有任何一份说明用途或禁用后的后果，也没有 NVIDIA 官方文档。已按「用途不明」放进 ask_user，不要升级成 junk。
- Shield Experience 9.x 系统更新之后，被 pm uninstall -k --user 0 移除的包会不会自动回来？Florisse / Level1Techs 都只给了手动恢复命令，没有说更新后的行为。
- 2019 Tube（2 GB）实际清理后能腾出多少内存？本轮没有找到像 Fire TV 那样的 dumpsys meminfo 实测数据，worth_it_hint 里只能定性描述。
- Shield 首页那条横幅推广位对应的是哪个包？关掉 tvrecommendations / leanbacklauncher.recommendations 之后横幅是否还在？社区讨论只说「关不干净」，没有定位到具体包名。
- 社区有 root / 解锁 bootloader / 刷开发者版固件的讨论（XDA 上有 2019 Pro 的 developer image 教程），本项目不采用。

## 创维 / 酷开 / Skyworth / Coocaa · 适用

能做：绝大多数国内创维/酷开机型走「本机信息 → 上上下下左右左右 → 工厂菜单 → 打开 ADB」，是国产电视里入口最稳定、教程最多的一家；但工厂菜单是厂商自制界面，只能动 ADB 那一项。值不值得：2019 年前的 1–2GB 机型收益最大；2024 年后的 AIOS 机型社区还没有实测教程，先别指望。

**怎么认机型**

- remote_has_digits：2016 年后的创维/酷开遥控器多数没有数字键；主流入口用方向键输入，不依赖数字键，这点比康佳/长虹友好
- system_name_shown_at：设置 → 关于本机（部分老机型叫「版本信息」）→ 本机信息；这一页同时显示本机型号、机芯号和系统版本号，也是「上上下下左右左右」必须站在的那一页
- about_page_fields：本机型号（如 55G7200）；机芯号 / 芯片方案号（如 8H87、7S711、K5C）；系统版本号 / 软件版本（酷开系统 x.x 或内部版本号如 180524）；MAC 地址；序列号
- model_naming_rule：创维整机按定位分 W > S > Q > G > A 五档（ZNDS 2021 解析：W「尖端机型」、S「旗舰机型」、Q「高品质机型」、G「国民机型」，A 是 2020 年新增、主打年轻人的系列）；型号前面的数字是尺寸，如 65A5D = 65 英寸 A 系列。子品牌酷开（coocaa）另用 K / U / P / S / C 等字母+数字，如 42K1、40U1、P50 Pro、K3 Pro。注意创维官网「G 系列（游戏电竞）」指的是显示器不是电视，别混。
- sticker_location：机身背面右下角的铭牌贴纸印有完整型号与序列号；机芯号一般只在「关于本机」里看得到，贴纸上没有
- year_ranges：酷开系统 5.x ≈ 2016–2019，6.x ≈ 2017–2018，7.x ≈ 2018–2019，8.x ≈ 2019–2023，AIOS ≈ 2024 起

### 酷开系统 5.x（5.0 / 5.5 / 5.8）（2016 年发布 5.0，小版本延续到约 2019，android Android 5.x 附近（同代机型实测见 Android 5.0 / API 21））

- 认：「设置 → 关于本机 → 版本号」里写「酷开系统 5.0 / 5.5 / 5.8」或「Coocaa 5.x」；桌面是横向大色块的瀑布流首页，顶部一排「精选 / 影视 / 应用 / 我的」。这一代遥控器多数还带数字键盘。
- 值不值：值得做：这代机型普遍 1–2GB 运存，开机广告与预装最多，机龄已 7–10 年，卡顿明显，清理后提升最直观；同时这代 ADB 限制最松，很多机型连接后不弹授权框。

**酷开系统 5.x 通用主路径** · 证据：多源
1. 遥控器按「设置」，进入「关于本机」（部分机型这一级叫「版本信息」）
2. 进入「本机信息」子页面，停在这一页不要退出
3. 用方向键缓慢依次按：上、上、下、下、左、右、左、右（是「左右左右」，不是「左左右右」）
4. 屏幕进入「工厂菜单 / 工程模式」
5. 选择「通用设置」或「常用开关」，找到「ADB」，设为「开」
6. 连按返回键退出工厂菜单，部分机型需断电重启才生效
- 授权弹窗：无；端口 5555
- ⚠ 只改「ADB」这一项。工厂菜单里还有屏参、色差、老化模式、STR 待机等硬件参数，ZNDS 原帖明确写「仅适用于有技术功底的玩家」，误改可能花屏或需要刷机恢复。改完立刻连按返回退出，不要在里面翻。
- 来源：[创维智能电视打开工厂模式，开启ADB方法](https://www.znds.com/tv-646545-1-1.html) 2017-02-22
- 来源：[2017创维酷开最全面攻略：安装软件看直播教程集合！](https://n.znds.com/article/19153.html) 2017-02-07
- 来源：[酷开电视进入工程模式，打开ADB调试方法【图文】](https://www.znds.com/tv-949448-1-1.html) 2017-07-03
- 来源：[Skyworth | QuickTVUI 官方设备接入文档（通用方法一）](https://quicktvui.com/zh-CN/guide/device/skyworth.html) 

**创维 E660E 等个别机型（工厂菜单里 ADB 开关在「常用开关」而不是「通用设置」）** · 证据：单源
1. 同上进入工厂菜单
2. 选择「常用开关」
3. 把「ADB」设置为「开」
- 授权弹窗：无；端口 5555
- ⚠ 只改「ADB」这一项。
- 来源：[创维智能电视打开工厂模式，开启ADB方法（创维E660E 段落）](https://www.znds.com/tv-646545-1-1.html) 2017-02-22

**酷开 K1 系列（42K1 / 42K1T / 42K1Y / 42K1TY / 55K1 / 55K1T）——工厂菜单要密码** · 证据：多源
1. 同上进入工厂菜单
2. 选择「高级设置」
3. 输入密码「123456」
4. 在里面找到 ADB 开关并打开
- 授权弹窗：有；端口 5555
- ⚠ 只改「ADB」这一项。「高级设置」里是整机出厂参数，进去后除 ADB 外不要动任何条目。
- 来源：[创维智能电视打开工厂模式，开启ADB方法（42K1/55K1 系列段落）](https://www.znds.com/tv-646545-1-1.html) 2017-02-22
- 来源：[创维电视如何开启adb调试功能以安装APP](https://www.cnblogs.com/05-hust/p/17089031.html) 2023-02-03
- 来源：[创维电视如何打开ADB（百度经验）](https://jingyan.baidu.com/article/5225f26b693131e6fa09083f.html) 

**酷开 40K1Y / 49K1Y / 49K1C——没有 ADB 开关，改的是参数项** · 证据：多源
1. 同上进入工厂菜单
2. 选择「其它设置」
3. 找到「Adbd Enable」这一项
4. 把它后面的参数值从 0 改成 1
- 授权弹窗：无；端口 5555
- ⚠ 只改「Adbd Enable」这一项，同页其它参数不要改。
- 来源：[创维智能电视打开工厂模式，开启ADB方法（40K1Y/49K1Y/49K1C 段落）](https://www.znds.com/tv-646545-1-1.html) 2017-02-22
- 来源：[【欢视商店】创维、TCL、长虹及海信电视进入工厂模式方法](https://zhuanlan.zhihu.com/p/20849467) 2016-05-05

**酷开 40U1 / 58U1——工厂菜单里根本没有 ADB 相关项** · 证据：单源
1. 进入工厂菜单后确认「高级设置」「其它设置」里都没有 ADB / Adbd Enable
2. 确认没有就到此为止，不要在工厂菜单里乱试其它开关
3. 原帖说法是升级到较新固件后 ADB 会自动打开，但这一条原帖自己也写的是「据说」，没有实测证据
- 端口 5555
- ⚠ 只看不改。这一型属于「查到了但没有可用入口」，不要为了找开关去动工厂菜单里的其它参数。
- 来源：[创维智能电视打开工厂模式，开启ADB方法（40U1/58U1 段落）](https://www.znds.com/tv-646545-1-1.html) 2017-02-22

**酷开系统 5.x 老机型的 PC 端连接（开关打开之后）** · 证据：多源
1. 确认电脑与电视连同一个局域网（路由器）
2. 在电视「网络设置」或「关于本机」里看电视的 IPv4 地址
3. 电脑执行 adb connect <电视IP>（默认端口 5555）
4. 看到 connected to <IP>:5555 即成功；这一代多数机型不弹授权框
- 授权弹窗：无；端口 5555
- ⚠ 工厂菜单里那个 ADB 开关本身就是授权，打开后局域网内任何人都能连上这台电视，做完清理建议回工厂菜单把它关掉。
- 来源：[酷开5.0系统无线ADB调试模式开启教程](https://www.znds.com/tv-482323-1-1.html) 
- 来源：[创维电视如何开启adb调试功能以安装APP（连接与异常处理）](https://www.cnblogs.com/05-hust/p/17089031.html) 2023-02-03
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（这一代没有找到落到具体机型/版本的 pm 命令实测记录，不照搬 6.x/8.x 的结论。）

### 酷开系统 6.x（6.0 / 6.2）（2017–2018（百度百科标 6.0 为 2017 年「千人千面」），android Android 6.0 附近（论坛拆机帖里的 5S58_K5C 实测为 Android 6.0，2018 年上市））

- 认：「关于本机 → 版本号」写「酷开系统 6.0 / 6.2」。这一代的桌面开始出现「千人千面」的个性化推荐首页；U 盘装第三方应用在这一代基本还不拦（多篇 2018–2020 年教程称 6.0 版「没有任何限制」，可以直接从应用圈装当贝市场）。
- 值不值：值得做：2GB 运存为主，机龄 7–8 年，广告服务 com.tianci.ad 在同代机型上实测 CPU 占用可达 26%，清掉体感提升明显。

**酷开系统 6.x 通用（与 5.x 同一条路径）** · 证据：多源
1. 设置 → 关于本机 / 版本信息 → 本机信息
2. 方向键缓慢按：上、上、下、下、左、右、左、右
3. 进入工厂菜单，选「通用设置」，把「ADB」打开
4. 连按返回退出
- 授权弹窗：无；端口 5555
- ⚠ 只改「ADB」这一项。同一菜单里的「STR 待机」在部分机型（5S58_K5C）实测改了也不生效，别反复折腾它。
- 来源：[创维酷开电视各版本软件安装方法汇总](https://www.fdeer.com/2403.html) 2020-11-29
- 来源：[创维电视如何开启adb调试功能以安装APP](https://www.cnblogs.com/05-hust/p/17089031.html) 2023-02-03
- 命令能力：disable-user=no · hide=yes · uninstall-user0=yes（证据落在 5S58_K5C（酷开系统 6.x 代、Android 6.0、2018 年上市）这台机器上：非 root 的 adb shell（uid=2000）对系统签名应用（uid=1000）执行 pm disable-user 直接报 SecurityException: Permission Denial: attempt to change component state；改用 pm uninstall -k --user 0 对 com.coocaa.screensaver、com.tianci.ad 等预装应用返回 Success（属于对 user 0 卸载，不删底层分区，OTA 或恢复出厂后可能复原）；pm hide 能隐藏，但对 com.tianci.ad 重启后会被系统自动恢复，原帖作者最终是 root 后在 install-recovery.sh 里加 sleep 10 + pm hide 才稳定——本项目不采用 root 路线，所以对这台机器的结论是：广告服务只能「卸到 user 0」，不能指望 hide 持久。）
- 禁停（11）：`com.tianci.setting`、`com.skyworth.tv`、`com.skyworth.tv.provider`、`com.tianci.ipc`、`com.tianci.system`、`com.skyworth.tianciime`、`com.android.packageinstaller`、`com.android.defcontainer`、`com.coocaa.factory`、`com.android.providers.tv`、`com.skyworthdigital.stb.dataprovider`
- 可停（8）：`com.tianci.ad`、`com.coocaa.screensaver`、`com.tianci.autoinstaller`、`com.tianci.push`、`com.skyworth.skymarket`、`com.coocaa.app_browser`、`com.coocaa.teampresent`、`com.bajintech.karaok`
- 看你用不用（5）：`com.tianci.movieplatform`、`com.tianci.user`、`com.coocaa.sky.ccapi`、`com.coocaa.os.customerservice`、`com.coocaa.emergencywarning`
- 坑：换掉默认桌面 com.tianci.movieplatform 之后，系统设置、coocaaTV 等界面 UI 会变丑，「定时关机」功能失效；需要用高版本固件里对应的应用替换或反编译修改才能修回来。
- 坑：彻底删掉广告服务 com.tianci.ad 之后，恢复出厂设置时出现「无操作 100 秒自动黑屏（无背光），LED 灯还是绿的，安卓仍在运行」；根因是换桌面后丢了原厂 Receiver 的唤醒逻辑。原帖作者最后的选择是保留广告服务、只 hide 不删。
- 坑：部分酷开机型 /sdcard 被阉割（没有可写的内置存储用户目录），依赖 /sdcard 的第三方应用（如 TVBox 读本地接口文件）会不正常；恢复它要动 boot 分区，风险高，本项目不做。
- 坑：工厂菜单里的「STR 待机」在 5S58_K5C 上实测无效，连反编译强行加回选项也不生效——说明是硬件不支持，改配置越不过硬件能力边界，别在这上面耗。

### 酷开系统 7.x（7.0 / 7.5）（2018–2019（7.0 定位「全视频交互电视系统」；7.5 见于酷开 P70，内置百度 AI 语音），android 未查到明确版本号，推测 Android 7–8 区间，无实证）

- 认：「关于本机 → 版本号」写「酷开系统 7.0 / 7.5」。7.5 这一代开始主推远场语音（可以不用遥控器直接喊），遥控器上通常多一个麦克风键。
- 值不值：值得做：机龄 6–7 年、2GB 运存居多，广告已经比较密；但这一代没有独立实测教程，进工厂菜单后菜单项名称可能与 6.x/8.x 不完全一致，要现场对着找。

**酷开系统 7.x（按同代通用路径试，未见该版本的独立实测帖）** · 证据：单源
1. 设置 → 关于本机 / 版本信息 → 本机信息
2. 方向键缓慢按：上、上、下、下、左、右、左、右
3. 进入工厂菜单后，在「通用设置」「常用开关」「高级设置」「其它设置」里逐个找 ADB 或 Adbd Enable
4. 找到就打开，找不到就退出，不要试其它开关
- 端口 5555
- ⚠ 只改 ADB 那一项。这一代缺实测帖，路径属于按同代通用规律外推，界面对不上就停手，别在工厂菜单里挨个点。
- 来源：[创维酷开电视各版本软件安装方法汇总](https://www.fdeer.com/2403.html) 2020-11-29
- 来源：[酷开电视（百度百科，7.5 系统与 P70 机型）](https://baike.baidu.com/item/%E9%85%B7%E5%BC%80%E7%94%B5%E8%A7%86/4379482) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未找到落到 7.x 具体机型的 pm 命令实测记录。）

### 酷开系统 8.x（约 2019–2023（这一代跨度最长，创维与酷开双品牌主力机型都在这里），android 以 Android 9 为主，部分较新机型见 Android 11）

- 认：「关于本机 → 版本号」写「酷开系统 8」或一串纯数字内部版本号（如 180524、191223）——看到纯数字版本号基本就是这一代。桌面首页顶部是「精选 / 剧集 / 电影 / 应用 / 我的」横向 Tab，开机有 15–30 秒可跳过或不可跳过的开机广告。
- 值不值：值得做：2–4GB 运存，机龄 2–6 年，开机广告和「猜你喜欢」推荐最密集，是清理收益/风险比最好的一代。注意这一代起 U 盘直装第三方 APK 的限制变严，ADB 侧装基本是唯一路子。

**酷开系统 8.x 通用主路径（2023–2024 年的教程仍在用这一条）** · 证据：多源
1. 设置 → 关于本机 → 本机信息
2. 方向键缓慢按：上、上、下、下、左、右、左、右
3. 进入工厂菜单，选「高级设置」或「通用设置」
4. 部分机型此处要输密码，常见「123456」
5. 找到「ADB」开关打开，连按返回退出，必要时断电重启
- 授权弹窗：有；端口 5555
- ⚠ 只改「ADB」这一项。这一代的工厂菜单里多了屏参与背光校正项，改错要返厂，进去之后直奔 ADB，不要顺手看别的。
- 来源：[创维电视如何开启adb调试功能以安装APP](https://www.cnblogs.com/05-hust/p/17089031.html) 2023-02-03
- 来源：[创维酷开电视进入工厂模式，打开ADB调试教程](https://www.4k123.com/archiver/tid-62507.html) 
- 来源：[汇总13款安卓电视机开启Adb（创维段落）](https://zhuanlan.zhihu.com/p/1901349166480880333) 

**部分酷开机型的另一条路：快速演示模式 + 应用调试（密码 69573028）** · 证据：单源
1. 设置 → 本机信息
2. 方向键按：上、上、下、下、左、左、右、右（注意这一篇记录的是「左左右右」，与主路径不同）
3. 打开「快速演示模式」
4. 回到「应用」里搜索「应用调试」
5. 输入密码「69573028」
6. 之后即可安装 U 盘里的 APK
- 授权弹窗：有
- ⚠ 这条走的是「装第三方应用」而不是「开 ADB 网络调试」，两者不等价。只开「快速演示模式」和「应用调试」，工厂菜单里其它项不要动。方向键顺序与主路径不同（左左右右），主路径按不出来时才试这条。
- 来源：[酷开TV安装第三方APP方式，打开abd调试](https://blog.csdn.net/m0_46268055/article/details/135572963) 2024-01-13
- 命令能力：disable-user=no · hide=unknown · uninstall-user0=yes（证据来自 2024-07 的创维去广告帖：非 root 的 adb shell（uid=2000）对系统签名应用执行 pm disable-user 报 SecurityException: Permission Denial: attempt to change component state from pid=xxx, uid=2000, package uid=1000；同帖里 pm uninstall -k --user 0 对同一批预装应用返回 Success。⚠️ 这篇帖子没有写明机型与酷开系统版本号，只能按发帖时间（2024-07）推定属于 8.x 代，所以标 单源、不能当作全代际定论。）
- 禁停（3）：`com.tianci.setting`、`com.skyworth.tianciime`、`com.coocaa.factory`
- 可停（5）：`com.tianci.ad`、`com.coocaa.screensaver`、`com.tianci.autoinstaller`、`com.tianci.push`、`com.skyworth.skymarket`
- 坑：adb connect 之后设备状态显示 offline，或提示 already connected 装不上东西：先 adb disconnect → adb kill-server → adb start-server 重连，还不行就回电视端把 ADB 开关关掉再打开。
- 坑：「上上下下左右左右」最常见的失败原因是节奏太快或记成了「上上下下左左右右」，多篇教程专门强调要缓慢、且是「左右左右」。

### 酷开 AIOS / Coolita AIOS（2024 年 4 月发布系统，2024 年 9 月首款搭载机型酷开 K3 Pro；2024 年 10 月以「Coolita AIOS」名义在广交会作为出海系统亮相，android 官方未公开底层 AOSP 版本）

- 认：开机后桌面是「AI 智控桌面」，首页以对话式/智能体入口为主（影音、娱乐、生活、健康、教育、通用六个智能体），而不是老版的横向 Tab 瀑布流；遥控器有独立 AI/语音键，喊话可以直接跳到影视片段。海外机型上这套系统叫 Coolita。
- 值不值：先别做：这一代是 2024 年后的新机，运存和流畅度本来就不差，广告收益有限；而且社区目前没有任何 ADB 开启实测记录，贸然在工厂菜单里翻找风险大于收益。
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（无任何实测记录。）
- 坑：酷开科技官网关于 AIOS 的页面是面向电视厂商的 B2B 能力介绍（六大智能体、出海方案），完全没有开发者选项/ADB 的说明，别把官网当教程找。

### 创维 / Skyworth 海外 Android TV · Google TV 认证机型（年份与覆盖型号未查实，android 标准 Android TV / Google TV 认证版本）

- 认：认这三条：① 开机动画或设置页出现 Google TV / Android TV 字样与 Google 账号登录；② 遥控器上有 Google 助理（麦克风）键和 YouTube / Netflix 硬按键；③ 设置里是「设置 → 关于 → Android TV OS 版本 / 软件版本号」这种原生结构，而不是创维自家的「关于本机 → 本机信息」。满足这些就走原生开发者选项，不要去按「上上下下左右左右」。
- 值不值：看情况：原生 Android TV / Google TV 的预装广告远少于国内酷开系统，多数人调一调推荐设置就够了；真要清理的话入口是标准的，风险也最低。

**创维官方开发者文档给出的通用方法二（原生开发者选项）** · 证据：官方
1. 进入「设置」→「关于电视」/「关于」
2. 找到「软件版本号」（Google TV 机型上叫 Android TV OS build），连续点击 7 次
3. 看到「您现在是开发者」提示
4. 返回上一级，进入新出现的「开发者选项」
5. 打开「USB 调试」
6. 部分设备需要断电重启才生效
- 授权弹窗：有；端口 5555
- ⚠ 这是标准安卓路径，不进厂商工厂菜单，风险最低。首次 adb connect 时电视上会弹「允许 USB 调试吗」，必须用遥控器确认。
- 来源：[Skyworth | QuickTVUI 官方设备接入文档（通用方法二）](https://quicktvui.com/zh-CN/guide/device/skyworth.html) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未查到创维海外机型上的 pm 命令实测记录。）
- 坑：QuickTVUI 官方文档把「上上下下左右左右」和「连点软件版本号 7 次」并列为创维电视的「通用方法一 / 通用方法二」，并没有说方法二专属于 Google 认证机型。所以不能反推出「哪些型号是原生路径」——实际做的时候是两条都试，哪条界面对得上用哪条。

**待验证 · 欢迎投稿**

- 酷开 AIOS / Coolita AIOS（2024 年起，首搭 K3 Pro）没有任何社区实测的 ADB 或开发者模式记录，官方也未公开；不确定它是延续「上上下下左右左右」工厂菜单，还是切到了标准 Android TV 开发者选项，甚至是否已经封死。这是本品牌最大的空白。
- 创维/Skyworth 的 Android TV / Google TV 认证机型具体覆盖哪些型号和年份未查实。QuickTVUI 官方文档只把原生路径列为「通用方法二」，没有给型号范围，所以无法给用户一句「你这台是哪条路」的判据，只能让用户按设置页长相自己分辨。
- 工厂菜单密码没有建立起完整的「型号 → 密码」映射：目前只确认 K1 系列（42K1/55K1 等）用 123456、另有一篇 2024 年 CSDN 记录的「应用调试」密码 69573028；坊间还流传 9999 等其它密码但没找到可靠出处，本文件不收录未证实的密码。
- 未找到创维/酷开机型采用 Android 11+「无线调试 + 配对码」机制的确凿证据，现有资料全部是传统 adb connect <IP>:5555；较新机型是否强制走配对码流程待验证。
- 酷开系统 7.0 / 7.5 这一代缺少独立的 ADB 开启实测帖，本文件里 7.x 的路径是按 6.x/8.x 的通用规律外推的，工厂菜单各级子菜单名称是否一致未核实。
- 早期非智能/半智能创维机型流传「按住机身或遥控器菜单键不放，再依次按 9、7、8」进工厂模式的说法（2016 年搜狐汇总帖），与本文件收录的智能电视路径是两套东西，未验证，也不适用于本项目要处理的安卓机型。
- com.coocaa.remotectrlservice、com.mstar.android.providers.tv 等包在原始拆机帖里被作者标注为「不确定能不能删」，没有第二个独立来源，本文件没有收进任何一组名单。
- 社区里存在针对酷开系统的 root 讨论（例如用 root 让 pm hide 持久生效），本项目不采用。

## 索尼 Sony BRAVIA · 适用

2015 年起索尼电视全线用 Android TV / Google TV，开发者选项和 ADB 都在，国行与海外路径一致；2015–2018 的老机型只有 2GB 内存、系统已停更，是本项目收益最大的一批；2021 年后的 Google TV 机型如果被推到 Android 11+ 的「无线调试」，就得用配对码而不是 5555 直连。

**怎么认机型**

- remote_has_digits：多数国行机型遥控器有 0–9 数字键；海外机型常省略。判断是不是 Android/Google TV 看遥控器有没有麦克风键（官方判据），不看数字键。
- system_name_shown_at：按遥控器 HOME 看首页：左上或右上出现「Google TV」字样 + 圆形 Google 账号头像 = Google TV（2021 年起）；首页长相类似但右上角没有账号头像 = Android TV（2015–2020）；两者都没有、是一屏方块图标的老界面 = 非安卓机型（2014 年及更早），本项目不适用。另外「设置 → 系统 → 关于 / 关于本机」页里能看到系统版本。
- about_page_fields：Android TV OS 版本 / 安卓TV操作系统（国行译名）；Android 安全补丁级别；内部版本号 / Build；型号 KD-xxxxx / XR-xxxxx / K-xxxxx；BRAVIA 处理器名（X1 / X1 Extreme / XR 认知芯片）
- model_naming_rule：海外 KD/XR + 尺寸 + 系列字母 + 年份字母：X=LCD、A=OLED、Z=旗舰巨屏；结尾字母就是年份代号 C=2015、D=2016、E=2017、F=2018、G=2019、H=2020、J=2021、K=2022、L=2023。例：KD-55X9000E 是 2017 年的 X90 系列，XR-65A80J 是 2021 年的 A80 OLED。2024 年起改名为 BRAVIA 3 / 7 / 8 / 9（型号前缀 K-）。国行型号多为 KD-55X9000E 这种同构写法，但并非每个海外型号都有国行对应款。
- sticker_location：机身背面贴纸，或侧面接口附近的标签；壁挂时用小镜子照背面贴纸再用手机拍照（索尼官方建议）。型号和序列号是两个不同的号。
- year_ranges：2015–2020 = Android TV（Android 5.0 → 10）；2021 至今 = Google TV（Android 10 → 14）。2014 年及更早的 BRAVIA 不是安卓系统。

### 第一代 Android TV（2015–2016）（2015–2016，android 出厂 Android 5.0/5.1（Lollipop），OTA 升到 6.0（Marshmallow），2018 年索尼又给 2015/2016 机型推了 Android 8.0 Oreo，之后停更）

- 认：首页是老式 Android TV 横排「推荐 / 应用 / 游戏 / 设置」行，没有 Google 账号头像；设置里能看到「Discovery 菜单」这种索尼自有项（索尼官方后来专门发过 2015–2016 机型 Discovery 菜单变更公告）。型号结尾字母是 C 或 D。
- 值不值：收益最高的一批。索尼社区从 2015 年起就有一条几百页的「2015 Bravia Android TV Issues」长帖，集中在开机慢、遥控延迟、待机后卡顿；官方社区另有用户确认这代用 MediaTek SoC、只有 2GB 内存且焊死在主板上。系统已停更，Sony Select / 促销 / 日志上报这些常驻服务白占内存。做完 debloat 能明显改善，但硬件上限在那，别期望变成新电视。

**2015–2016 全系（Android 5/6/8）** · 证据：多源
1. 遥控器按 HOME → 进「设置」
2. 找到「关于」（Android 8 上路径是「设置 → 设备偏好设置 → 关于」）
3. 高亮「内部版本号 / Build」，用遥控器确认键连按 7 次，屏幕提示「您现在是开发者」
4. 返回上一级，菜单里出现「开发者选项」
5. 进「开发者选项」，打开「ADB 调试」（老系统上就叫这个名字，没有「网络调试」项）
6. 另外要装第三方 APK 的话：「设置 → 安全和限制 → 未知来源」打开
7. 电脑上 adb connect 电视IP:5555，电视上会弹授权框，勾「一律允许来自这台计算机」再确定
- 授权弹窗：有；端口 5555
- ⚠ 这代系统内部存储本来就小，安装第三方 APK 前先在「设置 → 存储」看剩余空间，低于 15% 会更卡。
- 来源：[【超全】2024各品牌电视打开ADB调试方法汇总](https://www.znds.com/tv-1251785-1-1.html) 2024-08-15
- 来源：[Developer Functions（Sony 官方社区：Settings > About > Build 连按 7 次）](https://community.sony.co.uk/t5/android-tv/developer-functions/td-p/2143518) 
- 来源：[skinny-bravia：Sony Bravia TV Debloating & Performance Optimization（明确写了老机型是「ADB debugging」、新机型是「Network Debugging」）](https://github.com/therealhoodboy/skinny-bravia) 
- 来源：[索尼中国官方：安装应用程序时提示「禁止安装」？（安全和限制 → 未知来源；安卓 9 需先进设备偏好设置）](https://www.sony.com/zh-cn/electronics/support/articles/00146629) 2025-03-11
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（社区 debloat 教程在索尼上同时用过 pm disable-user --user 0 和 pm uninstall --user 0 两种，都成功。本项目一律只用 disable-user：出问题能用 pm enable 直接恢复，不用恢复出厂。索尼官方设置界面本身也有图形化的「应用 → 选中应用 → 停用」，很多包不用上 ADB 就能关。pm hide 有人在索尼社区问过但没有成功回帖，算未知。）
- 禁停（4）：`com.sony.dtv.tvx`、`com.sony.dtv.braviasyncservice`、`com.android.location.fused`、`com.sony.dtv.seconddispsetting`
- 可停（10）：`tv.samba.ssm`、`com.sony.dtv.sonyselect`、`com.sony.dtv.promos`、`com.sony.dtv.discovery`、`com.sony.dtv.demomode`、`com.sony.dtv.sonybugreportsys`、`com.google.android.tv.bugreportsender`、`com.google.android.tvrecommendations`、`com.sony.dtv.imanual`、`com.sony.dtv.b2b.hotelmode`
- 看你用不用（7）：`com.google.android.katniss`、`com.google.android.apps.mediashell`、`com.android.vending`、`com.sony.dtv.smartmediaapp`、`com.vewd.core.integration.dia`、`com.sony.dtv.hbbtvlauncher`、`com.google.android.marvin.talkback`
- 坑：网上流传最广的那份 skinny-bravia 脚本用的是 pm uninstall --user 0（卸载），不是 disable，而且清单里混进了电视核心 com.sony.dtv.tvx、无障碍 TTS / TalkBack、BRAVIA Sync（HDMI-CEC）和联系人 / 日历 Provider。整段复制粘贴会同时废掉「电视」信号源、一个遥控器控全屋和读屏功能。只能当包名字典用，不能当执行清单用。
- 坑：Android TV 机型（2015–2020）首页那些推荐内容，索尼官方明确说「无法移除」；只有 2021 年起的 Google TV 机型能靠「仅应用模式」（设置 → 帐号与登录 → 选中帐号 → 仅应用模式 → 开启）把推荐行藏起来。所以老索尼想干净只能走 ADB 关包或换第三方桌面。

### 第二代 Android TV（2017–2018）（2017–2018，android 2017 机型出厂 Android 6.0，2018 机型出厂 7.0；后续升 8.0 Oreo，大部分止步于 Android 9）

- 认：型号结尾字母 E（2017）或 F（2018）。首页仍是 Android TV 横排推荐行，右上角没有 Google 账号头像。关于页 Android 版本停在 8.0 或 9。
- 值不值：同样值得做。索尼官方社区有明确的停更公告：2017、2018 年的大部分机型（X900E、X900F 这些热门款在内）和 2019 年的部分机型（AG8 OLED）已经到了 Android TV 生命终点，不再拿新系统版本。停更 = 后台服务永远不会被官方优化掉，关掉就是净收益。

**2017–2018 全系** · 证据：多源
1. 遥控器按 HOME → 「设置」
2. 「设备偏好设置 → 关于」（老版本菜单里直接是「关于」）
3. 高亮「内部版本号 / Build / Android TV OS Build」，确认键连按 7 次，提示「您现在是开发者」
4. 返回 → 「开发者选项」
5. 打开「ADB 调试」；系统较新的机型这一项叫「网络调试 / Network Debugging」
6. 装第三方 APK 另需：「设置 → 安全和限制 → 未知来源」打开（Android 9 机型路径是「设置 → 设备偏好设置 → 安全和限制」）
7. adb connect 电视IP:5555，电视上确认授权弹窗
- 授权弹窗：有；端口 5555
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[skinny-bravia（Preparation 一节：About → Build 连点 7 次，Developer Options → Network Debugging 或 ADB debugging；作者在 Android 9 Pie 上实测）](https://github.com/therealhoodboy/skinny-bravia) 
- 来源：[索尼中国官方：安装应用程序时提示「禁止安装」？](https://www.sony.com/zh-cn/electronics/support/articles/00146629) 2025-03-11
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（与第一代相同。skinny-bravia 作者在 Android 9 Pie 的索尼机型上实测过整套命令。）
- 禁停（3）：`com.sony.dtv.tvx`、`com.sony.dtv.braviasyncservice`、`com.android.location.fused`
- 可停（6）：`tv.samba.ssm`、`com.sony.dtv.sonyselect`、`com.sony.dtv.promos`、`com.sony.dtv.demomode`、`com.sony.dtv.sonybugreportsys`、`com.google.android.tvrecommendations`
- 看你用不用（4）：`com.google.android.katniss`、`com.google.android.apps.mediashell`、`com.sony.dtv.hbbtvlauncher`、`com.google.android.marvin.talkback`
- 坑：这一代已经收到索尼的 Android TV 停更通知（2017、2018 年大部分机型 + 2019 部分机型）。停更意味着以后不会再有新系统版本，但索尼仍可能推应用层更新，被禁用的系统包不会被自动重新启用——不过做过一次恢复出厂就全回来了，得重跑。

### 第三代 Android TV（2019–2020）（2019–2020，android 出厂 Android 8.0 / 9，2023 年索尼推固件 v6.6053 把 2018–2020 机型升到 Android 10）

- 认：型号结尾字母 G（2019）或 H（2020）。首页还是 Android TV 样式（无 Google 账号头像），但关于页 Android 版本可能已经是 10。设置菜单里有「设备偏好设置」这一层。
- 值不值：中等偏上。内存比 2015 代大，日常没那么卡，但推荐行和上报服务一样多；Android 10 之后首页广告位反而更明显。想要干净首页值得做。

**2019–2020 全系（Android 9 / 10）** · 证据：多源
1. HOME → 「设置」→「设备偏好设置」→「关于」
2. 高亮「内部版本号 / Android TV OS Build」，确认键连按 7 次
3. 返回 →「设备偏好设置」或「系统」里出现「开发者选项」
4. 打开「网络调试 / Network Debugging」（这一代大多显示这个名字），没有的话打开「ADB 调试」
5. 装 APK 另需：「设置 →（设备偏好设置 →）安全和限制 → 未知来源」，给你用的文件管理器 / 安装器单独授权
6. adb connect 电视IP:5555，电视上确认授权
- 授权弹窗：有；端口 5555
- ⚠ Android 9 起「未知来源」从一个全局开关变成按应用授权，只开全局没用，必须给具体那个安装器打勾。
- 来源：[skinny-bravia（Preparation：Device Preferences → About → Build 7 次；Developer Options → Network Debugging）](https://github.com/therealhoodboy/skinny-bravia) 
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[索尼中国官方：安装应用程序时提示「禁止安装」？（Android 9 需先选「设备偏好设置」再选「安全和限制」）](https://www.sony.com/zh-cn/electronics/support/articles/00146629) 2025-03-11
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（同上两代。）
- 禁停（3）：`com.sony.dtv.tvx`、`com.sony.dtv.braviasyncservice`、`com.android.location.fused`
- 可停（6）：`tv.samba.ssm`、`com.sony.dtv.sonyselect`、`com.sony.dtv.promos`、`com.sony.dtv.discovery`、`com.google.android.tvrecommendations`、`com.sony.dtv.demomode`
- 看你用不用（3）：`com.google.android.katniss`、`com.google.android.apps.mediashell`、`com.google.android.marvin.talkback`
- 坑：这一代在 2023 年被索尼从 Android 9 推到 Android 10（固件 v6.6053）。跨大版本升级会重置一部分系统应用状态，做过 debloat 的机器升级后要复查哪些包被重新启用了。

### Google TV 世代（2021 至今）（2021 至今，android 出厂 Android 10 / 11（Google TV 界面），2025 年索尼给 2023 机型推 Android 12，部分 2024/2025 机型已到 Android 14）

- 认：按 HOME，首页左上或右上有「Google TV」字样，右上角有圆形 Google 账号头像（未登录时屏幕底部也有 Google TV 说明）——这是索尼官方给的判据。遥控器带麦克风键，还有 Netflix / YouTube / Prime Video 等一排彩色快捷键。型号前缀 XR-（2021–2023 带认知芯片）或 K-（2024 起 BRAVIA 3/7/8/9）。
- 值不值：看诉求。硬件不缺内存，做 debloat 主要图的是首页干净和少上报，不是提速。这一代有官方的「仅应用模式」可以先试——不用 ADB 就能藏掉推荐行；试完不满意再上 ADB。

**Google TV 机型，系统在 Android 10 / 11 且开发者选项里能看到「网络调试 / ADB 调试」** · 证据：多源
1. HOME → 右上角齿轮进「设置」→「系统」→「关于」（国行叫「关于本机」）
2. 高亮「Android TV OS 版本 / 安卓TV操作系统」，遥控器确认键连快点 7 次，屏幕提示「您已处于开发者模式」
3. 返回上一级，「系统」里出现「开发者选项」
4. 进去打开「USB 调试」以及「网络调试 / ADB 调试」
5. 装第三方 APK 另需：「设置 → 隐私设置 → 安全限制 → 未知来源」，给安装器（国行是「应用助手」）单独打开权限
6. adb connect 电视IP:5555，电视上勾「一律允许来自这台计算机」再确定
- 授权弹窗：有；端口 5555
- 来源：[索尼电视如何开启开发者选项？（什么值得买，国行界面逐字：设置 → 系统 → 关于本机 → 安卓TV操作系统 连点 7 次；开发者选项 → USB 调试；隐私设置 → 安全限制 → 应用助手）](https://post.smzdm.com/p/aognq4on) 2026-02-18
- 来源：[汇总13款安卓电视机开启Adb，全是干货！](https://zhuanlan.zhihu.com/p/1901349166480880333) 
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[索尼中国官方（A95L 使用指南）：U 盘装 APK 走【主菜单】→【更多应用】→【应用助手】](https://service.sony.com.cn/Enjoyment/A95L/02.html) 

**开发者选项里只有「无线调试 / Wireless debugging」没有 5555 直连的机型（Android 11 起的新系统，索尼把部分 2023 机型推到 Android 12→14 之后也变成这样）** · 证据：多源
1. 先按上一条把开发者选项打开
2. 「开发者选项 → 无线调试 / Wireless debugging」打开
3. 选「使用配对码配对设备」，电视上显示一个六位配对码和一个 IP:配对端口（端口是随机的，关掉弹窗就换）
4. 电脑上 adb pair 电视IP:配对端口，按提示输入配对码
5. 配对成功后回到「无线调试」主页面，记下页面上「IP 地址和端口」那一行（这个调试端口和配对端口不是同一个，重启电视会变）
6. adb connect 电视IP:调试端口
- 授权弹窗：有；需配对码
- ⚠ 调试端口每次重启电视都会变，做成开机自动连接的脚本会失效；每次重启后要回电视上重新看端口。
- 来源：[A Complete Guide to Android ADB Wireless Debugging（配对端口与调试端口是两个不同端口，调试端口重启才变）](https://gadgetguideonline.com/android/android-adb-wireless-debugging) 
- 来源：[PSA: Using the new Android 12 tile for Wireless debugging to establish adb over Wi-Fi（XDA：Android 11 起端口随机分配）](https://xdaforums.com/t/psa-using-the-new-android-12-tile-for-developer-options-wireless-debugging-to-establish-adb-connection-over-wi-fi-without-usb.4469339/) 
- 来源：[Android Debug Bridge broken with Android 14 Security Features（Home Assistant issue #148359：Android 14 拿掉了 5555，改成配对码 + 动态端口）](https://github.com/home-assistant/core/issues/148359) 2025-07-07
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（Google TV 机型上 AVSForum 的 A80J/A90J 清单大量使用 pm disable -k --user 0，成功。本项目仍统一用 disable-user。）
- 禁停（5）：`com.google.android.apps.tv.launcherx`、`com.google.android.tungsten.setupwraith`、`com.sony.dtv.tvx`、`com.sony.dtv.braviasyncservice`、`com.android.location.fused`
- 可停（7）：`com.sony.dtv.promos`、`com.sony.dtv.sonyselect`、`com.google.android.tvrecommendations`、`com.google.android.apps.tv.dreamx`、`tv.samba.ssm`、`com.sony.dtv.sonybugreportsys`、`com.sony.dtv.demomode`
- 看你用不用（5）：`com.google.android.play.games`、`com.sony.dtv.smartmediaapp`、`com.android.vending`、`com.google.android.apps.mediashell`、`com.sony.dtv.voice.guide`
- 坑：🔴 先试不用 ADB 的官方办法：Google TV 机型有「仅应用模式」（设置 → 帐号与登录 → 选中你的帐号 → 往下翻到「仅应用模式 / Apps only mode」→ 开启），首页会变成只有应用图标，推荐行全部消失。代价是用不了个性化结果和语音助手。索尼官方文章明确说这是 Google TV 独有、Android TV 机型没有的能力。
- 坑：「仅应用模式」不是永久解药：有媒体报道开了之后过一段时间「个性化推荐」会重新出现，现在只能算减少广告而不是彻底关掉。想要确定性还是得关包或换桌面。
- 坑：索尼在 2025 年给部分机型推了 Android 12 → 14 的系统更新，更新后老的 adb connect ip:5555 直连失效，只剩配对码流程。升级前做好的自动化脚本会静默失败。这条在 r/AndroidTV 有讨论、在 Home Assistant 的 issue 里有同类症状记录（Android 14 拿掉 5555 改动态端口），但没有索尼官方公告。
- 坑：AVSForum 的那份 A80J/A90J 清单是 2021 年、海外 Google TV 10 系统整理的，它的「未登录 Google 账号」前提对国行机型不成立；国行是否预装同一批遥测服务、包名是否一致没有核实过，别整份照抄。
- 坑：国行特有：装第三方 APK 不是靠系统自带文件管理器，而是索尼国行预置的「应用助手」。索尼中国官网 A95L 使用指南给的路径是【主菜单】→【更多应用】→【应用助手】，APK 要放 U 盘根目录；还要先在「隐私设置 → 安全限制」里给应用助手开权限，否则装上了也打不开。海外机型没有这个应用。

**待验证 · 欢迎投稿**

- 国行索尼开 ADB / 网络调试之前，是否必须先登录 Google 账号完成开机向导？海外资料没提这个依赖，国行说明也没写。国行机型 GMS 状态本身就和海外不同，需要实测一台国行机才能定。
- 国行「应用助手」的包名是什么？它属于 must_keep（是国行唯一的 APK 安装入口）还是可以关，没找到有来源的包名。
- 索尼遥控器 / 蓝牙遥控相关的包名（类似 com.google.android.tv.remote.service 或索尼自有的 bluetoothrcu 服务）在 BRAVIA 上叫什么，没找到可靠清单。这是 must_keep 里目前唯一空着的关键类别——关错了遥控器直接失灵。
- 索尼输入法 / 屏幕键盘的包名未核实（关错了 Wi-Fi 密码都输不进去）。
- com.sony.dtv.servicemode（Sony Service Mode，工厂 / 维修模式）在 skinny-bravia 里被列为可卸载，但没有任何资料说明它被关掉后会不会影响售后维修或固件恢复。本项目按「不碰」处理，不写进任何一组。
- 各代具体机型的内存大小没有官方规格可查（索尼规格页不列 RAM）。2GB 这个数字来自索尼官方社区用户讨论，不是官方数据，只能当参考。
- 国行是否存在阉割 GMS 的特供固件版本、若存在其 ADB 行为是否与国际版一致，仍未找到资料（第一轮就留着的问题，本轮没有进展）。
- 2024 年起改名的 BRAVIA 3 / 7 / 8 / 9（K- 前缀）上开发者选项路径是否有变化、是否默认就是无线调试配对码模式，没有针对这些新型号的教程或实测。
- 社区有 root / 解锁 bootloader 的讨论，本项目不采用，也未检索其内容。

## 小米 / Redmi / Xiaomi TV · 适用

从 2013 年初代到 2026 年的澎湃 OS 4，小米/Redmi 电视一直走同一套「关于页连点解锁开发者 → 账号与安全里打开 ADB 调试 → 电脑 adb connect 电视IP:5555 → 电视上按确认授权」的路子，是国产电视里入口最稳定、教程最多的品牌；海外 Mi TV / Xiaomi TV 分 PatchWall（老机型，入口同国内）和 Google TV（新机型，改走 系统→关于→构建号连点→开发者选项）两条线。值得做：整机广告多、入门款只有 1.5–2GB 运存。

**怎么认机型**

- remote_has_digits：国内近年主力遥控（小米电视 A/S、Redmi A/X）是无数字键的蓝牙语音遥控，只有方向键、返回、主页和小爱/语音键；印度及东南亚的 PatchWall 机型遥控通常带完整数字键，并印有 Netflix / Prime Video / PatchWall / Xiaomi TV+ 快捷键——看到 PatchWall 实体键基本可断定是海外版
- system_name_shown_at：国内：设置 → 关于（老机型叫「小米电视设置 → 关于」，2018 年后多为「关于电视」）；海外 Google TV 机型：设置 → 系统 → 关于，或 设置 → 设备偏好设置 → 关于（小米香港官网给的查序列号路径就是 设置-附加设置-设备首选项-关于-状态）
- about_page_fields：产品型号（老机型在这一项上连点解锁开发者模式）；版本号 / 系统版本（2018 年后机型在这一项上连点）；MIUI TV 版本（MIUI for TV 时代显示）；小米澎湃OS 版本号，形如 OS3.0.28.0.UFTMATV —— 尾缀带 ATV 的就是电视端固件；Android TV OS build / 构建号（海外 Android TV、Google TV 机型）；网络信息：以太网 MAC、WiFi MAC、蓝牙 MAC（找客服关开机广告要用这三个）
- model_naming_rule：国内整机型号印成 L<屏幕尺寸>M<平台代号>-<子系列后缀>，例如 L55M5-AZ = 小米电视4A 55 英寸(2017)、L55M5-AD = 同机型 2018 款、L65M7-Z2 = 小米电视6 OLED 65 英寸、L65M7-EA = 全面屏 EA 系列 65 英寸；后缀字母就是子系列（EA/ES/EC/EX、A、S、RA/RX=Redmi）。🔴 2023-03-20 小米官方（公关部总经理王化）宣布电视命名变更：EA/ES 里的 E 原本代表「无边框」，因为市面上已无有边框产品而去掉，EA→A 系列、ES→S 系列，所以 2023 年 3 月前后同一档产品名字会突然少一个字母，不是两条产品线。海外则用 Mi TV 4A/4X/4S/5X、Mi TV P1/Q1/A2、Xiaomi TV A / A Pro 这类名字，与国内型号不通用。
- sticker_location：机身背板右下角或接口区旁的铭牌贴纸上印 L..M..-.. 完整型号；不方便搬电视时，海外 Google TV 机型可在 设置 → 附加设置 → 设备首选项 → 关于 → 状态 → 序列号 里查（小米香港官网 KA-17810），国内机型在 设置 → 关于 → 产品型号 里查
- year_ranges：2013 小米电视初代（MIUI for TV 1.0）；2017–2019 小米电视4/4A/4C/4X/4S 与「全面屏」E 系列；2019–2021 小米电视5/6、Redmi 电视 A/X/MAX；2020-09 起推 MIUI for TV 3.0「无界桌面」；2022 推 MIUI 13 TV；2023-03 EA/ES 改名 A/S；2023-10 起澎湃OS(HyperOS) 逐步覆盖电视端，2026-01-31 官方公告电视端澎湃OS 3 正式版开推，2026-08 电视端澎湃OS 4 Beta 开推

### MIUI for TV 1.x / 2.x 早期（小米电视 1/2/3、小米盒子 1–3 代）（2013–2016，android 基于 Android 4.x–5.x 深度定制（小米香港官网 FAQ 明确早期机型为「基于安卓4版本深度定制的电视系统」）；小米官方未公开逐代 MIUI TV 与 Android 版本的对照表）

- 认：关于页写「MIUI TV 版」而不是「小米澎湃OS」；主界面是横向排列的大图卡片＋左侧「小米电视设置」入口，没有 2020 年后的「无界桌面」；遥控器是只有方向键+主页+菜单的老式红外/蓝牙遥控，没有小爱语音键。
- 值不值：机龄 10 年上下，运存普遍 1GB、存储 4–8GB，即便精简干净也只能缓解卡顿；广告主要是开机广告和应用商店推荐。除非只当播放器用，性价比更高的做法是外接一个新盒子，把电视当显示器。

**小米电视 1/2/3 与小米盒子，遥控器在「产品型号」上连点** · 证据：官方
1. 主页 → 我的应用 → 小米电视设置（老机型入口名就叫「小米电视设置」，不是「设置」）
2. 进入「关于」
3. 选中「产品型号」，用遥控器确认/OK 键连续点 5~8 次（百度经验记的是 6 次，多数教程写 7 次），直到提示「您已处于开发者模式」
4. 返回 设置 → 账号与安全（老机型写作「帐户与安全」）
5. 把「ADB 调试」设为「允许」，顺手打开「允许安装未知来源应用」
6. 电脑执行 adb connect <电视IP>:5555（电视 IP 在 设置 → 关于 → 网络信息 里看），电视屏幕弹出「允许USB调试吗？」，用遥控器点确定
- 授权弹窗：有；端口 5555
- ⚠ 首次连接必须在电视屏幕上手动点授权框，没法纯命令行静默完成。老机型系统分区小，卸载系统应用后若要恢复往往只能三清（恢复出厂），操作前先确认自己记得住三清方式。
- 来源：[电视/盒子应用开发指南（小米开发者文档：主页→小米电视设置→账户与安全，允许安装未知来源应用、允许ADB调试）](https://dev.mi.com/docs/mitv/user_guide/) 
- 来源：[如何打開小米電視並Mi Box開發者選項？如何開啟ADB調試（小米官方支持页）](https://www.mi.com/hk/support/article/KA-17750/) 
- 来源：[小米电视怎样进入开发者模式开启ADB调试（进入关于→产品型号→连按6次OK）](https://jingyan.baidu.com/article/3052f5a1ad8320d6f21f8677.html) 2020-07-12
- 来源：[打开小米电视的adb调试开关](https://blog.csdn.net/chenzhengfeng/article/details/89242654) 2019-04-12
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（这一代机型的社区教程集中在 2015–2017 年、且多数走 root/刷机路线，本次调研没有找到针对 Android 4/5 老机型、只用 pm disable-user 或 pm uninstall --user 0 的可靠实测报告。Android 4.x 上 `pm uninstall --user 0` 本身就不一定可用（多用户卸载是 Android 5.0 之后才普及的），所以这里一律留 unknown 而不是照抄新机型的结论。）
- 坑：社区有针对这一代机型的 root / 刷机 / 救砖讨论，本项目不采用，也不记录相关方法。

### MIUI for TV 2.x 后期 ～ 3.0「无界桌面」（小米电视 4/4A/4C/4X/4S、5、6，以及「全面屏」E 系列 EA/EC/EX/ES）（2017–2021（MIUI for TV 3.0 自 2020 年 9 月起分批推送到已上市机型），android Android 6–9 区间（第三方精简教程提到「确保电视系统是安卓7以上」才好换第三方桌面，说明这一代跨了 7 前后两档）；小米未公开官方对照表）

- 认：关于页仍写 MIUI TV 版本号，没有「小米澎湃OS」字样；2020-09 之后升过级的机型桌面是「无界桌面」（大图内容瀑布＋顶部 Tab），没升级的还是老版分行卡片桌面。型号铭牌上带 E 开头后缀（EA/EC/EX/ES）的一定是这一代的「全面屏」机型——2023 年 3 月官方把 E 去掉之后就不再有新的 E 系列了。
- 值不值：这一代是精简收益最高的一档：机龄 5–9 年，运存多为 1.5–2GB，开机广告、桌面「今日头条框/视频头条」推荐位、应用商店推送全都在，禁掉后开机和桌面明显变快；同时系统还够新（多数 Android 7+），pm disable-user 可用。

**小米电视 4 系列及以后国内整机，「版本号」连点解锁 → 账号与安全开 ADB（最主流路径）** · 证据：多源
1. 设置 → 关于电视（部分机型显示为「关于」「关于本机」）
2. 选中「版本号」（老一些的机型是「产品型号」），遥控器确认键连点 5~8 次，直到出现「您已成为开发者 / 您已处于开发者模式」
3. 返回 设置 → 账号与安全（帐号与安全）
4. 把「ADB 调试」设为「允许」
5. 电脑执行 adb connect <电视IP>:5555，电视弹「允许USB调试吗？」用遥控器确认（建议勾「一律允许来自这台电脑」）
- 授权弹窗：有；端口 5555
- ⚠ 点几次没有统一标准（官方支持页写 7 次，百度经验写 6 次，也有机型 5 次），以电视自己弹出的倒计时文案为准，点到提示出现就停。
- 来源：[如何打開小米電視並Mi Box開發者選項？如何開啟ADB調試（小米官方支持页）](https://www.mi.com/hk/support/article/KA-17750/) 
- 来源：[小米机电视机/顶盒 打开USB调试教程（方法一）](https://www.quicktvui.com/zh-CN/guide/device/xiaomi.html) 
- 来源：[小米电视ADB删除系统自带无用软件及开机广告教程（作者机型为小米电视4C）](https://www.znds.com/tv-1177461-1-1.html) 2020-07-14
- 来源：[纯小白通过adb精简小米电视自带软件：①设置—关于—产品型号连点 ②设置—账号与安全—ADB调试，弹窗点允许](https://www.right.com.cn/forum/thread-8431736-1-1.html) 2025-06-10

**部分机型「账号与安全」里找不到 ADB 调试时的备用路径：设置 → 更多设置 → 开发者选项** · 证据：多源
1. 先按上一条把开发者模式解锁出来
2. 返回 设置 → 更多设置 → 开发者选项
3. 在开发者选项里分别打开「USB 调试」和「网络调试 / ADB 调试」两个开关（有的机型是两个独立开关，只开一个连不上）
4. 电脑执行 adb connect <电视IP>:5555 并在电视上确认授权
- 授权弹窗：有；端口 5555
- ⚠ 开发者选项里除了调试开关还有一堆渲染/动画/进程数设置，只改 USB 调试和网络调试这两项，别顺手动别的。
- 来源：[小米机电视机/顶盒 打开USB调试教程（方法二：返回设置→更多设置→开发者选项）](https://www.quicktvui.com/zh-CN/guide/device/xiaomi.html) 
- 来源：[小米电视启用ADB调试后仍无法连接，常见原因有哪些？（指出路径非线性：部分机型需先解锁开发者模式再进 设置→更多设置→开发者选项）](https://ask.csdn.net/questions/9337825) 

**小米官方开发者文档给的另一条入口（从应用商店进系统应用设置）** · 证据：官方
1. 主页 → 应用商店 → 应用管理 → 系统应用 → 设置 → 开发者选项
2. 打开右上角总开关
3. 勾选需要的调试项，再回到 设置 → 账户与安全 确认「ADB 调试」为允许
- 授权弹窗：有；端口 5555
- ⚠ 官方文档面向开发者写的，普通用户用上面两条更顺；这条主要用于前两条路径的菜单被改掉时兜底。
- 来源：[电视/盒子应用开发指南（小米开发者平台，给出「主页→应用商店→应用管理→系统应用→设置→开发者选项」路径）](https://dev.mi.com/docs/mitv/user_guide/) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（两条命令都能执行。社区主推 `adb shell pm disable-user <包名>`：比 uninstall 更容易回滚（pm enable 就能恢复），也更不容易被系统自动恢复。`adb shell pm uninstall --user 0 <包名>` 对多数系统应用也返回 Success，但广告插件类（尤其 com.miui.systemAdSolution）有「重启后又回来」的用户回帖。`pm hide` 在电视上没有找到实测记录，留 unknown。）
- 禁停（3）：`com.mitv.tvhome`、`com.xiaomi.mitv.tvmanager`、`com.sohu.inputmethod.sogou.tv`
- 可停（8）：`com.miui.systemAdSolution`、`com.duokan.airkan.tvbox`、`com.duokan.videodaily`、`com.xiaomi.mitv.tvpush.tvpushservice`、`com.xm.webcontent`、`com.xiaomi.mitv.handbook`、`com.xiaomi.mibox.gamecenter`、`com.xiaomi.gamecenter.sdk.service.mibox`
- 看你用不用（5）：`com.xiaomi.mitv.shop`、`com.mipay.wallet.tv`、`com.xiaomi.mi_connect_service`、`com.xiaomi.wakeupserviceadb`、`com.mitv.gallery`
- 坑：🔴 禁用桌面 com.mitv.tvhome 之后断电重启可能开不了机。实测报告来自一台 Redmi X55 2022 款（MIUI TV 2.27.5224）的精简脚本评论区：先是「没桌面了」，随后「断电后重启，不开机了」。要换第三方桌面的正确顺序是——先装好新桌面 → 设为默认并重启验证能自启 → 再禁系统桌面，且全程保持 ADB 可连（一旦黑屏，能连上 ADB 就还能 pm enable 救回来）。
- 坑：用 pm uninstall --user 0 卸载广告类系统应用后，部分应用会在重启后被系统守护进程重新装回来（评论区「删了以后重启又有了」）。所以本项目一律优先 pm disable-user，既更持久也随时能 pm enable 回滚。
- 坑：电脑端连不上电视时，先查是不是别的软件占了 5037（adb server 默认端口）——各种手机助手、模拟器都会自带一个版本不同的 adb 常驻，会把连接抢走。
- 坑：不要用「电视管家」清理垃圾来去广告：有教程记录，电视管家的清理会把用户手动替换过的 com.mitv.tvhome 缓存目录、/miad/ 广告素材目录一并「修好」，前面做的事白做。
- 坑：官方渠道可以永久关开机广告，不需要 ADB：在小米商城 App 里转人工客服（或打小米售后热线），说要关闭电视开机广告，客服会要 三个 MAC 地址——以太网 MAC、WiFi MAC、蓝牙 MAC，都在 设置 → 关于 → 网络信息 里；提交后约 2 个工作日后台生效。2023-10 起多家媒体和用户反馈一致，但没有找到小米官方公告页，所以评级为「多源但非官方」。对不敢碰 ADB 的家庭，这是首选动作。
- 坑：「设置 → 声音 → 开机广告音」只关声音，画面照放，别和「永久关闭开机广告」混为一谈。另外系统里还有「系统工具广告」总开关和各入口的「个性化推荐」开关，能关掉一批系统级广告位，属于零风险动作，应该在动 ADB 之前先做完。

### MIUI 13 TV（2022 款 ES/EA、Redmi 智能电视 X/A 2022、Redmi MAX 98）（2022 年 1 月起分批推送稳定版，android 未查到官方公布的 Android 基线版本）

- 认：关于页版本号写「MIUI 13」而不是「小米澎湃OS」；机型名还带 E（ES/EA），且是 2022 年款——2023 年 3 月之后发布的同档产品已经改叫 S/A 了。官方专题页 miui13.home.miui.com/tv 上列的机型就是这一批。
- 值不值：机龄 3–4 年，运存 1.5–2GB 居多（Redmi X Pro 等高配到 3GB）。这一代广告形态和上一代一样（开机广告＋桌面推荐位），精简收益仍然明显，且系统够新、pm 命令可用，是「做了就舒服」的一档。

**MIUI 13 TV 全系，路径与上一代完全相同** · 证据：多源
1. 设置 → 关于电视 → 选中「版本号」，遥控确认键连点 5~8 次，直到提示已成为开发者
2. 返回 设置 → 账号与安全 → 「ADB 调试」改为允许
3. 找不到时退而求其次：设置 → 更多设置 → 开发者选项 → 打开 USB 调试与网络调试
4. 电脑 adb connect <电视IP>:5555，在电视上确认授权弹窗
- 授权弹窗：有；端口 5555
- ⚠ 本次没有找到「MIUI 13 TV 改了 ADB 入口」的任何记录，各版本教程给的都是同一条路径。
- 来源：[MIUI 13（电视端）官方专题页（列出 ES/EA 2022 款、Redmi X 2022 款、Redmi MAX 98 等适配机型）](https://miui13.home.miui.com/tv) 
- 来源：[如何打開小米電視並Mi Box開發者選項？如何開啟ADB調試（小米官方支持页）](https://www.mi.com/hk/support/article/KA-17750/) 
- 来源：[小米机电视机/顶盒 打开USB调试教程](https://www.quicktvui.com/zh-CN/guide/device/xiaomi.html) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（精简脚本的实测机型正是这一代（Redmi X55 2022 款，MIUI TV 2.27.5224），disable-user 全套可用；uninstall --user 0 同样可执行，仍有广告类应用自动恢复的风险。）
- 禁停（2）：`com.mitv.tvhome`、`com.sohu.inputmethod.sogou.tv`
- 可停（3）：`com.miui.systemAdSolution`、`com.duokan.videodaily`、`com.xiaomi.mitv.tvpush.tvpushservice`
- 看你用不用（1）：`com.xiaomi.mitv.tvmanager`
- 坑：找客服永久关开机广告这条路对这一代同样成立（知乎那篇实测用的就是小米 ES 系列），且零风险，建议先做这个再考虑 ADB。

### 小米澎湃 OS（Xiaomi HyperOS）TV —— A / S / S Pro 系列与 Redmi A / A Pro / X / MAX（2023 年 10 月澎湃 OS 发布后逐步覆盖电视端；官方公告电视端澎湃 OS 3 正式版自 2026-01-31 起开推，澎湃 OS 4 Beta 自 2026-08-14 起在小米电视 S Pro Mini LED 2026 系列开推，android 未查到官方公布的 Android 基线；澎湃 OS 官方定位为「基于 Android 及 Xiaomi Vela」）

- 认：关于页的系统名写「小米澎湃OS」，版本号形如 OS3.0.28.0.UFTMATV —— 🔴 尾缀带 ATV 的一定是电视端固件，别拿手机端的 OS3.0.x 版本号对号入座。机型名不带 E（A / A Pro / S / S Pro / X / MAX），是 2023 年 3 月官方去掉 E 之后的命名。桌面上多了「控制中心」（可以直接切信号源、控米家设备），这是澎湃 OS 2 电视篇加的。
- 值不值：分两档：入门的 REDMI A 系列 2GB+32GB（2024 款更差，1.5GB+8GB），开机就吃掉 1.2–1.5GB，装两个视频 App 就转圈，精简收益最大；A Pro（3GB+64GB）和 S / S Pro Mini LED 本身不卡，做精简主要是为了去广告和推荐位。机龄都在 3 年内，系统还在收更新，动之前先想清楚是不是真的卡。

**澎湃 OS(HyperOS) TV 各版本，国内整机与盒子** · 证据：多源
1. 设置 → 关于（关于电视）
2. 选中「版本号」或「产品型号」，遥控确认键连点 5~8 次，直到提示已进入开发者模式
3. 返回 设置 → 账号与安全 → 「ADB 调试」改为允许
4. 备用路径：设置 → 更多设置 → 开发者选项 → 打开 USB 调试与网络调试
5. 电脑 adb connect <电视IP>:5555（小米官方开发者文档写明电视/盒子默认支持网络 adb 调试），在电视上确认「允许USB调试吗？」
- 授权弹窗：有；端口 5555
- ⚠ 小米官方开发者平台 2024-11 的电视文档明确写「打开 adb 方式同旧版」，本次调研也没有找到任何「澎湃 OS 改了电视端 ADB 入口」的记录；但手机端澎湃 OS 对「USB 调试（安全设置）」这类高权限开关有强制联网＋登录小米账号的要求，电视端是否同样收紧尚无专属证据，见 open_questions。
- 来源：[电视应用开发指南（小米澎湃OS 开发者平台：小米电视/盒子默认支持网络 adb 调试，打开 adb 方式同旧版）](https://dev.mi.com/xiaomihyperos/documentation/detail?pId=1329) 2024-11-14
- 来源：[纯小白通过adb精简小米电视自带软件（2025 年仍是 设置—关于—产品型号连点 + 设置—账号与安全—ADB调试）](https://www.right.com.cn/forum/thread-8431736-1-1.html) 2025-06-10
- 来源：[如何打開小米電視並Mi Box開發者選項？如何開啟ADB調試（小米官方支持页）](https://www.mi.com/hk/support/article/KA-17750/) 
- 来源：[小米Android电视或盒子ADB 开启方法](https://zhuanlan.zhihu.com/p/691431320) 2024-04-09
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=unknown（2025-06 的精简帖在澎湃 OS 时代的机型上仍在用同一套 ADB 精简流程，disable-user 路线可用。但本次没有找到 2024 年之后专门针对澎湃 OS TV 的逐命令实测报告（尤其是 uninstall --user 0 的自动恢复行为在澎湃 OS 上是否还存在），所以 uninstall_user0 留 unknown 而不是照抄 MIUI TV 的结论。）
- 禁停（1）：`com.mitv.tvhome`
- 坑：🔴 澎湃 OS TV 的包名清单本项目没有独立核验过。上一代（MIUI TV / MIUI 13 TV）的 junk 清单只能当候选，执行前必须先 `adb shell pm list packages` 拉一份真实清单逐个对照，对不上的直接跳过。这是本品牌当前最大的空白。
- 坑：电视端澎湃 OS 3 正式版从 2026-01-31 起分批推送（小米电视 S Mini LED、REDMI MAX 2025、REDMI A Pro、REDMI A 2025 等），澎湃 OS 4 Beta 从 2026-08-14 起在小米电视 S Pro Mini LED 2026 系列开推。也就是说这一代机型的系统还在滚动更新，一次 OTA 就可能把禁用状态改回去或改掉菜单路径——做完精简后，若发现广告回来了，先查是不是刚升过级。
- 坑：找客服永久关开机广告这条路在这一代依然有效且是零风险首选：小米商城 App 转人工客服或打售后热线，提供 设置 → 关于 → 网络信息 里的以太网 MAC、WiFi MAC、蓝牙 MAC，约 2 个工作日后台生效。针对 Redmi A Pro 的实测记录指出这不是「遥控器一键关」，而是一次 10 分钟以上的申请流程。
- 坑：动 ADB 之前先把免费的开关关掉：系统设置里有「系统工具广告」总开关，各内容入口还有各自的「个性化推荐」开关，关掉能去掉一批系统级广告位。

### 【海外】Mi TV / Xiaomi TV · PatchWall on Android TV（Mi TV 4A/4X/4S/5X、Mi TV P1 / Q1 / A2）（2017–2023，android Android 8–11 区间（有实测记录的 MiTV4I 为 Android 9））

- 认：开机后除了 Android TV 原生桌面，还有一层 PatchWall 内容墙，遥控器上有独立的「PatchWall」实体键（以及 Netflix / Prime Video / Xiaomi TV+ 快捷键、完整数字键）——看到 PatchWall 键基本可确认是海外版，国内机型没有这个键。系统语言/商店是 Google Play，不是小米应用商店。
- 值不值：机龄 3–8 年，1–2GB 运存居多。海外版没有国内的开机广告，但 PatchWall 桌面本身塞满了内容推荐行（Xiaomi TV+、JioHotstar、Prime Video 卡片），一位用户的原话是桌面「表现得像块广告牌」。精简收益主要在流畅度和桌面清净度。

**Mi TV / Mi Box 海外版，小米官方英文支持页给的标准路径** · 证据：官方
1. Settings → About → 选中 product model（产品型号），按 OK 键连点 7 次
2. 出现「You are now a developer」后返回 Settings → Account & Security
3. 把「ADB debug」设为 Allow（官方原文：After turning on developer options, Setting >> account and security will display ADB debug option, select allow）
4. 电脑 adb connect <TV IP>:5555，在电视上确认授权弹窗
- 授权弹窗：有；端口 5555
- ⚠ 官方原文特别说明：因为电视/盒子功能与手机不同，这一代海外机型的「Developer options」里其实只暴露 ADB debug 这一项，不要指望看到手机上那一整页开关。
- 来源：[How to turn on the Mi TV and Mi Box developer options? How to turn on ADB debug?（小米全球官方支持页）](https://www.mi.com/global/support/article/KA-06513) 
- 来源：[同一篇官方支持文章的英国站版本](https://www.mi.com/uk/support/article/KA-06513) 

**走原生 Android TV 菜单的海外机型（Settings → System → About）** · 证据：多源
1. Settings（全部设置）→ System → About
2. 找到 Build / Android TV OS build，按 OK 连点 7 次，直到出现「You are now a developer」
3. 返回 System → Developer options
4. 打开 USB debugging（如果只用网线/WiFi，则打开 Network debugging / ADB debugging）
5. 回到主界面一次（Google 官方文档强调必须返回主屏幕设置才生效）
6. 在 Settings → Network → Wi-Fi → 已连接网络 → Status info 里查 IP，然后 adb connect <IP>:5555 并在电视上确认授权
- 授权弹窗：有；端口 5555
- ⚠ 🔴 Google 官方文档明确：零售版 Android TV 设备默认不监听 ADB 端口，开发机才自动监听。所以部分海外机型必须先用 USB 线连一次把端口初始化出来，之后才能纯走网络 adb connect。国内机型不受此限（小米官方文档写明默认支持网络 adb 调试）。
- 来源：[Debugging（Google 官方 Android TV 调试文档：Device → About → Build 连点、Developer options → USB debugging、Status info 查 IP）](https://developers.google.com/cast/docs/android_tv_receiver/debugging) 
- 来源：[XIAOMI Mi TV Box 3S - How to Enable Developer Options（HardReset.Info，逐步演示 Quick Settings → All settings → System → About → Android TV OS build ×7）](https://www.youtube.com/watch?v=dvPZcStI_Pk) 2026-02-16
- 来源：[Android TV Debugging via ADB Device NOT FOUND（回答者用 Mi TV 实测：除 USB debugging 外还要打开 MiTv Adb 选项）](https://stackoverflow.com/questions/55290963/android-tv-debugging-via-adb-device-not-found) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（海外 Android TV 机型上两种方式都有实测：一位用户用 `pm disable-user --user 0 <pkg>` 批量禁用后用 `pm list packages -d` 核对结果；另一位在 MiTV4I（Android 9）上用 `pm uninstall --user 0` 完成清理。）
- 禁停（2）：`com.mitv.tvhome.atv`、`com.xiaomi.mitv.res`
- 可停（8）：`com.mitv.tvhome.mitvplus`、`com.mitv.tvhome.michannel`、`com.miui.tv.analytics`、`com.xiaomi.android.tvsetup.partnercustomizer`、`com.xm.webcontent`、`com.mitv.dream`、`com.google.android.leanbacklauncher.recommendations`、`fusion.android.tv.demo`
- 看你用不用（6）：`com.xiaomi.mitv.updateservice`、`com.xiaomi.mitv.smartshare`、`com.xiaomi.amazon.alexa`、`com.netflix.ninja`、`com.amazon.amazonvideo.livingroom`、`com.google.android.tts`
- 坑：🔴 海外版没有国内那套开机广告，所以「找客服关广告」这条路不适用；海外用户的痛点是 PatchWall 桌面的内容推荐行。一位 MiTV4I（Android 9）用户的实测结论很重要：把 Xiaomi TV+、Prime Video 行背后的 App 全删掉之后，桌面本身仍然会渲染自己的推荐行和赞助卡片——真正的解法是换一个极简第三方桌面，而不是逐个删内容源 App。
- 坑：海外 Android TV 有一批「名字看着没用、其实挂着信号源/调谐器/Play 服务/桌面」的包。那位 MiTV4I 用户特别提醒：因为家里用同轴线看 Free Dish，他刻意没碰 tuner 和 Live TV 相关的包。随手删包是让电视变得更难用的可靠办法。
- 坑：做完精简记得回去把 Developer options 里的 USB / 网络调试关掉。开着网络 ADB 等于电视在局域网上一直监听调试端口，短时间维护可以，不该长期保持。

### 【海外】Xiaomi TV A / A Pro、Xiaomi TV Box S、TV Stick · Google TV（2022–2026，android Google TV（Android 11 / 12 及以后）；Xiaomi TV Box S 2nd Gen 为 MDZ-28-AA，3rd Gen 为 MDZ-32-AA（2025））

- 认：桌面是 Google TV 的「For you」大横幅样式（不是 Android TV 的横排 App 行），设置里出现「Device Preferences / 设备偏好设置」这一层；遥控器有 Google Assistant 键。Xiaomi Smart TV A 系列是 Google TV 与 PatchWall 双界面并存（官方以 PatchWall Plus 形式保留），所以看到 Google TV 桌面不代表没有 PatchWall。
- 值不值：机龄 0–4 年，系统较新、本身不算卡。Xiaomi TV A Pro 2025 被评测者称为「终于没广告了」，所以这一档的动机主要是去掉 Google TV 桌面的赞助推荐行和预装流媒体，而不是救卡顿。如果只是嫌桌面乱，先在 Google TV 设置里关掉个性化推荐，再考虑换桌面。

**Google TV 界面的小米电视与盒子（Xiaomi TV A Pro、TV Box S、TV Stick）** · 证据：多源
1. 主界面 → 设置（齿轮）→ 系统 System（部分机型路径为 设置 → 设备偏好设置 Device Preferences）
2. 进入 关于 About
3. 找到「Android TV OS build」或「构建号 / Build」，按 OK 键连点 7 次，直到出现「You are now a developer」
4. 返回上一级 System / 设备偏好设置 → 开发者选项 Developer options
5. 打开「USB 调试 USB debugging」；只走网络的打开「网络调试 / ADB 调试」
6. 在 设置 → 网络 → 已连接的 Wi-Fi → Status info 里查 IP，电脑执行 adb connect <IP>:5555，在电视上确认授权弹窗（勾「Always allow from this computer」）
- 授权弹窗：有；端口 5555
- ⚠ Android 11 以上的原生「无线调试」是带配对码的（Settings → Developer options → Wireless debugging → Pair device with pairing code，端口随机不是 5555）。本次调研没有找到小米这批 Google TV 机型上「无线调试配对码」界面的确认记录，所以 wireless_pairing 留空；实操时先按传统 5555 试，界面里如果出现 Wireless debugging 就按配对码流程走。
- 来源：[XIAOMI Mi TV Box 3S - How to Enable Developer Options（HardReset.Info 逐步演示，含完整字幕）](https://www.youtube.com/watch?v=dvPZcStI_Pk) 2026-02-16
- 来源：[XIAOMI TV A Pro 32 (2026) - 开启开发者选项与ADB 网络调试（章节：设置→设备偏好设置/关于→连点构建号→开发者选项→打开ADB网络调试）](https://www.youtube.com/watch?v=ZDMuV7nq7IQ) 2026-07-05
- 来源：[Debugging（Google 官方 Android TV / Google TV 调试文档）](https://developers.google.com/cast/docs/android_tv_receiver/debugging) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（Xiaomi Smart TV Box S (2nd Gen) 的公开 debloat 清单整篇用 `pm uninstall -k --user 0`；XDA 上救砖的用户则是用 ADB 把被禁的 Google TV 桌面 `pm enable` 回来，说明 disable/enable 这条路在 Google TV 机型上双向可用。）
- 可停（4）：`com.mitv.tvhome.mitvplus`、`com.miui.tv.analytics`、`com.xiaomi.android.tvsetup.partnercustomizer`、`com.google.android.leanbacklauncher.recommendations`
- 看你用不用（3）：`com.mitv.tvhome.atv`、`com.xiaomi.mitv.updateservice`、`com.google.android.videos`
- 坑：🔴 具体机型的变砖案例：Xiaomi TV Box S 2nd Gen（MDZ-28-AA）。用户装了 Projectivy Launcher 之后用 ADB TV 把 Google TV 系统桌面禁掉，结果开机黑屏，遥控器完全没反应——这就是「部分变砖」。救回方式：从手机端的 ADB TV App 里列出设备上的应用，打开 ADB TV（它本身装在盒子上），用它把 Google TV 系统桌面重新 enable 回来。Xiaomi TV Box S 3rd Gen（MDZ-32-AA）的同类事故里，用户是接 USB 键盘按 Alt+PrintScreen+i 调出设置界面，再进开发者选项打开 ADB 调试，然后用电脑 ADB 把 Google TV 桌面启用回来。注意：Google TV 桌面是禁用不是卸载，所以永远能用同样的方式启用回来。
- 坑：换第三方桌面的正确顺序（XDA 那位用户吃过亏之后总结的）：装好 Projectivy 等第三方桌面 → 在无障碍设置里给它开启自启权限 → 确认重启后能自动进第三方桌面 → 最后才禁用 Google TV 桌面。顺序反了就是黑屏。
- 坑：Google 官方文档提醒：零售版 Android TV / Google TV 设备默认不监听 ADB 网络端口，只有开发机才自动监听。海外机型如果 adb connect 一直连不上，先用 USB 线连一次把端口初始化出来。
- 坑：社区有针对 Xiaomi TV Box S 刷 boot.img、进 recovery 的讨论（也有人因此彻底刷砖），本项目不采用、不记录刷机方法。

**待验证 · 欢迎投稿**

- 国内小米/Redmi 电视是否在某个版本引入了 Android 11+ 那种带配对码的「无线调试」独立界面？本次查到的全部国内教程（含 2025-06、2026 年的）都还是「账号与安全 → ADB 调试」+ adb connect 5555 的传统模式，小米官方开发者文档也只写「默认支持网络 adb 调试」，没有配对码相关说明。
- 海外 Google TV 机型（Xiaomi TV A Pro 2025/2026、TV Box S 2nd/3rd Gen）的开发者选项里到底有没有原生「Wireless debugging + 配对码」项？教程视频只演示到「ADB 网络调试」，没有出现配对码界面的画面证据。
- 澎湃 OS（HyperOS）TV 的系统应用包名清单没有独立核验。当前 junk / must_keep 清单全部来自 MIUI TV 与 MIUI 13 TV 时代的实测，澎湃 OS 上包名是否改名、是否新增广告组件未知——这是本品牌最大的空白，也是给用户执行前必须先 pm list packages 对照的原因。
- 澎湃 OS TV 上 `pm uninstall --user 0` 是否仍有「重启后被守护进程装回来」的现象，以及 `pm hide` 在电视端是否可用，均无实测记录。
- 电视端「账号与安全 → ADB 调试」是否像手机端澎湃 OS 那样强制要求联网并登录小米账号？目前只有手机端 MIUI 2016 年的官方公告和第三方文章可类推，没有电视端专属证据。
- MIUI for TV 2.0 的确切发布年份与首发机型未找到权威资料，只确认了 1.0（2013）和 3.0（2020-09）两个节点；各代 MIUI TV 与 Android 基线版本的官方对照表也始终没有公开。
- 小米电视 S Pro Mini LED 2026 系列上的澎湃 OS 4（2026-08 起 Beta）是否改动了开发者模式或 ADB 菜单路径，尚无任何教程或用户报告。
- 海外 Google TV 机型上 Google TV 系统桌面的确切包名未从权威来源取得（XDA / Reddit 的帖子都只写「Google TV launcher」而没给包名），所以本文件没有把它写进 must_keep 的包名清单，只在 pitfalls 里描述。执行时应现场用 pm list packages 取真实包名。
- 小米/Redmi 电视棒、盒子与整机电视在包名清单上的具体差异有多大（国内侧）未做系统比对；本文件的国内包名证据主要来自整机电视。

## 长虹 · 部分适用

能做，但要先看遥控器：有实体数字键的老机走「0816 工厂菜单」，取消数字键的中期机型走「情景模式→上右右→软键盘 0816」，2020 年后的 Android 9 机型走「连点版本号 7 次」。长虹官方那两篇 ADB 教程正文被社区设成「回复可见」，抓不到，所以工厂菜单里 ADB 开关究竟在第几项至今无可引用来源。

**怎么认机型**

- remote_has_digits：这是长虹最重要的分岔口：2016 年前后的遥控器有一排实体数字键 0-9，走数字键法；CHiQ2/CHiQ3 之后的遥控器取消了实体数字键、改成带语音键的极简遥控，必须走软键盘法或连点法
- system_name_shown_at：设置 → 关于本机 / 关于电视 / 系统信息
- about_page_fields：Android 版本；软件版本号；机型；SN 号
- model_naming_rule：现行型号形如「尺寸数字 + 系列字母 + 数字 + 代号字母」，开头两位数字是英寸（65D8K = 65 英寸）。Q 系（Q1N/Q2E/Q2F/Q3T/Q5K/Q6K/Q8K/Q9K/Q10Air）是 CHiQ 启客高端线，D 系（D2P/D3P/D4PS/D5S/D6H/D7F/D8K/D55H/D55S）是走量线，另有 U/A/F/T 系。再往前的 A2000/B3000/C5000 这种「单字母 + 四位数字」是 2014-2016 的第一代智能机型。长虹官方未公布完整命名对照表，字母的确切含义不要硬套。
- sticker_location：机身背面铭牌；包装纸箱外侧的白色条码贴也印型号与 SN。长虹官方给了三条查型号路径：外包装条码、系统信息页、工厂模式里的索引项
- year_ranges：2014-2016 第一代智能（50Q1N 为 Android 4.2）；2016-2019 CHiQ2/CHiQ3 及 Q2/Q3 系，遥控器取消数字键；2020 年前后进入 Android 9（长虹官网 65D8K 规格页写明 Android 9.0/64 位电视系统）；2022 年后 D55S 等走 2GB+64GB

### 第一代智能电视 / 实体数字键遥控机型（A/B/C 系与早期 Q 系）（约 2014-2016，android Android 4.2 起（百度百科 50Q1N 条目记为 Android 4.2）；后续机型到 Android 5/6 一代）

- 认：遥控器上有一排实体数字键 0-9；关于页的 Android 版本是 4.x-6.x；桌面是长虹早期的方块磁贴 UI，不是后来的横向卡片流
- 值不值：机龄 9 年以上，这批机器普遍 1GB 内存 / 8GB 存储，卡是硬件层面的卡，ADB 精简能回一点但幅度有限；广告主要是开机 15 秒贴片。综合判断：想看直播装软件的话，外接一个盒子比折腾系统划算得多。

**遥控器带实体数字键（或有虚拟数字键盘）的机型** · 证据：多源
1. 按遥控器「节目源」键，切到任意一个信源（如 HDMI1）
2. 按「菜单」键，左侧弹出菜单栏后不要再按方向键
3. 依次按数字键 0、8、1、6，进入工厂/工程菜单
4. 部分机型的写法是：按「节目源」弹出输入选择菜单后，趁菜单还没消失，依次按 0、8、1、6
5. 在工厂菜单里找到 ADB / USB 调试开关并置为「开」
- 端口 5555
- ⚠ 只改 ADB / USB 调试这一项。工厂菜单里同屏还有屏参数、老化模式、SSC 电磁兼容测试、总线设置等，长虹官方原文的警告是「一不小心造成硬件不可恢复损毁」——除 ADB 外一项都不要动，也不要为了看参数随手按确认键。进去之后建议先拍张照记下原值。
- 来源：[长虹Q3系列工程模式详解（长虹官网）](https://www.changhong.com/fw/cjwt/czzd/ds_1024/201607/t20160731_55650.html) 2016-09-22
- 来源：[长虹智能电视进工厂模式方法汇总（遥控器有无数字按键均可）](https://blog.csdn.net/u013463707/article/details/109057526) 2020-10-13
- 来源：[长虹智能电视进入工厂模式方法（大全）](https://www.4k123.com/archiver/tid-7130.html) 
- 来源：[[教程] 长虹电视进入工程菜单教程（ZNDS）](https://www.znds.com/tv-515774-1-1.html) 
- 来源：[长虹液晶 ZLM50H-iS 机芯升级方法，附工厂模式进入方式](https://blog.csdn.net/qq_18625655/article/details/135381153) 2024-01-04
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（没有任何长虹专属的 pm 命令成功或失败记录。第一轮找到的唯一一条网友说法是「长虹内置应用要 root 才能删」，与其他品牌普遍「免 root 可 disable-user」的经验冲突，且只有单帖，本轮不采信、一律记 unknown。）
- 坑：长虹官方那篇专门讲 ADB 的《玩机必看！小C教你开启adb的秘籍！》，正文整段是社区「游客需回复才可见」的隐藏内容——本轮再次抓取确认，页面上只剩前言和落款，具体步骤一个字都没有。所以工厂菜单里 ADB 开关在第几个索引项，目前没有可引用的权威来源。

### CHiQ2 / CHiQ3 及之后取消实体数字键的机型（Q 系、D 系语音遥控）（约 2016-2019，android 未确认（长虹未公开逐机型的 Android 版本，官网规格页要逐款查））

- 认：遥控器没有数字键，只有方向圈、语音键和几个功能键；按【菜单】能看到「情景模式」这一栏，里面有「标准模式/柔和模式」等选项——能看到「情景模式」就是走这一路；机芯号形如 ZLS59GiQ2、ZLM60HiS、ZLM61Hi，在工厂菜单里能看到
- 值不值：机龄 6-9 年，多为 1-2GB 内存；开机广告和推荐位已经明显变多，精简收益比上一代高。先看关于页的可用内存：低于 2GB 的机器做完精简体感提升最明显。

**取消实体数字键、菜单里有「情景模式」的机型** · 证据：多源
1. 按遥控器「菜单」键
2. 把焦点移到「情景模式」下的「标准模式」这一项上（停在上面，不要按确定）
3. 依次按【上】【右】【右】，屏幕弹出数字软键盘
4. 输入 0816，进入工厂菜单
5. 在工厂菜单里找到 ADB / USB 调试开关并置为「开」
- 端口 5555
- ⚠ 只改 ADB / USB 调试这一项，其余参数不动（同上一代的工厂菜单警告）。焦点必须停在「标准模式」上再按方向键，按错顺序不会有任何提示，退出菜单重来即可，不要连按。
- 来源：[长虹智能电视进工厂模式方法汇总（遥控器有无数字按键均可）](https://blog.csdn.net/u013463707/article/details/109057526) 2020-10-13
- 来源：[长虹液晶电视机如何进入工厂模式（工程模式）](https://www.ifix.icu/post/chang-hong-ye-jing-dian-shi-ji-ru-he-jin-ru-gong-chang-mo-shi%EF%BC%88gong-cheng-mo-shi%EF%BC%89.html) 2023-04-04
- 来源：[长虹智能电视 ZLM61Hi 机芯，附进维修模式方法](https://blog.csdn.net/qq_18625655/article/details/135398546) 2024-01-04
- 来源：[长虹智能电视 ZLM60HiS 机芯，附进维修模式方法](https://blog.csdn.net/qq_18625655/article/details/135397597) 2024-01-04

**无数字键遥控、且上面的软键盘法进不去的机型（信源法，待核实）** · 证据：单源
1. 按遥控器「信源」键，进入任意一个信源
2. 按视频教程演示的组合键序列进入隐藏/工程菜单
3. 在其中开启 ADB
- 端口 5555
- ⚠ 来源是短视频，本轮没有拿到逐键的文字序列，只确认了「从信源界面起手」这个前提。当软键盘法失败时作为第二选择尝试，不要作为首选步骤照抄。
- 来源：[长虹电视开启adb教程（抖音，演示无数字键遥控的开法）](https://www.douyin.com/shipin/7297561401277827123) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（同上，无长虹专属证据。）
- 坑：工厂菜单密码不是全系 0816。长虹 ZLS59GiD 机芯的官方升级说明里，同一套「设置键→情景模式→标准模式→上、右、右→小键盘」的操作，输入的是 0818。所以 0816 输进去没反应时，先试 0818，不要判定为「这台机器不支持」。哪些机芯用哪个码没有完整对照表。
- 坑：长虹官方明确说过：取消实体数字键之后，旧的「菜单 + 0816」按法在新机型上做不出来，不要买万能遥控器绕路之前先确认自己是不是该走软键盘法。

### Android 9 及以后机型（2020 起的 D 系 / Q 系新款）（2020 至今，android Android 9 起（长虹官网 65D8K 规格页写明「Android 9.0 / 64 位电视系统」））

- 认：设置 → 关于本机 里能直接看到 Android 版本为 9 或更高；桌面是横向大卡片流 + 内容推荐位；自带应用商店叫「欢视商店」（长虹与 TCL 共用欢网科技这套商店），系统里通常还能搜到「欢视助手」
- 值不值：机龄 1-6 年，2GB+64GB 起步（85D55S 为 2+64GB）。这代的广告和推荐位最密，也是精简收益最高的一代；但也是长虹收紧最狠的一代，能不能开出开发者选项因机型而异，先花两分钟试连点法，不成就走下面的当贝桌面旁路，别硬啃。

**Android 9+ 且开发者选项未被屏蔽的机型** · 证据：多源
1. 按遥控器「菜单」键 → 选择「设置」
2. 进入「关于本机」或「关于电视」
3. 连续点击「版本号」7 次，出现「您已成为开发者」类提示
4. 返回设置，进入「开发者选项」
5. 打开「USB 调试」（部分机型叫「ADB 调试」）
- 端口 5555
- ⚠ 这条是零风险路径（走的是安卓标准开发者选项，不进工厂菜单），先试它。点满 7 次没有任何提示，说明这台机器的开发者选项被屏蔽了，换下一条，不要反复点。
- 来源：[品牌电视ADB开启方法（异新网）](https://dnew.cc/index.php/archives/209.html) 2025-12-17
- 来源：[智能电视隐藏设置：ADB开关这样开，告别卡顿与广告](https://post.smzdm.com/p/a4qlqmzl) 2026-03-12
- 来源：[长虹液晶 60q3t 如何能进入 adb 模式？](https://ask.csdn.net/questions/8064592) 2024-02-20

**系统里找不到开发者选项、但还能装第三方桌面的机型** · 证据：单源
1. 在电视上安装当贝桌面（第三方 TV 桌面）
2. 从当贝桌面进入它自带的「系统设置」
3. 在其中打开开发者模式 / USB 调试
- 端口 5555
- ⚠ 前提是能先装上第三方 App；同一帖的跟帖里有人反映「调试开了、TV 助手能连上，但读不到系统软件列表」——能连上不等于能操作系统应用，先验证 adb shell pm list packages 有输出再往下做。
- 来源：[长虹电视ADB怎么打开？（ZNDS 长虹版）](https://www.znds.com/tv-1205266-1-1.html) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（无长虹专属证据。ZNDS 的长虹去广告教程用的是 adb uninstall（不带 --user 0），没有验证过 pm disable-user 在长虹上的行为。）
- 坑：长虹 Android 9 之后的部分机型，系统直接禁止 U 盘安装第三方软件，同时也找不到开发者模式入口。求助帖里有人绕开的办法是从自带应用商店装「欢视助手」，用它的「应用安装助手」+ 手机微信扫码来装软件——这是绕开 ADB 的旁路，不是开 ADB。
- 坑：不要照抄任何教程里的示例包名。ZNDS 那篇长虹去广告教程举的例子是 com.tianci.ad，但它自己也写明「不同型号电视的广告模块名称可能不同」——必须在本机装一个「包名查看」类 App 实际列一遍，记下名字里带「广告」、包名里带 ad 的那些再动手。
- 坑：零风险替代动作：长虹的开机 15 秒广告可以直接联系官方去掉——打售后电话或电商平台人工客服，要求永久取消开机广告。代价是取消后开机会先黑屏 15 秒再出画面。完全不想碰 ADB 的人先用这条。

**待验证 · 欢迎投稿**

- 工厂菜单里 ADB 开关具体在第几个索引项、叫什么名字——长虹官网两篇相关帖（《小C教你开启adb的秘籍》《长虹Q3系列工程模式详解》）的关键正文都是社区「回复可见」的隐藏内容，本轮换 B 站、抖音、CSDN、ZNDS、值得买、异新网多路来源，都只查到「进工厂菜单」这一步，没有一条写出 ADB 开关的确切位置。需要人工登录 bbs.changhong.com 回帖后查看，或找到该帖的镜像转载。
- 工厂菜单密码在各机芯上不统一（ZLS59GiD 用 0818，其余多数用 0816），没有机芯→密码的完整对照表。
- 长虹开启 ADB 后是否弹授权确认框、是否支持 Android 11 式的无线调试配对码、端口是否为默认 5555——三项全部零证据，表里的 5555 是安卓默认值而非长虹专属证据。
- pm disable-user / pm hide / pm uninstall --user 0 在长虹上是否可用，各系列均无证据。第一轮唯一一条「需 root 才能删」的网友说法与其他品牌经验冲突且是孤证，本轮未采信。
- 长虹预装应用的包名一条都没有可引用来源：桌面、信号源、遥控、输入法、设置、工厂应用（must_keep）与广告/推荐/上报（junk）全部空白，欢视商店/欢视助手的包名同样未查到。需要一台实机跑 pm list packages 才能补。
- 「无数字键遥控 + 从信源界面起手」那条入口只有一个短视频来源，逐键序列未拿到文字版。
- 2020 年后的机型是否有 Google TV 认证版本（海外用 CHiQ 品牌）在国内销售、其入口是否走标准安卓路径，未确认。
- 社区有 root / 刷机讨论（长虹官方自己也发过刷机包帖），本项目不采用。

## Amazon Fire TV（全系） · 部分适用

Fire OS 5～8（底层是 Android 5.1/7.1/9/11）的机器全部能开 ADB，入口统一在「设置 → My Fire TV → About → 点设备名 7 次」，端口 5555；但 2025 年起新出的 Fire TV Stick 改用 Vega OS，没有开发者选项也不能侧载，完全做不了。老机器（Fire OS 5/6、1～2 GB 内存）收益最大。

**怎么认机型**

- system_name_shown_at：设置（齿轮）→ My Fire TV（较新系统叫 Device & Software）→ About。第一行是设备名（如 Fire TV Stick 4K Max），下面能看到 Fire OS 版本号；About → Network 里能看到本机 IP 地址，adb connect 要用它。
- about_page_fields：设备名（Fire TV Stick / Fire TV Stick 4K / Fire TV Cube / 电视品牌名）；Fire OS 版本（如 5.2.7.x / 6.2.x / 7.6.x / 8.1.x）；Software Version 内部版本号（如 PS7242、PS7704、RS8149）；Serial Number 序列号；Storage 存储占用；Network（IP 地址、MAC）
- model_naming_rule：Amazon 内部设备代号一律以 AFT 开头，adb 通了之后 `adb shell getprop ro.product.model` 能直接读出来。常见对应：AFTM=Stick 1 代(2014)、AFTB=Fire TV 盒子 1 代、AFTS=Fire TV 盒子 2 代(2015)、AFTT=Stick 2 代/Basic Edition(2016–2019)、AFTN=Fire TV 盒子 3 代(2017)、AFTRS=Element 4K(2017)、AFTA=Cube 1 代(2018)、AFTMM=Stick 4K 1 代(2018)、AFTSS=Stick Lite(2020)、AFTKA=Stick 4K Max 1 代(2021)、AFTKM=Stick 4K 2 代(2023)、AFTKRT=Stick 4K Max 2 代(2023)；Fire TV Edition 电视用另一套：AFTBAMR311/AFTEAMR311=Toshiba/Insignia HD(2018–2020)、AFTKMST12=Toshiba 4K(2018–2019)、AFTLE=Onida HD(2019)、AFTDCT31=Toshiba/Insignia 4K UHD(2020)、AFTHA004=Hisense/Toshiba 4K UHD(2022)、AFTMD001=TCL S4/Q6(2023)、AFTSHN02=TCL 32/40 吋(2023)、AFTKA002=Fire TV 2-Series(2023)。
- sticker_location：Stick 机身侧面、Cube 底部、电视后壳贴纸上印有型号，但最可靠的还是 About 页里的设备名 + Fire OS 版本，贴纸上多是零售型号不是 AFT 代号。
- year_ranges：2014 年（Fire TV 1 代 / Stick 1 代）至今。2025 年起新款 Stick（Fire TV Stick 4K Select、新 Fire TV Stick HD）改用 Vega OS，不再是 Android。

### Fire OS 5（Android 5.1 / API 22）（2014–2019（含 2019 年仍在售的 Stick 2 代），fireos Android 5.1 Lollipop（API 22））

- 认：About 页的 Fire OS 版本以 5. 开头（如 5.2.7.4、5.2.9.x）。桌面是老版深蓝灰横条 Launcher，顶部一排大分类标签。遥控器没有数字键、没有 Alexa 蓝圈按钮（Stick 1 代连语音键都没有）。
- 值不值：整个 Fire TV 系列里收益最大的一代：机器最老、内存最小（社区普遍称 Stick 2 代为 1 GB RAM / 8 GB 存储，本轮未取到 Amazon 官方规格页，见 open_questions），而且这代 Fire OS 还没封死 `pm uninstall -k --user 0`，能真正把包从当前用户下卸掉腾空间，不是只「停用」。三个信号全中：机龄 6 年以上 + 内存小 + 首页全是推荐位。

**Fire OS 5 全部机型** · 证据：官方
1. 主页 → 右侧「设置」（齿轮）→ My Fire TV → About
2. 光标停在第一行的设备名（如 Fire TV Stick）上，用遥控中间的确认键连点 7 次，屏幕提示「You are now a developer」（部分固件版本是在 About → Your TV 上按方向盘 D-pad 7 次）
3. 返回：设置 → My Fire TV → Developer Options
4. 打开 ADB Debugging
5. 打开 Apps from Unknown Sources（只装 apk 要它，纯 adb 命令不需要）
6. 在 设置 → My Fire TV → About → Network 里看本机 IP
7. 电脑上执行 `adb connect <IP>:5555`，电视上会弹出「Allow ADB debugging?」，用遥控选 Allow，并勾上 Always allow from this device
- 授权弹窗：有；端口 5555
- ⚠ ADB Debugging 打开后，同一局域网内任何人都能往这台机器装东西（电视上自己的警告文案就是这么写的）。做完事建议把 ADB Debugging 关回去。
- 来源：[Connect to Fire TV Through ADB (Fire TV) — Amazon 官方开发者文档](https://developer.amazon.com/docs/fire-tv/connecting-adb-to-device.html) 
- 来源：[Developer Tools Menu (Fire TV) — 官方，明确 adb connect IP:5555 端口固定 5555](https://developer.amazon.com/docs/fire-tv/developer-tools.html) 
- 来源：[Fire TV ADB 启用流程与授权弹窗（含 Always allow 勾选）](https://displayhub.us/decoders-firetv) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（这代机器上 `adb uninstall -k --user 0 <包名>` 与 `pm disable <包名>` 社区教程都在用且有成功回报；`-k` 保留数据、可用 `pm install-existing <包名>` 恢复。注意 Fire OS 5 上早期教程用的是不带 `--user 0` 的 `pm disable`，那是需要 shell 权限的整机停用，现在新系统上已被受保护包机制拦住。）
- 禁停（5）：`com.amazon.tv.launcher`、`com.amazon.tv.settings.core`、`com.amazon.tv.ime`、`com.amazon.fireinputdevices`、`com.amazon.device.controllermanager`
- 可停（5）：`com.amazon.tv.legal.notices`、`com.amazon.tv.oobe`、`com.amazon.tmm.tutorial`、`com.amazon.storm.lightning.tutorial`、`com.amazon.tv.releasenotes`
- 看你用不用（3）：`com.amazon.venezia`、`com.amazon.avod`、`com.amazon.wcast.sink`
- 坑：这代机器已经停在 Android 5.1，很多新版流媒体 App 直接装不上或装上闪退；清理完也换不来新 App 的兼容性，只能换来更顺的老 App 体验。

### Fire OS 6（Android 7.1 / API 25）（2017–2020，fireos Android 7.1.2 Nougat（API 25））

- 认：About 页的 Fire OS 版本以 6. 开头（如 6.2.7.x、6.7.1.1）。这代开始遥控器上普遍有 Alexa 语音键（蓝圈）。Fire TV Edition 电视（东芝 / Insignia 那批）在 About 页会显示电视品牌名而不是 Fire TV Stick。
- 值不值：收益仍然很大：机龄 6–8 年，首页已经是 Amazon 推荐位 + 自动播预告片的形态。注意 Amazon 后来把部分 Fire OS 6 设备直接推到了 Fire OS 7，先看 About 页实际版本号再决定按哪一代做。

**Fire OS 6 全部机型（含 Fire TV Edition 电视）** · 证据：官方
1. 设置 → My Fire TV → About
2. 光标停在设备名那一行，确认键连点 7 次，出现「You are now a developer」
3. 返回：设置 → My Fire TV → Developer Options
4. 打开 ADB Debugging；要侧载 apk 再打开 Apps from Unknown Sources
5. About → Network 里看 IP，电脑执行 `adb connect <IP>:5555`
6. 电视上弹「Allow ADB debugging?」→ Allow（勾 Always allow from this device）
- 授权弹窗：有；端口 5555
- ⚠ Fire TV Cube 和 Fire TV 盒子有 USB 口，Developer Options 里还会多一个 USB Debugging。官方文档提醒：USB Debugging 打开后 USB 口不能再当外接存储或接键鼠用，要恢复就把它关掉。
- 来源：[Connect to Fire TV Through ADB — Amazon 官方](https://developer.amazon.com/docs/fire-tv/connecting-adb-to-device.html) 
- 来源：[Identify Fire TV Devices — 官方设备代号 / Fire OS 对照表](https://developer.amazon.com/docs/device-specs/identify-fire-tv-devices.html) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（这代还在社区 `pm uninstall -k --user 0` 教程的覆盖范围内。但从 Fire OS 7.2.4.2 前后开始，OTA 相关包（com.amazon.device.software.ota / .override）已经变成受保护包，命令会报 SecurityException — Fire OS 6 末期升上去的机器会先撞到这个。）
- 禁停（4）：`com.amazon.tv.launcher`、`com.amazon.tv.settings.v2`、`com.amazon.franktvinput`、`com.amazon.tv.ime`
- 可停（5）：`com.amazon.tv.forcedotaupdater.v2`、`com.amazon.tv.easyupgrade`、`com.amazon.shoptv.client`、`com.amazon.tv.legal.notices`、`com.amazon.tahoe`
- 看你用不用（4）：`com.amazon.venezia`、`com.amazon.avod`、`com.amazon.tv.alexadestination`、`com.amazon.wcast.sink`

### Fire OS 7（Android 9 / API 28）（2019–2023（至今仍是大量在用机器和 Fire TV 电视的主力版本），fireos Android 9 Pie（API 28））

- 认：About 页的 Fire OS 版本以 7. 开头（常见 7.2.x、7.6.x）。Amazon 自营电视（Omni / 4-Series）和 Hisense / TCL / 东芝的 Fire TV 电视都属于这一代：它们的设置入口写作「Device & Software」而不是「My Fire TV」，About 里第一行显示的是「Your TV」而不是 Fire TV Stick。遥控器带 Alexa 键，Omni 系列还有远场麦克风开关。
- 值不值：值得做，但要认清边界：这代开始 Amazon 把 OTA 和 metrics 设成受保护包，挡不住系统更新；能拿回来的是首页推荐位、自动播预告片、环境屏保和一堆遥测服务。Fire TV Stick Lite / 2-Series 这类 1 GB 级低配机型收益最明显；Omni QLED 这类电视主要是去广告位而不是救卡顿。

**Fire OS 7 的 Stick / Cube / 盒子** · 证据：官方
1. 设置 → My Fire TV → About
2. 光标停在设备名那一行，确认键连点 7 次 → 「You are now a developer」
3. 返回：设置 → My Fire TV → Developer Options
4. 打开 ADB Debugging（要装 apk 再打开 Apps from Unknown Sources；新固件上这一项叫「Install unknown apps」，要对具体的安装器 App 单独授权）
5. 设置 → My Fire TV → About → Network 看 IP
6. 电脑执行 `adb connect <IP>:5555`，电视上选 Allow
- 授权弹窗：有；端口 5555
- 来源：[Connect to Fire TV Through ADB — Amazon 官方](https://developer.amazon.com/docs/fire-tv/connecting-adb-to-device.html) 
- 来源：[Amazon Fire TV Sideloading Restrictions [2026 Guide]（记录了新固件把开关改名为 Install unknown apps）](https://www.kunalganglani.com/blog/fire-tv-sideloading-restrictions) 2026

**Fire TV Edition / Amazon 自营电视（Omni、4-Series、Hisense / TCL / 东芝 Fire TV）** · 证据：多源
1. 设置（齿轮）→ Device & Software（这批机器上没有 My Fire TV 这个字样）→ About
2. 光标停在「Your TV」那一行，确认键连点 7 次；屏幕下方会提示还差几次
3. 返回：设置 → Device & Software → Developer Options
4. 打开 ADB Debugging
5. About → Network 看 IP，电脑执行 `adb connect <IP>:5555`，电视上选 Allow
- 授权弹窗：有；端口 5555
- 来源：[Developer Tools Menu (Fire TV) — 官方明确「Settings > My Fire TV (or Device & Software) > About」两种菜单名](https://developer.amazon.com/docs/fire-tv/developer-tools.html) 
- 来源：[How to Locate Developer Options & Install Unknown Apps / ADB Debugging on Amazon Fire TV（YourSixTech，演示 Devices and Software 路径）](https://www.youtube.com/watch?v=8qnoMsP1Pgc) 2025-05-17
- 来源：[r/fireTV：Developer Options 路径确认](https://www.reddit.com/r/fireTV/comments/1najvul/developer_options) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=no（🔴 分两类：普通包用 `adb shell pm disable-user --user 0 <包名>` 有效且可用 `pm enable --user 0` 恢复；但 OTA、metrics、crashmanager 这几个被 Amazon 标成受保护包，命令直接抛 `java.lang.SecurityException: Cannot disable a protected package`，`pm uninstall -k --user 0` 返回 `DELETE_FAILED_INTERNAL_ERROR`。大量 7.2.4.2 用户在 troypoint 评论区复现了同样报错。挡系统更新只能在路由器 / DNS 层做，本项目不涉及。）
- 禁停（15）：`com.amazon.tv.launcher`、`com.amazon.firebat`、`com.amazon.tv.keypolicymanager`、`com.amazon.dcp`、`com.amazon.identity.auth.device.authorization`、`com.amazon.ale`、`com.amazon.tv.settings.core`、`com.amazon.tv.settings.v2`、`com.amazon.tv.ime`、`com.amazon.fireinputdevices`、`com.amazon.device.controllermanager`、`com.amazon.franktvinput`、`com.amazon.tv.routing`、`com.amazon.webview.chromium`、`com.android.systemui`
- 可停（34）：`com.amazon.tv.ftvambient`、`com.amazon.ftv.screensaver`、`com.amazon.sneakpeek`、`com.amazon.tv.acr`、`com.amazon.aca`、`com.amazon.hybridadidservice`、`com.amazon.ftvads.deeplinking`、`com.amazon.device.telemetry.emitter`、`com.amazon.wirelessmetrics.service`、`com.amazon.dp.logger`、`com.amazon.media.recommendations`、`com.amazon.shoptv.client`、`com.amazon.shoptv.firetv.client`、`com.amazon.minitv.android.app`、`com.amazon.gamehub`、`com.amazon.tv.website_launcher`、`com.amazon.bueller.photos`、`com.amazon.bueller.music`、`com.amazon.tv.forcedotaupdater.v2`、`com.amazon.tv.easyupgrade`、`com.amazon.tv.support`、`com.amazon.firetv.troubleshooting`、`com.amazon.tahoe`、`com.amazon.tv.legal.notices`、`com.amazon.ods.kindleconnect`、`com.amazon.tv.releasenotes`、`com.amazon.storm.lightning.tutorial`、`com.amazon.tmm.tutorial`、`com.amazon.tv.notificationcenter`、`com.amazon.uxnotification`、`com.amazon.logan`、`com.amazon.spiderpork`、`com.amazon.device.rdmapplication`、`com.amazon.wifilocker`
- 看你用不用（8）：`com.amazon.venezia`、`com.amazon.imp`、`com.amazon.ceviche`、`com.amazon.tv.alexaalerts`、`com.amazon.tv.alexadestination`、`com.amazon.wcast.sink`、`com.amazon.whisperlink.core.android`、`com.amazon.tv.livetv`
- 坑：🔴 挡不住系统更新。com.amazon.device.software.ota / .override 是受保护包，`pm disable-user` 直接抛 SecurityException，实机上 31 MB 照常在跑。社区做法是在路由器 / DNS 层拦，本项目不涉及。
- 坑：系统更新会把一部分被停用的包重新启用；出现异常想恢复出厂时，先把之前禁的包 `pm enable --user 0` 全部启回来再重置 — 有用户在禁用状态下直接恢复出厂导致开机循环。
- 坑：换第三方 Launcher（Projectivy / Wolf）时，「装好并设为默认」和「禁掉 Amazon 桌面」是两步。2025 年 12 月及以后的安全补丁把 com.amazon.tv.launcher 设成了受保护包，最后那步会 SecurityException — 前三步仍然有效，只是 Amazon 桌面会不时抢回 Home 键。
- 坑：🔴 别照抄通用 Android TV 清单。com.amazon.firebat 名字像「电池分析」，实机上是 Prime Video 本体；这类误杀直接打碎 Widevine 串流，且症状是「某个 App 打不开」，很难反查到是清理造成的。
- 坑：2024 年起 Amazon 对侧载持续收紧：先是被标记的包能装但一启动就被禁用，到 2026 年初升级为在安装阶段按包名直接拦截（提示 App installation blocked）。这与 ADB 清理本身无关，但会影响「清理完再装别的 App」这条路。

### Fire OS 8（Android 11 / API 30）（2023 至今，fireos Android 11（API 30））

- 认：About 页的 Fire OS 版本以 8. 开头（如 8.1.5.x、8.1.8.0）。首页是新版 Fire TV 界面，顶部一整条横幅推广位 + 待机进入「Ambient Experience」环境屏保轮播图片。
- 值不值：机器本身不慢（4K Max 2 代 16 GB 存储），做的收益主要在「去广告位 / 去遥测」而不是救卡顿：单是环境屏保一个包就实测常驻 104 MB。反过来，这代受保护包最多，挡更新、换桌面这两件事基本做不成，要先跟用户说清楚边界。

**Fire OS 8 全部机型** · 证据：官方
1. 设置 → My Fire TV（或 Device & Software）→ About
2. 光标停在设备名那一行，确认键连点 7 次 → 「You are now a developer」
3. 返回：设置 → My Fire TV → Developer Options
4. 打开 ADB Debugging
5. 要侧载 apk：Developer Options → Install unknown apps → 对具体的安装器 App（如 Downloader）单独打开
6. About → Network 看 IP，电脑执行 `adb connect <IP>:5555`，电视上选 Allow 并勾 Always allow
- 授权弹窗：有；端口 5555
- ⚠ Fire OS 8 仍是端口 5555 直连，不需要无线调试配对码 — 这一点和同期的 Google TV 设备不一样。
- 来源：[Connect to Fire TV Through ADB — Amazon 官方](https://developer.amazon.com/docs/fire-tv/connecting-adb-to-device.html) 
- 来源：[tv-tweak：Fire TV Stick 4K Max (AFTKRT) / Fire OS 8.1.8.0 实机验证，走的就是 adb 5555](https://github.com/26zl/tv-tweak) 
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=no（Fire OS 8.1.8.0 实机验证：普通包 `pm disable-user --user 0` 成功、`pm enable --user 0` 可回滚；受保护包（com.amazon.device.software.ota、.ota.override、com.amazon.client.metrics、com.amazon.device.metrics、com.amazon.device.crashmanager）报 `java.lang.SecurityException: Cannot disable a protected package`，`pm uninstall -k --user 0` 返回 `DELETE_FAILED_INTERNAL_ERROR`。）
- 禁停（20）：`com.amazon.tv.launcher`、`com.amazon.firebat`、`com.amazon.tv.keypolicymanager`、`com.amazon.ale`、`com.amazon.dcp`、`com.amazon.identity.auth.device.authorization`、`com.amazon.tv.settings.core`、`com.amazon.tv.settings.v2`、`com.amazon.tv.ime`、`com.amazon.fireinputdevices`、`com.amazon.device.controllermanager`、`com.amazon.tv.routing`、`com.amazon.tv.intentsupport`、`com.amazon.ssm`、`com.amazon.ssmsys`、`com.amazon.tcomm`、`com.amazon.tcomm.client`、`com.amazon.webview.chromium`、`com.android.systemui`、`com.android.providers.downloads`
- 可停（31）：`com.amazon.tv.ftvambient`、`com.amazon.ftv.screensaver`、`com.amazon.sneakpeek`、`com.amazon.tv.acr`、`com.amazon.aca`、`com.amazon.hybridadidservice`、`com.amazon.device.telemetry.emitter`、`com.amazon.wirelessmetrics.service`、`com.amazon.perfc`、`com.amazon.perfcollection`、`com.amazon.dp.logger`、`com.amazon.media.recommendations`、`com.amazon.shoptv.client`、`com.amazon.shoptv.firetv.client`、`com.amazon.minitv.android.app`、`com.amazon.gamehub`、`com.amazon.tv.website_launcher`、`com.amazon.bueller.photos`、`com.amazon.bueller.music`、`com.amazon.tv.forcedotaupdater.v2`、`com.amazon.tv.easyupgrade`、`com.amazon.tahoe`、`com.amazon.tv.legal.notices`、`com.amazon.tv.support`、`com.amazon.firetv.troubleshooting`、`com.amazon.tv.notificationcenter`、`com.amazon.uxnotification`、`com.amazon.systemnotices`、`com.amazon.ods.kindleconnect`、`com.amazon.audiohome`、`com.amazon.dummy.alarmclock`
- 看你用不用（6）：`com.amazon.imp`、`com.amazon.ceviche`、`com.amazon.tv.livetv`、`com.amazon.venezia`、`com.amazon.whisperlink.core.android`、`com.amazon.sharingservice.android.client.proxy`
- 坑：🔴 受保护包一组：OTA、OTA override、client.metrics、device.metrics、crashmanager。这五个在 Fire OS 8.1.8.0 上 disable-user 和 uninstall 都失败，且 OTA 包实测 31 MB 照常运行。承诺用户「能挡更新」是错的。
- 坑：Fire OS 8 不会再升到 Fire OS 16 — Amazon 已确认现有 Android 版 Fire TV Stick 不再升级 Android 版本，新的 Fire OS 只给智能电视。也就是说这批机器的软件形态基本定型了，做一次能管很久。

### Fire OS 14（Android 14）（2025 起（仅智能电视，不给 Stick），fireos Android 14（API 34））

- 认：About 页的 Fire OS 版本以 14. 开头。只出现在 2025 年及以后发布的 Fire TV 智能电视上，Stick 不会升到这一代。
- 值不值：新机器，性能不紧张；做的价值主要是关广告位和遥测。本轮没有拿到这一代的实机包清单和入口截图，动手前建议先在实机上 `pm list packages` 现抓一份，不要套用 Fire OS 7/8 的清单。

**Fire OS 14 智能电视（路径按 Fire OS 7/8 推断，未在实机核实）** · 证据：单源
1. 设置 → Device & Software → About
2. 光标停在「Your TV」那一行，确认键连点 7 次
3. 返回：设置 → Device & Software → Developer Options → ADB Debugging
4. About → Network 看 IP，`adb connect <IP>:5555`
- 端口 5555
- ⚠ 🔴 这一代的入口路径是从 Fire OS 7/8 推断的，本轮没有找到实机演示。Android 14 在原生 Android 上已经把无线调试和 USB 调试拆开（要配对码），Amazon 是否跟进不明 — 见 open_questions。
- 来源：[Fire OS Overview — 官方文档列出 Fire OS 14 / Fire OS 16 分支](https://developer.amazon.com/docs/fire-tv/fire-os-overview.html) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（本轮未取到 Fire OS 14 实机证据。）

### Vega OS（不适用）（2025 起，other 不是 Android — Amazon 自研的 Linux 系统）

- 认：About 页里看不到 Fire OS 版本号，也翻不出 Developer Options 这个菜单，任何地方都找不到「Apps from Unknown Sources / Install unknown apps」开关 — 这三条同时成立就是 Vega OS。2025 年及以后新买的入门款 Fire TV Stick 最可能是这种。
- 值不值：🔴 做不了，别试。Vega OS 不是 Android，没有开发者选项、没有 ADB、不支持侧载 — Amazon 明确说这是为「增强安全性」有意为之。要清理只能换设备。
- 命令能力：disable-user=no · hide=no · uninstall-user0=no（整个 Android 包管理体系不存在。）
- 坑：Amazon 已确认今后所有新款 Fire TV Stick 都用 Vega OS。也就是说「买新 Fire TV Stick 回来清理」这条路从 2025 年起就断了，二手老款反而是能做的那一批。

**待验证 · 欢迎投稿**

- Fire TV Cube 2 代（2019）和 3 代（2022）分别是 Fire OS 6 / 7 / 8 的哪一代？本轮在 Amazon 官方设备对照表里没有取到 Cube 2/3 代的明确行，不要凭印象填。
- Fire TV Stick 2 代（AFTT）的 RAM 和存储具体规格。社区普遍说 1 GB RAM / 8 GB 存储，本轮没有取到 Amazon 官方规格页面佐证，worth_it_hint 里已按「社区说法」标注。
- Fire OS 14 的开发者选项入口路径、是否改成 Android 14 原生的「无线调试 + 配对码」、以及实机包清单 — 全部缺实机证据。
- Fire OS 5 / 6 上 `pm hide` 是否可用？本轮所有来源用的都是 disable / disable-user / uninstall -k --user 0，没有一条用 pm hide。
- tv-tweak 的 alexa 层和 smarthome 层具体包含哪些包名？抓取时这两段被截断，只知道 alexa 层应用后语音遥控会失效。
- Amazon 自营电视（Omni QLED / 4-Series）上是否有 Stick 没有的独占包（远场麦克风、Alexa 免提模式、内置调谐器）？本轮没有实机 `pm list packages` 样本。
- 社区有 root / 刷机相关讨论（TWRP、Magisk、开发者版固件），本项目不采用，也不记录。

## 海尔 · 部分适用

先分清是「阿里 YunOS」还是「安卓」——海尔官网每款电视的规格参数页都直写「操作系统」这一栏，比在电视上翻菜单可靠。YunOS 那一批没有可用的 ADB 路线，直接走官方关广告；安卓那一批用「信源键 → 左左上右」进隐藏菜单开 ADB，有三个彼此独立的来源。

**怎么认机型**

- remote_has_digits：数字键有无都不影响；关键是遥控器上有没有「信源」（输入源 / Source）键——安卓机型的 ADB 入口就是从按这个键开始的
- system_name_shown_at：最可靠的不是电视本身，是 haier.com 上该型号页面的「规格参数 → 操作系统」字段：写「阿里YunOS」就是 YunOS 代，写「安卓系统」就是安卓代。电视端则看桌面长相（见各系列的 identify_hints）
- about_page_fields：操作系统（海尔官网规格参数页）；型号；Android 版本（仅安卓代有）
- model_naming_rule：老型号是「两个字母 + 尺寸 + 字母数字后缀」：LE 多见于全高清/普通 LED（LE42AL88A51、LE43AL88E81），LS 多见于 4K 超高清（LS65AL88A71、LS50AL88R81A2），LD 是早期模块化机（LD49U9000），LQ 亦见于 4K 机（LQ55AL88M92）。中段 AL88 后面的字母（A/E/M/R/T）大致随代次推进，在官网页面日期上能对上（A71 对 2018、T71 对 2019），但这只是观察到的规律，海尔没有公布对照表，判定年份请以官网该型号页面的发布日期为准。2024 年前后改成短型号：43H5、50A20、Q60D 这类「尺寸 + 单字母 + 数字」。海外/台湾线另用 LE75S8UX2、H65S900UX2 这种带 UX 尾缀的型号，走 Google TV，不在本条目的 cn 范围内。
- sticker_location：机身背面铭牌
- year_ranges：2014 模块化（LD49U9000）；2015-04 海尔阿里Ⅱ代；2016-11 阿里Ⅲ代；2017-09 阿里Ⅳ代。海尔官网 2018-05 的 LS65AL88A71 规格页仍写「阿里YunOS」，2019-10 的 65T75 介绍页仍提 YunOS，所以 YunOS 代至少延续到 2019；官网 2024 年的 43H5、50A20 规格页已写「安卓系统」。2019 到 2024 之间的确切切换年份本轮没查到。

### 海尔阿里电视（YunOS 代）（约 2014-2019，yunos 不适用——YunOS（阿里云 OS）不是标准 Android TV，没有安卓意义上的开发者选项）

- 认：海尔官网该型号规格页的「操作系统」写「阿里YunOS」；电视桌面是 YunOS for TV 的横向卡片/轮播式界面，不是标准安卓电视那种行列式排布；自带商店是阿里数娱体系而非安卓应用商店；型号多为 LE/LS/LQ + AL88 这一串
- 值不值：机龄 6-11 年，而且这一代本来就没有可用的 ADB 通道——花时间开 ADB 的期望收益基本为零。只做两件事：① 按下面 pitfalls 里的官方路径永久关掉开机广告（免费，不碰系统）；② 想装软件看直播就外接一个电视盒子，比折腾系统省事得多。
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（这一代进不去 ADB，pm 命令无从谈起，三项一律 unknown。）
- 坑：这一代没有找到任何可用的 ADB 开启路径。网友在知乎的原话是「没有任何办法」，教程类文章一律转向 U 盘侧装或第三方口令码安装工具，全都不经过 ADB。碰到这类机器，不要照搬通用安卓步骤反复试——按不出来是系统就没有，不是操作错了。
- 坑：替代动作（官方、免费、零风险，YunOS 和安卓机型都适用）：打海尔客服 400-6999999，按提示转投诉人工，说明要去除开机广告；客服会记录信息并派工单，售后 24 小时内回电，给两个选择——① 免费上门去除；② 电话指导：在海尔电视的应用商店搜装「广告助手」App，打开后屏幕上会显示一个 8 位校验码，把机器信息报给官方换取对应的码，输入即可永久去掉开机广告。这个校验码只能从官方拿。
- 坑：2015 年 11 月阿里 YunOS 因政策调整关停部分服务，大批用户已安装的第三方影视/直播软件一夜被强制卸载且装不回来。这是影响整个 YunOS 阵营（含海尔阿里系）的行业性事件，不是「没开 ADB」造成的，所以也不是开了 ADB 就能解决的。

### 海尔安卓电视（非 YunOS）（切换年份未确认；海尔官网 2024 年的机型规格页已写「安卓系统」，android 未确认——海尔官网规格页只写「安卓系统」，不标 Android 版本号）

- 认：海尔官网该型号规格页的「操作系统」写「安卓系统」；遥控器上有「信源」键；桌面是安卓电视式的行列桌面而非 YunOS 的横向卡片轮播；型号多为 2024 年后的短型号（43H5、50A20、Q60D）
- 值不值：机龄 0-5 年、2GB 以上内存的近年机型，精简收益明显（海尔开机广告一直是老用户的主要抱怨点）。一个例外：2026 年海尔自己推的「天悦零添加」Q60D 主打的就是没有开机广告，买这款就不用折腾。

**有「信源」键的海尔安卓机型** · 证据：多源
1. 先切到信源界面（按遥控器「信源」/ Source 键，停在信号源选择界面上）
2. 依次按【信源键】【左】【左】【上】【右】
3. 屏幕弹出隐藏菜单
4. 在隐藏菜单里把 ADB / USB 调试置为「开」
5. 电脑与电视连同一个局域网后 adb connect 电视IP:5555
- 端口 5555
- ⚠ 弹出的是隐藏菜单，不是完整工厂菜单——只碰 ADB / USB 调试这一项，菜单里若还有屏参数、老化、总线一类选项一律不动。按键必须依次按完，中途不要按返回或确定；按错没有任何提示，退出信源界面重新进来再按一遍即可，不要连着乱按。端口 5555 是安卓默认值，不是海尔专属证据。
- 来源：[Haier | QuickTVUI（电视应用开发框架的设备文档：打开 USB 调试教程）](https://quicktvui.com/zh-CN/guide/device/haier.html) 
- 来源：[智能电视隐藏设置：ADB开关这样开，告别卡顿与广告](https://post.smzdm.com/p/a4qlqmzl) 2026-03-12
- 来源：[海尔电视打开ADB教程！（B站）](https://www.bilibili.com/video/BV1wQzuYqENY/) 
- 来源：[海尔电视ADB打开方式（B站）](https://www.bilibili.com/video/BV1GT421i7Wn/) 

**上面那条按不出来时的变体记法** · 证据：单源
1. 进入信源界面
2. 依次按【信源键】【左】【左】【右】【右】
3. 在弹出的菜单里打开 ADB 开关
- 端口 5555
- ⚠ 同一个入口的另一种记法，三个来源里有两个写「左左上右」、这一个写「左左右右」，没有本机实测能判定哪个对、是否按机型分。「左左上右」失败时再试这条。
- 来源：[品牌电视ADB开启方法（异新网）](https://dnew.cc/index.php/archives/209.html) 2025-12-17
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（没有任何海尔专属的 pm disable-user / hide / uninstall --user 0 成功或失败记录，三项一律 unknown。）
- 坑：这个隐藏菜单入口不是所有机型都有。2022 年一位值得买作者本来就是打算用 ADB 删广告包的，原话是「海尔电视我半天没找到如何开启开发者选项」，最后改用 App 关广告了事。找不到就走官方关广告路径，别把时间耗在反复按组合键上。
- 坑：查资料时容易抄错品牌。网上的品牌汇总表常把海尔和 TCL/雷鸟并在同一行（异新网那篇的小标题就写成「TCL/雷鸟/海尔TCL电视/雷鸟电视」），那一段的前半句「打开我的应用→设置→关于本机→本机信息→按上下左右」是 TCL 系的走法，不是海尔的；海尔那句在后半句，起手键是「信源」。
- 坑：替代动作（官方、免费、零风险）：同 YunOS 代——打 400-6999999 转投诉人工要求去除开机广告，售后上门免费处理，或电话指导装官方「广告助手」App 并由官方提供 8 位校验码。不想开 ADB 的人先做这个。另有网友分享的第三方「冻结开机广告」App（U 盘或当贝市场推送安装，打开点一下即可），但这是单一网友来源，不如官方路径可靠。

**待验证 · 欢迎投稿**

- YunOS 代到安卓代的确切切换年份，以及第一批安卓机型的型号清单，没查到。本轮只能把边界收窄到：海尔官网 2018-05 的 LS65AL88A71 规格页仍写「阿里YunOS」、2019-10 的 65T75 页面仍提 YunOS，而 2024 年的 43H5 / 50A20 规格页写「安卓系统」。2019-2024 之间是空白。
- 统帅（Leader）与卡萨帝（Casarte）电视是否与海尔电视共用同一套系统和同一个 ADB 入口，仍无直接证据。本轮只查到卡萨帝电视「无 U 盘安装第三方软件」的教程（走应用商店装悟空遥控器一类工具），说明它至少和海尔电视是同一套应用生态玩法，但该帖完全不涉及 ADB；统帅没有任何专属的 ADB 资料，一条都没有。
- 「信源 + 左左上右」与「信源 + 左左右右」两种记法哪个正确、是否按机型/年份分化——三个来源 2:1，没有本机实测可以定案。
- 海尔机型开启 ADB 后是否弹授权确认框、是否需要 Android 11 式的无线调试配对码、端口是否为 5555：三项全部零证据（5555 是安卓默认值）。
- 海尔预装应用包名一条都没有来源：must_keep（桌面/信号源/遥控/输入法/设置/工厂）与 junk（广告/推荐/上报/购物/演示）全部空白，广告模块的包名同样未知。要补必须有一台实机跑 pm list packages。
- 海外/台湾海尔（LE75S8UX2、H65S900UX2 等 UX 尾缀型号）走 Google TV / Android TV，入口理应是标准安卓连点法，但本轮未验证，且不属于本条目的 region=cn 范围。
- 社区有海尔刷机包与 root 相关讨论（电视刷机网等），本项目不采用。

## 海信 / Vidda / Hisense · 部分适用

分三种情况：国行安卓系（海信 / Vidda）要先进工厂菜单把 To Fac 由 U 改成 M 才会露出安卓原生设置，然后才能开 USB 调试，越老越值得做；海外北美的 Android TV / Google TV / Fire TV 机型走标准连点 7 次，最省事；海外欧洲及其他地区的 VIDAA（Linux）机型根本不是安卓，没有 ADB 也没有 pm 命令，只能在设置里关个性化广告。

**怎么认机型**

- remote_has_digits：国行约 2018 年前的老机遥控器普遍带 0-9 数字键（可直接在声音平衡处输 1969 进工厂菜单）；2019 年后国行与 Vidda 遥控器大多取消数字键，只剩方向键 / OK / 菜单 / 返回，部分带红绿黄蓝彩色键；海外遥控器一般无数字键，取而代之是 Netflix / Prime Video / VIDAA 等流媒体直达键
- system_name_shown_at：国行：设置 → 关于 / 本机信息（这里只会看到海信自己的软件版本，看不到安卓版本；要连按几次菜单键才会跳出隐藏的安卓原生设置）。海外：Settings → System → About / Support（VIDAA 机型这里显示 VIDAA 版本号；Google TV 机型这里显示 Android TV OS Build）
- about_page_fields：型号（如 65E7G-PRO / 55U8N / HZ60U7A）；软件版本 / Build Number（海外 VIDAA 固件号形如 L0901，用于判断是不是 2021 年后的机器）；VIDAA 版本（海外显示 VIDAA U2.5 / U3 / U4 / U5 / U6 / U7，2024 年后改叫 VIDAA OS 8.x / 9）；安卓版本（只有安卓系机型有；国行要进隐藏的安卓原生设置才看得到）；芯片 / SoC（海外在 DevKit → System Info 里能看到 MT5658 / MSD6886 / MT9602 / MT9900 等）；MAC 地址
- model_naming_rule：国行形如「65E7G-PRO」：65 = 尺寸；紧跟的字母 = 系列（E 线上电商款、U 线下 ULED 高端款、A 线下渠道款、S 社交电视）；数字 = 档次（3 系入门、5 系中端、7 / 8 系高端）；最后一个字母 = 年代号，按字母表逐年往后走，已确认 E=2019、F=2020、G=2021、H=2022；2023 年起常见 K、2024 年 N、2025 年 Q（这三个是按各年上市机型倒推的，海信官方没公布过对照表，容易混的 I / J / L / M 被跳过）。Vidda 子品牌型号形如「Vidda X65 Pro」「55V1F-S」，不完全遵守主品牌规律。海外同代机型会在尾部再加平台字母区分，例如 2025 款美版 U8QG 跑 Google TV、欧版 U8Q 跑 VIDAA。
- sticker_location：机身背面右下角或左下角的铭牌贴纸，印完整型号和序列号；部分挂架机型贴在底座支架背面。国行老机型号常带括号后缀（如 HZ60U7A(0000)），括号内是固件批次，找教程时可以忽略。
- year_ranges：国行安卓系覆盖约 2014-至今；海外 VIDAA（Linux）自 2014 年起在欧洲等非美非中市场为主力；北美 2018-2021 以 Android TV 为主，2022 年起整线换 Google TV，同时另有 Roku TV / Fire TV / Xumo TV 版本并行在售

### 国行 老款 VIDAA（安卓 5.1 / 6.0 时代，数字键遥控器）（约 2015-2018，android Android 5.1 / 5.1.1 / 6.0）

- 认：遥控器带完整 0-9 数字键；桌面是海信自家的「聚好看 / 聚好用 / 聚好玩」三格布局；设置 → 关于里只写海信软件版本，看不到「安卓版本」字样；整机内存 1.5-2 GB。
- 值不值：值得做，但收益上限也就到这儿：本机实测 2017 款 2 GB 机型清掉 29 个包后空闲内存从 551 MB 涨到 988 MB、开机广告消失，操作明显跟手。但机器自带存储只有 2 GB 级别，扣掉系统后剩约 200 MB，现代第三方 App 常常装不上，别指望靠它救回一台能跑新应用的电视。

**遥控器带数字键的老机（最省事的一条）** · 证据：多源
1. 设置 → 声音（若有「杜比全景声 / 杜比音效」先关掉，否则「声音平衡」是灰的点不动）
2. 光标停在「声音平衡」上
3. 直接用数字键输入 1969，进入工厂菜单（部分地区 / 机型流传 0532、0538 两个码，说法不统一）
4. 切到「选项」标签页，把第一项「To Fac」由默认「U」改成「M」
5. 按电源键关机，等电视完全重启
6. 重启后进设置 → 关于 → 本机信息，连按遥控器「菜单」键 3-5 次，跳出隐藏的安卓原生设置
7. 进「关于手机 / 关于电视」，光标移到最下方的版本号，连按 OK 键 7 次，提示「您已进入开发者模式」
8. 返回一级，进「开发者选项」，打开「USB 调试」
9. 电脑上 adb connect 电视IP:5555，电视会弹授权框，勾「一律允许」
- 授权弹窗：有；端口 5555
- ⚠ 工厂菜单里只改「To Fac」这一项，改完立刻关机，别动同页面其他任何参数（背光、白平衡、屏参一改就可能花屏或亮度异常，且没有一键还原）。「母块清空」是重置类操作，只有在同时想清广告缓存、且接受电视恢复出厂时才碰。
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[教你海信智能电视如何打开工厂模式](https://www.sohu.com/a/79836150_366131) 2016-06-03
- 来源：[Hisense - 海信电视（QuickTVUI 设备接入指南）](https://www.quicktvui.com/zh-CN/guide/device/hisense.html) 

**本机实测：Vidda 50 寸 / VIDAA_TV / MSD6A828 / 固件 LVY48C 01.02.40.01（2017-05-22 构建）/ Android 5.1.1 / 2 GB RAM** · 证据：本机实测
1. 设置 → 声音设置 → 光标停在「声音平衡」
2. 交替按遥控器 OK 键与菜单键，屏幕右侧出现雪花即进入工厂菜单
3. 「选项」页把 To Fac 由 U 改为 M，重启
4. 重启后桌面出现安卓原生「设置」应用：关于 → 连按版本号 → 开发者选项 → 调试 → USB 调试
5. 这台机器没有「无线调试」项；adb connect 电视IP:5555 后电视弹授权框，勾「一律允许」
- 授权弹窗：有；端口 5555
- ⚠ 只改「To Fac」一项。另外遥控器按关机 = 待机而不是真重启，验证效果必须用 adb reboot（约 80 秒后回连），否则会误判成「改了没用」。
- 来源：[海信VIDDA 旧电视 LED55N3000U 折腾记录（同代机型的第三方印证）](https://krau.top/posts/garbage-hisense-tv) 2025-08-05
- 命令能力：disable-user=no · hide=yes · uninstall-user0=unknown（本机实测 2017 款 MSD6A828：pm disable-user / pm disable / pm trim-caches 全部抛 SecurityException（shell uid 2000 无权限）；pm hide / pm unhide 可用，效果与 disable 等价，pm unhide 可完整还原。settings put global 可用（动画缩放 1.0 → 0.5 有效）。这台机器没有 logcat 二进制。未测 pm uninstall --user 0。）
- 禁停（9）：`com.jamdeo.tv.vod`、`com.jamdeo.*`、`com.android.settings`、`com.hisense.uifac`、`com.hisense.systeminfo`、`com.mstar.android.tv.app`、`com.sohu.inputmethod.sogouoem`、`com.hisense.tv.tiletransition`、`com.google.android.webview`
- 可停（14）：`com.hisense.service.ad`、`com.hisense.business.ad`、`com.hisense.tv.advertisementproxy`、`com.hisense.hitv.shopping`、`com.hisense.hitv.payment`、`com.hisense.demonstrate`、`com.hisense.electronictutorial`、`com.hisense.hireport`、`com.hisense.hitv.das`、`com.hisense.cloudfalcon`、`com.jamdeo.tv.silentpackageinstaller`、`com.hisense.service.message`、`com.hisense.startupimage`、`com.hisense.magic.ad`
- 看你用不用（20）：`com.keylab.speech.core.vidaa`、`com.keylab.speech.view.vidaa`、`com.hisense.voicecontrol`、`com.hisense.vidaaassistant`、`com.intelligent.speech`、`com.hispeech.ai.area`、`com.hisense.speech.aiupgrade`、`cn.airjoy.android.service`、`com.hisense.multiscreen.screenhelper`、`com.multiscreen.airplay`、`com.hisense.wifidisplay`、`com.ju.miguvideoTV.cmcc`、`com.tencent.qqmusictv`、`com.audiocn.karaoke.micmanager`、`com.speech.chatroom`、`com.hisense.store.tv`、`com.hisense.hitv.hicloud.account`、`com.hisense.tv.ota`、`com.hisense.remoteupgrade`、`com.hisense.usbupgrade`
- 坑：遥控器按关机只是待机，不是真重启。本机实测：不用 adb reboot 真重启，测出来的空闲内存和广告状态都是旧的，很容易误判成「白干了」
- 坑：本机实测：第一批一次性藏太多包之后 system_server 看门狗重启了一次，桌面黑屏（内容区空白但音量条还能弹）。全部 pm unhide 再 reboot 即可恢复，之后改成逐组藏包 + 每组真重启，全程正常——所以这不是某个包必须常驻，而是一次动太多的问题
- 坑：别想着换第三方桌面。这代机器装不上现代 launcher，而海信自家桌面绑着信号源切换和设置入口，桌面一没，遥控器上的信号源键和设置键就没地方去
- 坑：清广告缓存 / 精简前先在路由器上把电视断网，否则系统会自动重新下载广告素材；全程保持断网直到确认无广告再联网
- 坑：To Fac 由 U 改成 M 之后有副作用：某些固件下「海信聚好看」手机 App 会搜不到这台电视，微信电视 / QQ 视频通话 / 慧享家等预装 App 从桌面消失，「Hi 投屏」提示被禁用（实测投屏其实还能用）
- 坑：这代机器出厂存储只有 2 GB 级别，扣掉系统和预装后用户可用约 200 MB，很多现代第三方 App 因为架构过旧或空间不足根本装不上——ADB 打通了也不等于能装新应用
- 坑：社区有 root 讨论，本项目不采用

### 国行 VIDDA3 / VIDDA4（无数字键遥控器，交替按键版）（约 2019-2022，android Android 7-9 一带（社区描述不完全统一，多数帖子对应安卓 9））

- 认：遥控器已经没有 0-9 数字键，只有方向键 / OK / 菜单 / 返回，部分带红绿黄蓝彩色键；桌面仍是聚好看那套横向内容流；设置里有「杜比全景声」开关（进声音平衡前必须先关，否则声音平衡是灰的）。
- 值不值：最值得做的一代：运存通常 1.5-2 GB，广告已经比老机更密，而工厂菜单入口还在、pm uninstall --user 0 也还能用，清一轮下来体感提升明显。

**无数字键遥控器（这一代的主流路径）** · 证据：多源
1. 设置 → 声音 → 先关掉「杜比全景声 / 杜比音效」（不关的话「声音平衡」是灰的）
2. 进「高级设置」，光标选中「声音平衡」
3. 交替按遥控器「确认(OK)」键和「菜单」键，重复 2-3 轮，进入雪花屏
4. 再按 1-2 次菜单键，调出工厂菜单
5. 「选项」标签页第一项「To Fac」由 U 改成 M
6. 按电源键关机，等电视重启
7. 重启后：设置 → 关于 → 本机信息，连按「菜单」键 3-5 次，进入隐藏的安卓系统设置
8. 找到「关于手机」→ 光标移到最下方的版本号 → 连按确认键 7 次，提示已开启开发者模式
9. 返回一级 → 开发者选项 → 打开 USB 调试
10. 电脑 adb connect 电视IP:5555，电视弹授权框选「允许 / 始终允许」
- 授权弹窗：有；端口 5555
- ⚠ 工厂菜单里只改「To Fac」这一项。「母块清空」在同页面，按方向右键就会执行并重置电视，手滑代价很大，不清广告缓存就别碰它。
- 来源：[海信电视如何去广告？2024海信电视去广告精简系统教程（作者机型 HZ60U7A(0000)）](https://www.znds.com/tv-1251066-1-1.html) 2024-07-25
- 来源：[海信电视进入工厂模式，方便adb](https://blog.csdn.net/ScottePerk/article/details/123598981) 2022-03-19
- 来源：[海信vidda电视进入工厂模式，开启adb调试，安装app（实测可行）](https://blog.csdn.net/qq_39636272/article/details/130476557) 2023-05-03
- 来源：[海信电视打开ADB进入开发者模式方法](https://www.znds.com/tv-1170751-1-1.html) 

**带红绿黄蓝彩色键的遥控器** · 证据：多源
1. 设置 → 声音 → 声音平衡
2. 在声音平衡页面按遥控器下方的红、绿、黄、蓝彩色键进入工厂模式（出现雪花屏 / 大写 M）
3. 后续与主流程相同：To Fac 改 M → 重启 → 关于 → 本机信息连按菜单键 → 版本号连点 → 开发者选项 → USB 调试
- 授权弹窗：有；端口 5555
- ⚠ 只改 To Fac 一项。
- 来源：[2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[海信E7K电视如何进入工厂模式](https://www.znds.com/tv-1256285-1-1.html) 

**桌面上有「VIDAA 助手」的机型（绕过工厂菜单的官方向入口）** · 证据：多源
1. 我的应用 / 全部应用 → 打开「VIDAA 助手」
2. 进「设置」→ 找到「电视调试授权」并打开
3. 同时到开发者选项里把「ADB 调试」打开
- 授权弹窗：有；端口 5555
- ⚠ 这条最安全（完全不进工厂菜单），但只有部分机型的 VIDAA 助手里有「电视调试授权」这一项，没有就只能回到工厂菜单那条路。
- 来源：[【超全】2024各品牌电视打开ADB调试方法汇总（海信方法1）](https://www.znds.com/tv-1251785-1-1.html) 2024-08-15
- 来源：[2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（HZ60U7A 的完整精简教程通篇用 pm uninstall -k --user 0 且逐条列了包名，说明这代 shell 有卸载权限；另有优化指南用 pm disable-user --user 0 处理广告 / 语音类包并称安全。注意这与本机实测的 2017 款 MSD6A828 不同——那代 disable-user 被 SecurityException 挡死，只能用 pm hide。也就是说 pm 权限是按代放开 / 收紧的，别拿一代的结论套另一代。）
- 禁停（8）：`com.jamdeo.tv.vod`、`*GlobalUI*`、`*TvBaseService*`、`com.jamdeo.tv.livetv`、`com.hisense.prism`、`com.hisense.tv.tiletransition`、`com.android.settings`、`com.android.tv.settings`
- 可停（18）：`com.hisense.service.ad`、`com.hisense.magic.ad`、`com.hisense.tv.advertisementproxy`、`com.hisense.hitv.das`、`com.hisense.tv.catlog`、`com.hisense.ctsapp`、`com.hisense.demonstrate`、`com.hisense.electronictutorial`、`com.baidu.map.location`、`com.android.location.fused`、`com.hisense.ai.service`、`com.hisense.ai.area`、`com.hisense.tv.infer`、`com.aispeech.dui.dds.tvassistant`、`com.jamdeo.android.avatarresources`、`com.jamdeo.android.jamdeoavatartrafficcontrol`、`com.android.settings.intelligence`、`com.gitvdemo.video`
- 看你用不用（20）：`com.iflytek.xiri`、`com.hisense.voicecontrol`、`com.hisense.vidaaassistant`、`com.keylab.speech.core.vidaa`、`com.keylab.speech.view.vidaa`、`com.hisense.speech.aiupgrade`、`com.multiscreen.airplay`、`com.hisense.multiscreen.screenhelper`、`com.hisense.wifidisplay`、`com.ms.weshare`、`com.hisense.hitv.hicloud.account`、`com.hisense.tv.ota`、`com.hisense.remoteupgrade`、`com.hisense.usbupgrade`、`com.jamdeo.updateservice`、`com.tencent.qqmusictv`、`com.bajintech.karaok`、`com.hisense.tv.videochat`、`com.android.wechathci`、`com.sohu.inputmethod.sogouoem`
- 坑：有一篇流传很广的 HZ60U7A 精简清单把 com.android.tv.settings、com.android.settings、com.jamdeo.tv.vod、com.sohu.inputmethod.sogouoem 也列在了包列表里（作者本人只是罗列、没有全删）。照抄整张清单会把设置、桌面和输入法一起干掉，直接变砖——只执行带 pm uninstall 前缀的那几条，且逐条看懂再敲
- 坑：进声音平衡前必须先关杜比全景声，否则「声音平衡」是灰的点不动，很容易误判成「这台机器的工厂入口被砍了」
- 坑：「母块清空」与 To Fac 在工厂菜单同一页，按方向右键立即执行并重置电视；不清广告缓存就别碰
- 坑：精简前先在路由器上断掉电视的网，不然广告素材会自动重新下载回来
- 坑：社区有 root 讨论，本项目不采用

### 国行 新款 海信 / Vidda（VIDAA AI 聚好看新版）（约 2023 至今（K / N / Q 代），android 未公开标注；国行不走标准 Android TV 分支，关于页不直接显示安卓版本，要进隐藏的安卓原生设置才看得到）

- 认：遥控器无数字键，有的是带光标的「指向性遥控器」；桌面是全屏卡片流，顶部有横向推荐位与开机广告；设置里能看到「开机管理 → 开机广告」这类新增开关（老机型没有）；型号最后一个字母是 K / N / Q。
- 值不值：看情况。这一代运存普遍 2-4 GB，卡顿没老机那么严重，主要痛点是广告；而且工厂模式入口是不是还在，各机型固件差别很大，社区 2025-2026 年的教程仍称 Vidda 可用，但 ZNDS 2026 汇总帖同时明确说「很多 VIDAA 机型即使有开发者模式提示，也未必真开放 ADB」。先按下面步骤试一遍，试不通就退回到设置里关开机广告。

**2023 年后国行海信 / Vidda（社区 2025-2026 年仍在用的路径）** · 证据：多源
1. 全部设置 → 声音设置 → 先关掉杜比音效（没有就跳过）
2. 找到「声音平衡」，进入后 OK 键和设置 / 菜单键交替按，直到出现雪花屏和一个大写 M 的界面
3. 按遥控器菜单键调出工厂菜单，在「选项」一栏把 toFac 的 U 改成 M，然后重启电视
4. 重启后：设置 → 关于电视 → 本机信息处连续按菜单键，进入安卓设置后台
5. 在「手机状态 / 关于手机」里找到版本信息，连续点击 7 次打开开发者选项
6. 开发者选项 → 打开 USB 调试（若同时出现「无线调试」也一并打开）
7. 电脑或手机 ADB 工具刷新设备列表，连接后在电视弹出的「允许 USB 调试」对话框选「始终允许」
- 授权弹窗：有；端口 5555
- ⚠ 工厂菜单里只改「toFac」这一项，其余参数（背光、白平衡、屏参、母块清空）一律不动。这一代固件更新更勤，入口被后续 OTA 堵掉的可能性比老机高。
- 来源：[海信电视怎么打开ADB？声音平衡点不了？vidda也能用（当贝市场）](https://www.sohu.com/a/904074012_213569) 2025-06-13
- 来源：[全网最详细的海信Vidda电视打开adb教学](https://post.smzdm.com/p/aogzlnxn) 2026-01-16
- 来源：[海信电视怎么开启ADB？四步解锁开发者权限](https://zhuanlan.zhihu.com/p/1890085242871715197) 2025-03-31

**备选：连点版本号无效时，用 Activity Manager 类工具直接拉起隐藏的 TV 设置** · 证据：单源
1. 先用 U 盘或海信爱家 / 当贝市场装一个 Activity Manager 类应用
2. 用它直接拉起 com.android.tv.settings 的组件，进入隐藏的开发者选项页
3. 在该页面打开 ADB 调试
- 授权弹窗：有；端口 5555
- ⚠ 只有单一来源，没找到第二方交叉验证，只作为工厂菜单路径失效后的备选。
- 来源：[教你通过 Activity Manager 应用来打开海信电视的 TV 设置（com.android.tv.settings）的开发者选项](https://www.bilibili.com/video/BV1fse3zeETg/) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（没有找到任何 2023 年后国行机型的 pm 权限实测记录。已知的两个数据点方向相反（2017 款 disable-user 被封、2020 款 uninstall --user 0 可用），不能外推到这一代，请以实机 pm list packages 后逐条试为准。）
- 坑：开机广告在这一代有了官方开关（设置 → 系统设置 → 开机管理 → 开机广告），但实测反馈是关掉后仍有约 5 秒的「隐形」等待——所谓可关闭更接近文字游戏
- 坑：打开 ADB 无线调试后，有用户反馈带光标的「指向性遥控器」光标消失，影响正常操作
- 坑：ZNDS 2026 年汇总帖明确提醒：VIDAA 不是标准 Android TV，很多机型即使能点出「开发者模式」提示，也不一定真的开放 ADB 和普通 APK 安装——能不能用要看具体型号和固件
- 坑：社区有 root 讨论，本项目不采用

### 国行 标准 Android TV / Google TV 分支（若确属）（年份与机型清单未核实，android 随所属 Android TV / Google TV 版本）

- 认：判据不是型号而是界面：开机后是 Android TV / Google TV 的原生横向卡片首页（左上角 Google TV 字样）、能进 Google Play 商店、设置里直接有「系统 → 关于 → Android TV OS Build」这一行。国行海信 / Vidda 绝大多数不是这种，如果你看到的是聚好看那套界面，就走上面几条。
- 值不值：如果确认是这一类，是所有海信机型里最省事的：不用进工厂菜单。

**确认跑标准 Android TV / Google TV 的机型** · 证据：官方
1. 设置 → 系统 → 关于 → 「Android TV OS Build」/ 版本号
2. 连按 OK 键 7 次，提示「您已成为开发者」
3. 返回：设置 → 系统 → 开发者选项 → 打开「USB 调试 / ADB 调试 / 网络调试」
4. adb connect 电视IP:5555，电视弹授权框选允许
- 授权弹窗：有；端口 5555
- ⚠ 方法本身是 Google 官方通用路径，风险在前提：先确认这台机器真的是标准 Android TV / Google TV，而不是套了原生壳的 VIDAA。
- 来源：[创建和运行电视应用 | Android TV（Google 官方）](https://developer.android.com/training/tv/get-started/create?hl=zh-cn) 
- 来源：[2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未查到国行此类机型的实测记录。）

### 海外 Hisense Android TV（北美 / 部分地区，G 代及以前）（约 2018-2021，android Android 8 / 9 / 11（Android TV 分支））

- 认：开机是 Android TV 原生首页（顶部一行「Apps」「Play Next」推荐条，不是 Google TV 那种整屏影视海报流）；遥控器带 Google Assistant 麦克风键和 Netflix / Prime Video 直达键；设置 → Device Preferences → About 里有「Build」这一行。
- 值不值：值得做且最省事——入口是标准的，不用碰工厂菜单。这批机器现在 5-7 年机龄，Android 8/9 上的推荐与预装确实吃内存，清一轮有感。

**所有 Hisense Android TV 机型** · 证据：多源
1. 首页右上角齿轮进 Settings
2. Device Preferences → About
3. 往下找到「Build」，选中后连按 OK 键 7 次，提示「You are now a developer」
4. 返回 Device Preferences，出现新的「Developer options」
5. 进去打开「USB debugging」
6. 电脑 adb connect 电视IP:5555，电视弹授权框选 Always allow
- 授权弹窗：有；端口 5555
- ⚠ 用完建议把 USB debugging 关掉，别让 5555 端口长期裸露在网络上。
- 来源：[Hisense Android TV USB Debugging Mode [Developer Options]（逐步演示）](https://www.youtube.com/watch?v=R30AIhbONCA) 
- 来源：[HISENSE Android TV: How to Enable Developer Options](https://www.youtube.com/watch?v=_gQ854SzHoQ) 2023-11-04
- 来源：[创建和运行电视应用 | Android TV（Google 官方）](https://developer.android.com/training/tv/get-started/create?hl=zh-cn) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（没有找到针对海外 Hisense Android TV 的 pm 权限实测记录。这批机器是 AOSP Android TV 分支，理论上 pm uninstall --user 0 / pm disable-user 都应可用，但厂商固件常单独收权限，请以实机试为准。社区有 bootloader 解锁 / root 讨论，本项目不采用。）

### 海外 Hisense Google TV（北美 2022 起）（2022 至今，android Android 11-14（Google TV））

- 认：开机是 Google TV 整屏影视海报流、左上角有你的 Google 头像；设置 → System → About 里那一行叫「Android TV OS build」。北美 2024 年起同一代机型美版是 Google TV、欧版是 VIDAA，型号只差尾字母，别拿美版教程套欧版机器。
- 值不值：机器还新（3 年内）、运存 3-4 GB，卡顿不明显，主要动机是关推荐与广告。做的收益不如老机型大，但入口最容易。

**所有 Hisense Google TV 机型** · 证据：多源
1. All Settings → System → About
2. 选中「Android TV OS build」，连按 OK 键 7 次，提示「You are now a developer」
3. 返回并往下找到新出现的「Developer options」
4. 打开「USB debugging」，弹窗按 OK 确认
5. adb connect 电视IP:5555；Android 13 及以上的部分机型只提供「Wireless debugging」，那种要先用配对码（Pair device with pairing code）配对，不能直接连 5555
- 授权弹窗：有；需配对码；端口 5555
- ⚠ 别把「无线调试」和老式 adb connect ip:5555 搞混：Android 11 引入的无线调试要配对码，端口是随机的，配对成功后才能 connect。
- 来源：[HiSense Google TV: How to Enable or Disable USB Debugging Mode](https://www.youtube.com/watch?v=Tyb_JZEpk-E) 
- 来源：[2026各品牌电视打开ADB调式的方法汇总（Android TV 13+ 需配对码）](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[Hisense unveils 2024 U8N, U7N, U6N miniLCD TVs（美版 Google TV / 欧版 VIDAA 分区的官方口径）](https://www.flatpanelshd.com/news.php?subaction=showfull&id=1705920595) 2024-01
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未查到海外 Hisense Google TV 机型的 pm 权限实测记录。）

### 海外 Hisense / Toshiba VIDAA（Linux）—— 不适用（2014 至今（欧洲、澳新及美中以外大多数市场的主力），vidaa-linux 不适用，不是 Android）

- 认：最快的判据：没有 Google Play 商店、装不了任何 APK，应用商店里全是 VIDAA 自己那套；设置 → System → About / Support 里显示的是「VIDAA U5」「VIDAA OS 9」这类版本号而不是 Android 版本；遥控器上有 VIDAA 键。型号尾部通常带 Q / QF / KQ / NQ（欧规）而不是美规的 G 尾。
- 值不值：【不适用】这类电视根本不是 Android：没有 adb、没有 pm 命令、不能装 APK，没有任何「清理预装包」的空间。能做的只有两件事：① 设置 → System → Advanced Settings → Personalised Ads（个性化广告）关掉，并执行一次 Reset Ads ID；② 确认电视处于 Home 模式而不是卖场的 Store 模式（Store 模式会一直播广告片）。还想更进一步只能在路由器 / DNS 层面（NextDNS、ControlD 一类）屏蔽广告域名。注意：官方的 VIDAA DevKit（应用商店搜索框上按 2775379 装出来）只能装 HTML5 网页应用且需要向 VIDAA 申请 secure code，不是 ADB，也清不掉预装。
- 命令能力：disable-user=no · hide=no · uninstall-user0=no（系统是 Linux + HTML5 应用框架，压根没有 Android 的 pm 命令，三项都无从谈起。）
- 坑：关广告的位置：Settings → System → Advanced Settings → Personalised Ads → 关闭并 Reset Ads ID；另外把电视从 Store 模式切回 Home 模式
- 坑：关了个性化广告也未必干净：2025-2026 年有多地用户报告开机后、切换 HDMI 输入时甚至换台时出现不可跳过的广告，且在已关闭全部广告选项的机器上照样出现。海信对西班牙市场的说法是「平台侧广告形式测试」。这一类是服务端下发的，本地设置挡不住，只能在 DNS 层拦
- 坑：同一平台还用在 Toshiba、JVC 等品牌电视上，这些机器的结论与 Hisense VIDAA 完全一致

### 海外 Hisense Fire TV 版（北美）（约 2021 至今，fireos Fire OS（基于 Android 的亚马逊分支，版本不对外标注））

- 认：开机是 Fire TV 界面、遥控器上有 Alexa 语音键和亚马逊 logo；设置菜单叫「My Fire TV」而不是「System」。
- 值不值：入口简单，但 Fire OS 的预装广告主要来自亚马逊自家推荐，能动的余地比国行安卓机小。

**Hisense Fire TV 版机型** · 证据：单源
1. Settings → My Fire TV → About
2. 选中设备名称，连按 OK 键 7 次解锁 Developer options
3. 返回 My Fire TV → Developer options → 打开「ADB debugging」（同页面还有「Apps from Unknown Sources」）
4. adb connect 电视IP:5555
- 授权弹窗：有；端口 5555
- ⚠ 只找到一条来源，且 Fire OS 版本间菜单层级有变动（有的机型 Developer options 直接在 My Fire TV 下，有的在 About 里）。
- 来源：[Hisense TV Fire TV settings not showing developer options](https://www.justanswer.com/tv-repair/pm43l-hisense-tv-firetv-settings-not-showing-developer-options.html) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未查到 Hisense Fire TV 机型的 pm 权限实测。）

### 海外 Hisense Roku TV / Xumo TV（北美）—— 不适用（约 2019 至今，roku 不适用，不是 Android）

- 认：遥控器上有紫色 Roku logo 或 Xumo 字样；首页是 Roku 的九宫格频道网格 / Xumo 的频道条，没有任何 Android 或 Google 元素。
- 值不值：【不适用】Roku OS 与 Xumo（Comcast 平台）都不是 Android，没有 adb 也没有 pm，装不了第三方应用。Roku 上的替代动作：Settings → Privacy → Advertising → 打开「Limit ad tracking」，并在 Settings → Privacy → Smart TV experience 里关掉「Use info from TV inputs」（ACR 内容识别）。Xumo 侧只有各 App 内的自动播放开关可调。
- 命令能力：disable-user=no · hide=no · uninstall-user0=no（非 Android 系统，无 pm 命令。）

**待验证 · 欢迎投稿**

- 2023 年后国行海信 / Vidda（K / N / Q 代）具体哪些型号还留着工厂菜单入口、哪些已被固件砍掉——社区 2025-2026 年的教程仍称「vidda 也能用」，但没有任何一篇点名具体型号加固件号；ZNDS 2026 汇总帖同时提醒很多 VIDAA 机型有开发者模式提示却不真开放 ADB。这是本轮最大的空白。
- 2023 年后国行机型的 pm 权限（disable-user / hide / uninstall --user 0 各自能不能用）完全没有实测记录。已知两个数据点方向相反：2017 款 MSD6A828 的 disable-user 被 SecurityException 封死只能用 hide（本机实测），2020 款 HZ60U7A 的 uninstall -k --user 0 可用（教程逐条列命令）。说明海信是按代调整 shell 权限的，不能外推。
- 国行海信 / Vidda 到底有没有搭载标准 Android TV / Google TV 的型号——本轮未找到任何官方或第三方给出的国行机型清单，只能让用户按界面自行判断（有没有 Google Play、首页是不是 Google TV 样式）。
- 海信型号年代字母 K=2023 / N=2024 / Q=2025 是按各年上市机型倒推的，海信官方从未公布过对照表；E=2019 / F=2020 / G=2021 / H=2022 有第三方文章明说。若后续拿到官方口径应更正。
- VIDDA3 与 VIDDA4 之间、以及国内「VIDAA 3 / 4 / 5 / 6」这套版本号到底怎么对应年份和安卓版本——中文资料里只有 ZNDS 板块名出现过「VIDAA3」，找不到一份把国内 VIDAA 版本号与年份、安卓版本对齐的权威材料。海外那条线反而清楚（U2.5 → U3 → U4 → U5 → U6 → U7 → OS 8.5 → OS 9，可用 SoC 反查），国内的版本号与海外并非同一套。
- 部分教程把工厂菜单里的参数写作「QFAZ」而不是「To Fac」，本轮仍未确认这是不同固件批次的命名差异还是转述错误。
- 国行工厂菜单的数字码在不同来源里出现 1969 / 0532 / 0538 三种说法，未能逐一对应到具体型号或年份；QuickTVUI 的设备接入文档里甚至「输入 0538」和「按数字 7 或确认键 7 次」两种表述并存。
- 海外 Hisense Android TV / Google TV / Fire TV 机型的 pm 权限均无实测记录，也没有可靠的 Hisense 专属垃圾包名清单（现有清单全部来自国行机型，包名前缀 com.jamdeo.* / com.hisense.* 在海外固件上不一定存在）。
- 官方渠道（VIDAA 官网 support、海信官网 support、海信开放平台 dev.hismarttv.com）均查不到任何关于开发者模式 / ADB 的官方文档——这条链路在消费级产品上属于非官方支持的工程遗留入口，随时可能被固件更新关掉，因此国行这几条入口永远拿不到「官方」级别的来源。

## 华为智慧屏 · 部分适用

2019-2026 年出厂系统是 HarmonyOS 4.X 及以下的智慧屏（V / S / SE / X / B / Vision 全系）底层仍是 Android/AOSP 分支，连点版本号出开发人员选项后能开 ADB，社区教程多；只有 2025 年起的 MateTV 系列出厂即 HarmonyOS 5.1/6.1（纯鸿蒙），没有 ADB，且官方确认既不能回退到 4.X、老机型也不会升到 5/6/7——所以「能不能做」看的是关于本机里的 HarmonyOS 版本号，不是买的年份。

**怎么认机型**

- remote_has_digits：未核实：未找到华为官方对智慧屏遥控器按键布局的说明，不要靠遥控器判断，直接看关于本机的版本号
- system_name_shown_at：智慧屏主页 → 设置 → 系统 → 关于本机；HarmonyOS 5.1 及以上的机器路径变成：设置 → 点击你的设备名称 → 关于本机（华为官方说明）。也可以在手机「智慧生活 App」里点智慧屏卡片 → 右上角 → 设备设置 → 设备信息 → HarmonyOS 版本
- about_page_fields：HarmonyOS 版本（智慧屏有自己独立的版本号轨道，形如 2.0.0.580、4.2.0.160(SP5)、4.3.0.115(SP3)、5.1、6.1，与手机端的版本号不是一回事）；设备型号（形如 HEGE-550、HD55KUNL、HD65AJMS、HD65EDIH）；序列号
- model_naming_rule：🔴 判断能不能做只看版本号，型号只用来对系列。归纳自华为官方机型清单：2019 年首批用 HEGE-550/560/570（V 系列）、PLAT-760（X65）；之后统一为 HD + 尺寸两位 + 四位平台代码，如 HD55KANB（智慧屏 S 55）、HD65DESA（智慧屏 SE 65）、HD65MILA（Vision 智慧屏 65）、HD65QINA（Vision 3）、HD55KUNL（Vision 4 SE）、HD65AJMS（S3 Pro）、HD65KULU（Vision 5 SE）；2024 年后新增 HDB5266H / HDB526G6 / HDB525A9 这类 HDB 开头的编号。🔴 MateTV 系列固定是 HD__EDIH（MateTV）/ HD__EDIJ（MateTV Pro、Max），看到 EDIH/EDIJ 就是纯鸿蒙、没有 ADB
- sticker_location：未找到华为官方对智慧屏铭牌位置的说明；型号和版本号建议直接在「设置 → 系统 → 关于本机」里看，最稳
- year_ranges：2019-08 首发（X/V 系列，HarmonyOS 1.x）→ 2020-2022 S/SE/B3（2.x-3.x）→ 2021-2026 Vision/Z/V5/S3/S5/4SE/5SE（3.x-4.3）→ 2025-2026 MateTV 系列（出厂 HarmonyOS 5.1/6.1，官方已预告 2026 Q4 推 HarmonyOS 7）

### 智慧屏 X 系列 / V 系列（初代 ~ 2021 款）（2019-2021，harmony 华为未公开；HarmonyOS 1.x-2.x 的 TV 固件是兼容 Android/AOSP 的分支，adb 能连上、pm 命令存在）

- 认：关于本机里版本号是 1.x 或 2.x；型号以 HEGE- 或 PLAT- 开头的就是 2019-2020 那两批首发机；桌面是早期「智慧屏」大卡片布局，带 AI 慧眼摄像头（V 系列可升降）
- 值不值：值得做：机龄 6-7 年、内存小、系统层广告与预装最多，清理后收益最明显；但这批机器已过保，动手前先确认还在正常使用

**HarmonyOS 4.X 及以下的华为智慧屏通用路径（2019-2026 除 MateTV 外全系）** · 证据：多源
1. 智慧屏主页 → 设置 → 关于本机（部分固件路径是：设置 → 系统和更新 → 关于本机）
2. 连续点击【版本号】7 次，直到提示已进入开发者模式（不同固件写的次数从 5 到 8 不等，多点几下即可，不需要密码）
3. 返回【设置】→【系统和更新】→【开发人员选项】
4. 打开【USB 调试】；部分固件在它下方还有一个【启用 ADB】子开关，要一起打开（另有教程里这个开关叫【HDB 调试】，看到哪个开哪个）
5. 电脑与智慧屏连同一个局域网，执行 adb connect <电视IP>:5555（也可以用 USB 线直连）
6. 首次连接时电视上会弹出调试授权确认框（类似手机的 RSA 指纹确认），用遥控器选允许
- 授权弹窗：有；端口 5555
- ⚠ 华为官方文档从未正面确认过 TV 端 ADB，全部是社区教程与网友实测；2021 年有用户在华为开发者论坛反馈自己的 V55i（HEGE-550，版本 2.0.0.188）只能打开【开发人员选项】、里面找不到 ADB 开关，官方没给过原因，所以存在「按教程做了但开关不存在」的情况
- 来源：[HuaWei | QuickTVUI 打开 USB 调试教程](https://quicktvui.com/zh-CN/guide/device/huawei.html) 
- 来源：[华为智慧屏 adb 方式怎么打开 - ZOL 问答](https://ask.zol.com.cn/x/16779157.html) 
- 来源：[Android 手机、电视(盒子) 打开 ADB 调试一览表](https://community.aijishu.com/a/1060000000016999) 2019-10-12
- 来源：[华为智慧屏使用 ADB 教程，可开无障碍](https://www.bilibili.com/video/BV1ut421F7tT/) 
- 来源：[adb 也能用来卸载华为智慧屏的自带应用（该文把开关写作「hdb 调试」）](https://zhuanlan.zhihu.com/p/585539070) 2022-11-22
- 来源：[如何看待华为智慧屏中的鸿蒙系统带有安卓的 ADB？](https://www.zhihu.com/question/339809553/answer/785404970) 2019-08-10
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（只查到一篇知乎专栏标题直指「adb 卸载华为智慧屏自带应用」，正文未抓取成功，没有可引用的命令与返回值；网上流传的华为包名清单几乎都来自手机端 EMUI/HarmonyOS 教程，未核实在 TV 固件上是否同名。动手前请先跑 adb shell pm list packages 看实际输出，再逐个试 pm disable-user，不要照搬手机教程）
- 坑：部分固件的【开发人员选项】里只有【USB 调试】，没有下面那个【启用 ADB】子开关，导致 adb devices 一直空；2021 年华为开发者论坛有两例同类反馈（型号 HEGE-550，版本 2.0.0.188(SP...)），官方未给根因或解法

### 智慧屏 S 系列 / SE 系列 / B3 系列（2020-2022，harmony 华为未公开；HarmonyOS 2.x-3.x 的 TV 固件仍是 Android/AOSP 兼容分支）

- 认：关于本机版本号是 2.x 或 3.x；型号形如 HD55KAN*（S 系列）、HD55DES*（SE 与 B3 系列）、HD__KHAA（SE MEMC 版）。S 系列 2020-12-21 发布，SE 系列 2021 年发布、配磁吸式摄像头
- 值不值：值得做：4-6 年机龄，2GB/3GB 内存机型在 2026 年已明显吃力，关掉开机广告与后台推荐类应用收益大

**同「智慧屏 X/V 系列」条目的通用路径（设置 → 关于本机 → 连点版本号 7 次 → 开发人员选项 → USB 调试 / 启用 ADB / HDB 调试 → adb connect IP:5555 → 电视上点允许）** · 证据：多源
1. 设置 → 关于本机 →【版本号】连点 7 次
2. 设置 → 系统和更新 → 开发人员选项
3. 打开【USB 调试】及其下方的【启用 ADB】（或写作【HDB 调试】）
4. adb connect <电视IP>:5555，在电视上确认授权弹窗
- 授权弹窗：有；端口 5555
- ⚠ 同上：华为官方无 TV 端 ADB 文档；个别固件缺少 ADB 子开关
- 来源：[HuaWei | QuickTVUI 打开 USB 调试教程](https://quicktvui.com/zh-CN/guide/device/huawei.html) 
- 来源：[华为智慧屏 adb 方式怎么打开 - ZOL 问答](https://ask.zol.com.cn/x/16779157.html) 
- 来源：[adb 也能用来卸载华为智慧屏的自带应用](https://zhuanlan.zhihu.com/p/585539070) 2022-11-22
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未找到该系列的 pm 命令实测记录，先 pm list packages 再逐个试）

### Vision 智慧屏 / Z 电竞版 / V5 / S3·S5 / Vision 3·4SE·5·5SE（HarmonyOS 3.x-4.3，最后一代 Android 底层）（2021-2026，harmony 华为未公开；出厂版本 HarmonyOS 4.3 及以下，官方明确这批机器不会升到 5/6/7，所以 Android 兼容层和 ADB 路径会一直在）

- 认：关于本机版本号是 3.x / 4.0 / 4.2 / 4.3（如 4.2.0.160(SP5)、4.3.0.115(SP3)）；型号是 HD + 尺寸 + MILA/QINA/KUNA/KUNL/KULU/FRU*/ARK*/AJM*，或 HDB52 开头。🔴 只要版本号是 4 开头或更低，这台机器就永远停在这一代（官方已声明不升级）
- 值不值：1-5 年机龄，内存通常 3-4GB，卡顿没那么严重，但首页推荐位与预装影音应用最多；主要收益在关广告/推荐而不是抢内存。做之前先看关于本机版本号确认是 4.X 及以下

**HarmonyOS 4.X 及以下的在售/在用机型** · 证据：多源
1. 设置 → 关于本机（或 设置 → 系统和更新 → 关于本机）→【版本号】连点 7 次
2. 返回 设置 → 系统和更新 → 开发人员选项
3. 打开【USB 调试】，以及下方的【启用 ADB】/【HDB 调试】开关
4. adb connect <电视IP>:5555，在电视上勾选允许（可勾「一律允许」）
- 授权弹窗：有；端口 5555
- ⚠ 2024-2026 年新机是否仍保留 ADB 开关，没有找到 2025 年之后的实测帖；官方只保证系统版本不变，没保证开发者开关不变
- 来源：[HuaWei | QuickTVUI 打开 USB 调试教程](https://quicktvui.com/zh-CN/guide/device/huawei.html) 
- 来源：[华为智慧屏使用 ADB 教程，可开无障碍](https://www.bilibili.com/video/BV1ut421F7tT/) 
- 来源：[adb 也能用来卸载华为智慧屏的自带应用](https://zhuanlan.zhihu.com/p/585539070) 2022-11-22

**HDC 调试（面向原生 HarmonyOS/ArkTS 应用，和上面的 Android ADB 是两条独立通路）** · 证据：单源
1. 在【开发人员选项】里找到并打开【HDC 调试】开关
2. 电脑装 DevEco Studio（或华为 Command Line Tools）
3. 在 DevEco Studio 里用 IPConnect 连接同一局域网的智慧屏
4. Run 配置里 Select Device 选中智慧屏
- 需配对码
- ⚠ 只有一篇论坛帖，帖里网友追问「智慧屏的 udid 怎么获取」没人答，流程本身不完整；哪些型号/固件起有这个开关、要不要登录开发者账号，官方都没文档。这条通路用于装原生鸿蒙应用，不是清理预装的工具
- 来源：[HarmonyOS 鸿蒙 Next 华为智慧屏的 HDC 调试功能已上线](https://bbs.itying.com/topic/67a5f27a55a429007d7d12bd) 约 2025-02
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（没有该系列的 pm 实测证据）

### 智慧屏 MateTV / MateTV Pro / MateTV Max（出厂 HarmonyOS 5.1 及以上）（2025-2026，harmony 无 Android 框架：官方文档写明当前仅 MateTV 系列出厂版本为 HarmonyOS 5 及以上（在售固件 5.1 / 6.1），且不支持回退到 4.X）

- 认：关于本机的 HarmonyOS 版本是 5.1 或 6.1；查版本的路径本身也变了——设置里先点你的设备名称，再进关于本机（HarmonyOS 5.1 及以上才是这个路径）；型号带 EDIH / EDIJ
- 值不值：🔴 不用做：新机、内存充足，而且系统里没有 ADB 这条路。官方已预告 2026 Q4 推 HarmonyOS 7，版本只会往前走不会回退
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（纯鸿蒙没有 Android 的 pm 命令体系；未找到任何 MateTV 上执行包管理命令的公开记录）
- 坑：🔴 别把手机端「HarmonyOS 5 起砍掉 ADB」的结论直接套到所有智慧屏上，也别反过来。华为官方明确：2019 年以后全系智慧屏都叫 HarmonyOS，但出厂 4.X 及以下的机器没有升级到 5/6/7 的计划，只有 MateTV 系列出厂就是 5 及以上。所以同一时间在售的两台华为电视，一台能 adb、一台完全没有，唯一判据是关于本机里的版本号
- 坑：智慧屏的固件版本号是独立轨道，官方支持文档里会出现 HarmonyOS 4.3.0.115(SP3)、4.2.0.160(SP5) 这种四段号，和手机端版本号不一一对应；某些论坛把智慧屏帖子归到「HarmonyOS Next」板块，但帖内用户机器显示的是 2.0.0.580——分类标签不等于真实系统代际

**待验证 · 欢迎投稿**

- HarmonyOS 4.X 及以下的智慧屏上，pm disable-user / pm uninstall --user 0 到底能不能用、返回什么，没有一条带命令输出的实测记录；TV 端专属的包名清单（广告、推荐、上报）也完全没有可引用来源
- 2024-2026 年出厂的 4.X 机型（HDB52 系列、Vision 5 SE 等）是否仍保留【启用 ADB】开关，没找到 2025 年之后的实测帖
- 【HDC 调试】开关从哪个型号/固件版本开始出现、是否需要登录华为开发者账号，官方无文档
- 2021 年用户反馈的「有开发人员选项但没有 ADB 子开关」的根因（固件版本？地区版本？账号限制？）无解释，也不知道现在还存不存在
- MateTV 系列的首销时间未逐一核实（官方支持页只标注适用版本 HarmonyOS 5.1 / 6.1）

## 联想 ideatv（老联想智能电视，含夏普贴牌机） · 部分适用

2012-2014 年那批联想 ideatv（Android 4.0.4）情况很特殊：adb 的 5555 端口出厂就开着、连的时候电视上连授权弹窗都没有，一条命令就连上——但连上之后什么也改不了。固件里根本没有 com.android.settings，所以「开发者选项」这个菜单压根不存在；pm disable 对系统包直接报 SecurityException，没有 pm hide，pm uninstall 也不带 --user 参数，66 个系统预装包一个都动不了，只有 /data/app 下用户自己装的第三方包能卸。一句话：连得上，改不了。

**怎么认机型**

- remote_has_digits：未核实
- system_name_shown_at：这一代固件没有安卓原生设置，看不到标准的「关于本机 → 版本号」；判断靠 adb 连上后执行 adb shell getprop ro.build.fingerprint，出现 lenovo/ideatv 字样即是
- about_page_fields：adb shell getprop ro.build.fingerprint → 形如 lenovo/ideatv/ideatv_K82:4.0.4/P0031.42/0083:user/dev-keys；机器自带的系统设置里只有电视功能项（信号源、图像、声音、网络），没有安卓的开发者选项
- model_naming_rule：联想自有机型叫 K 系列 / S 系列（K72、K82、50S9、55S9i）与 17TV（50i、55i2）；🔴 更容易踩空的是贴牌：机器外壳印的是「夏普」，但 fingerprint 里写的是 lenovo/ideatv/ideatv_K82——品牌和系统主体不是一回事，按外壳品牌去搜教程会全部搜错
- sticker_location：机身背面铭牌（未找到该代官方文档确认，按常规位置）
- year_ranges：约 2012-2016，Android 4.0.4（ICS）时代；本机实测机型为 2013 年的夏普 LCD-80LX850A

### ideatv 平台（Android 4.0.4，K/S 系列 + 夏普等贴牌机）（约 2012-2016，android 4.0.4（ICS））

- 认：开机桌面是联想那一代方块磁贴式界面；系统设置里只有电视相关项，没有安卓设置；1GB 内存（实测可用 653MB、空闲仅 90MB），全机只有 68 个包。最硬的判据是 adb 连上后看 fingerprint 里有没有 ideatv
- 值不值：🔴 连得上改不了：能进 adb，但系统包一个都停不掉，清理带不来任何内存变化。这台机器的卡顿是 2012 年的硬件天花板（1GB 内存 / Android 4.0.4），不是预装应用造成的，做完等于白做。真想改善只能外接一个电视盒子当主力，把电视当显示器用

**夏普 LCD-80LX850A（本机实测；fingerprint: lenovo/ideatv/ideatv_K82:4.0.4/P0031.42/0083:user/dev-keys）** · 证据：本机实测
1. 不需要在电视上做任何操作：这代固件没有 com.android.settings，「开发者选项」菜单不存在，任何连点版本号、按键组合都无处可点
2. 确认电视和电脑在同一个局域网，在电视的网络设置里记下电视 IP
3. 电脑上直接执行 adb connect <电视IP>:5555
4. 立刻连上，电视上不会弹任何授权确认框
- 授权弹窗：无；端口 5555
- ⚠ 🔴 出厂就监听 5555 且没有授权确认，意味着同一局域网里任何设备都能直连这台电视的 adb。这是这代固件的既有状态，不是你打开的；知道了就别把它放在不可信的公共网络里
- 来源：[ZNDS 联想智能电视版块（该代机型的集中讨论区，可交叉印证机型与固件命名）](https://www.znds.com/bbs-69-1.html) 
- 命令能力：disable-user=no · hide=no · uninstall-user0=no（本机实测：pm disable 对系统包直接返回 SecurityException；系统里根本没有 pm hide 命令；pm uninstall 不支持 --user 参数；无 su。66 个系统预装包全部动不了，只有 /data/app 下的第三方包能 pm uninstall）
- 可停（2）：`com.tianci.ad`、`com.lenovo.tvmanager`
- 坑：网上多数「电视开 ADB」教程的第一步都是「设置 → 关于 → 连点版本号」，对这一代机器完全不适用——它的固件里没有安卓设置这个应用，不是藏起来了，是不存在
- 坑：社区有 root 讨论（ZNDS 上流传过一个 2016 年的联想电视 root 工具），本项目不采用

**待验证 · 欢迎投稿**

- K72 / 50S9 / 55S9i / 17TV 50i 等同代机型是否与实测的 K82 平台完全一致（同样没有开发者选项、adb 5555 出厂开放、pm disable 报错），没有找到其他机型的独立实测记录，只能从同平台推断，不构成证据
- 这一代 ideatv 是否还有官方固件更新渠道，以及更新后 adb 5555 是否仍然出厂开放，未核实
- 贴牌范围有多大：除夏普 LCD-80LX850A 外，还有哪些品牌/型号实际跑的是 ideatv 平台，没有可引用的清单

## 松下 Panasonic · 部分适用

松下同一年份的电视可能跑四套完全不同的系统，能不能做 ADB 全看你这台是哪一套：Android TV / Google TV 机型可以（欧洲从 2020 年 HX700 开始、亚太与印度更早更全），My Home Screen（Firefox OS 后代）机型完全不行，2023 年起的 Fire TV 机型要走亚马逊自己那套入口。中国大陆从未正式销售松下 Android 电视，所以本条只覆盖海外机型。

**怎么认机型**

- remote_has_digits：多数欧洲 / 亚太机型遥控器有 0–9 数字键（要看地面波频道）。Android TV 版遥控器是蓝牙的，带麦克风键；Fire TV 版遥控器上印 Alexa 麦克风键和一排 Prime Video / Netflix 快捷键。
- system_name_shown_at：松下官方给的判据是看首页 / 应用页长相，不是看设置：① 按遥控器 [Apps] 键，看到 [Apps Market] 图标 = My Home Screen（不适用）；② 首页左上或右上有「Google TV」logo = Google TV；③ 首页长得像 Google TV 但应用图标样式不同、带 Google 助理四色圆点图标 = Android TV；④ 首页正中央一条大横幅 banner（2023 年起）= Fire TV；⑤ 长得像 My Home Screen 但图标叫「App Store」= Essential Smart OS（低端入门机，不适用）。
- about_page_fields：Android TV OS 版本 / TV OS Build（Android TV / Google TV 机型）；型号 TX-xxXXXnnn（欧洲）/ THxxXXXnnn（亚太）；软件版本 / Software version；Fire OS 版本（Fire TV 机型）
- model_naming_rule：欧洲型号 TX- + 尺寸(英寸) + 系列字母 + 数字 + 销售地字母，例 TX-55MZ800B = 55 英寸、MZ 系列、英国版。系列首字母就是年份：G=2019、H=2020、J=2021、L=2022、M=2023、无字母的 Z95A/W95A=2024、Z95B/W95B=2025。第二个字母是面板类型：Z=OLED、X/S=LCD。数字越大越高端（700 入门、800 中端、940/2000 旗舰）。🔴 同一个型号名在不同地区可能是不同系统：MX800 在欧洲是 Fire TV，在印度是 Google TV。
- sticker_location：机身背面或侧面接口附近的铭牌贴纸；型号和序列号是两个不同的号。
- year_ranges：2019 及更早（欧洲）= My Home Screen，本项目不适用；2020 起欧洲入门 LCD 开始出现 Android TV（HX700/HX710）；2023 年欧洲首次出现 Fire TV（MX800）；2024 年起欧洲主力线全面转 Fire TV，只剩个别型号（如沿用的 MZ800、Z80A 之外的少数）还是 Android TV；亚太 / 印度 2023 年起转 Google TV。

### Android TV 机型（欧洲 2020–2023 的部分型号）（2020–2023（欧洲），亚太 / 印度更早，android Android TV 9 / 10 / 11（各机型出厂版本未逐一核实））

- 认：首页是 Android TV 样式：一行行应用 + 推荐内容，没有 Google TV 字样也没有右上角圆形账号头像，但能看到 Google 助理那个四色圆点图标（松下官方判据）。型号里第一个字母 H/J/L/M 对应 2020/2021/2022/2023。按 [Apps] 键如果出来的是「Apps Market」，那就不是这一类，是 My Home Screen。
- 值不值：值得做，但收益中等。这批基本都是松下的入门到中端 LCD 和入门 OLED，硬件规格不高（HX700 在 r/AndroidTV 有用户吐槽卡到不行），Android TV 的首页推荐行和 Google 上报服务照样有。松下自己预装的东西比国产品牌少得多，清理出来的空间也相应少。

**松下 Android TV / Google TV 机型通用** · 证据：单源
1. 遥控器按 HOME → 进「设置」（齿轮图标）
2. 「系统 → 关于」（部分机型是「设备偏好设置 → 关于」）
3. 找到「TV OS Build」或「Android TV OS 版本 / 内部版本号」，确认键连按 7 次，提示「您现在是开发者」
4. 返回 → 「系统」或「设备偏好设置」里出现「开发者选项」
5. 进去打开「USB 调试」；有「网络调试 / ADB 调试」的话一起打开
6. 装第三方 APK 另需：「设置 → 设备偏好设置 → 安全和限制 → 未知来源」，给你用的安装器（如 Downloader、文件管理器）单独打开
7. adb connect 电视IP:5555，电视上确认授权弹窗
- 授权弹窗：有；端口 5555
- ⚠ 松下没有公开的 ADB 官方文档，上面的入口只有第三方教程视频作为直接证据，其余是 Android TV 通用流程的推定。第一次连之前先确认电视上真的弹了授权框——没弹就说明这台不是 Android TV 系。
- 来源：[How to Enable Developer Options on Panasonic TV（首页 → Settings → System → About → 「TV OS Build」按 OK 七次）](https://www.youtube.com/watch?v=WozFDRQjxdc) 
- 来源：[PANASONIC Android TV : How to Enable Developer Options](https://www.youtube.com/watch?v=zt2nvyhhQvI) 
- 来源：[PANASONIC Android TV : Install Apps From Unknown Sources](https://www.youtube.com/watch?v=XGE9wr7y3cM) 
- 来源：[How to sideload apps on Android TV: APK Install and ADB（Android TV 通用路径：Settings > Device Preferences > Security & Restrictions > Unknown sources；adb connect ip:5555）](https://www.xda-developers.com/how-to-sideload-apps-android-tv/) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（没有找到任何一份针对松下机型的 pm disable-user / uninstall --user 0 实测记录。松下机型是标准 Android TV，理论上和其他 Android TV 一致，但本项目不在没有证据的情况下标 yes。先在目标机上拿一个明确无害的包（如 com.google.android.play.games）试 disable-user 并确认能 enable 回来，再做后面的。）
- 禁停（5）：`com.google.android.apps.tv.launcherx`、`com.google.android.tungsten.setupwraith`、`com.android.location.fused`、`com.google.android.tv.remote.service`、`com.google.android.inputmethod.latin`
- 可停（3）：`com.google.android.tvrecommendations`、`com.google.android.tv.bugreportsender`、`com.google.android.backdrop`
- 看你用不用（3）：`com.google.android.katniss`、`com.google.android.apps.mediashell`、`com.android.vending`
- 坑：🔴 松下同年份的机型系统不统一，别按年份推断。2021 年 JX700 是 Android TV 而同年 JX800 以上是 My Home Screen；2022 年英国只有 LX800 一款是 Android TV；2023 年 MZ800/MZ700 是 Android TV 而同年 MX800 在欧洲已经是 Fire TV；2024 年 Z80A 是 Fire TV 而沿售的 MZ800 还是 Android TV。动手前必须按松下官方那套「看首页长相」的办法确认，不要按买的年份猜。
- 坑：🔴 同名型号跨区不同系统：MX800 在欧洲是松下第一款 Fire TV，在印度是 Google TV。查教程时一定要确认作者那台的销售地区，不然入口和包名全对不上。
- 坑：松下自有包名（com.panasonic.* 之类）没有任何公开的 debloat 清单，本条目里的 junk / must_keep 全部是 Android TV / Google 通用包。真要做的话，第一步是在自己那台上跑 adb shell pm list packages | grep -i panasonic 把实际包名列出来，逐个确认再动，不要照抄别的品牌的清单。

### Google TV 机型（印度 / 亚太，2023 起）（2023 至今，android Google TV，Android 11 及以后（具体版本未核实））

- 认：首页左上或右上有「Google TV」logo，右上角是圆形 Google 账号头像（松下官方判据）。型号是 TH- 前缀的亚太 / 印度型号。⚠️ 同名的 MX800 在欧洲是 Fire TV，看首页才算数。
- 值不值：和上一条 Android TV 机型同一个量级。Google TV 界面的推荐行和广告位比老 Android TV 更重，做完首页会干净不少；但这批机器年份新、内存不紧张，提速收益有限。

**松下 Google TV 机型** · 证据：单源
1. HOME → 右上角齿轮进「设置」→「系统」→「关于」
2. 找到「Android TV OS 版本 / TV OS Build」，确认键连按 7 次
3. 返回 →「系统」里出现「开发者选项」
4. 打开「USB 调试」，以及「网络调试」（若系统只有「无线调试 / Wireless debugging」，走配对码流程：adb pair 电视IP:配对端口 + 六位配对码，再 adb connect 电视IP:调试端口）
5. 装 APK 另需「设置 → 应用 → 安全和限制 / 特殊应用权限 → 安装未知应用」给安装器授权
6. adb connect 电视IP:5555（或上一步拿到的调试端口）
- 授权弹窗：有；端口 5555
- ⚠ 这批机型是不是已经变成「只有无线调试、没有 5555」，没有针对松下的实测记录。先试 5555，连不上再去开发者选项里找无线调试。
- 来源：[How to Enable Developer Options on Panasonic TV（Settings → System → About → TV OS Build 按 OK 七次）](https://www.youtube.com/watch?v=WozFDRQjxdc) 
- 来源：[How to sideload any application on Android TV（Google TV 的未知来源在 Apps → Security & Restrictions 下）](https://www.androidpolice.com/how-to-sideload-any-application-on-android-tv/) 
- 来源：[A Complete Guide to Android ADB Wireless Debugging（Android 11+ 的配对端口 / 调试端口区别）](https://gadgetguideonline.com/android/android-adb-wireless-debugging) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（同上：没有松下机型的实测记录，不猜。）
- 禁停（3）：`com.google.android.apps.tv.launcherx`、`com.android.location.fused`、`com.google.android.tv.remote.service`
- 可停（2）：`com.google.android.tvrecommendations`、`com.google.android.tv.bugreportsender`
- 看你用不用（2）：`com.google.android.katniss`、`com.google.android.apps.mediashell`
- 坑：Google TV 机型先试不用 ADB 的官方办法：「设置 → 帐号与登录 → 选中帐号 → 仅应用模式（Apps only mode）」开启后首页只剩应用图标，推荐行消失。代价是没有个性化结果和语音助手。这是 Google TV 独有的，Android TV 机型没有这个开关。

### My Home Screen 机型（Firefox OS 后代，欧洲主力线 2015–2023）（2015–2023（欧洲 / 日本主力线），部分入门机更晚，other 不适用，不是 Android 系统）

- 认：按遥控器上的 [Apps] 键，应用页里有一个「Apps Market」图标——松下官方说这是 My Home Screen 各版本一直没变的识别标志。首页是一排大圆圈或大方块磁贴（分别代表直播电视、应用、设备输入、书签），底部可能标着 My Home Screen 3.0 / 5.0 / 7.0 之类的版本号。遥控器上没有 Google 助理键。
- 值不值：不适用，也不太需要。My Home Screen 是 Firefox OS 的分支，没有 Android 层，没有开发者选项，没有 ADB，装不了第三方应用。反过来说它也没有 Android TV / Google TV 那种整页推荐行和首页广告，本来就比较干净——这套系统在评测里长期被认为是「难得不烦人」的电视系统。真要清爽点，直接在首页菜单里把用不到的磁贴 / 应用删掉重排就行，那是 My Home Screen 设计上就允许的。
- 坑：不适用原因：My Home Screen 不是 Android 系统，没有 Android 调试桥这一层，所以既没有开发者选项也没有 ADB 端口，本项目的任何清理动作都做不了。替代做法：① 首页按遥控器菜单键可以自由增删、重排磁贴和应用，把不看的直接拿掉；② 「主菜单 → 设置」里可以关掉各种横幅提示（如 Dolby 通知、频道信息 banner 的显示时长）；③ 日志 / 使用数据采集相关的选项在「设置 → 隐私 / 使用条款」一节，逐条看一遍关掉。没有找到松下针对 My Home Screen 的官方「关闭推荐」说明文档，因为这套系统本来就没有算法推荐位。
- 坑：2024 年起松下把 My Home Screen 整个弃用，欧洲主力线换成 Amazon Fire TV。所以「我的松下是 My Home Screen」这个结论只对 2023 年及更早买的机器成立，不能拿去套新机。

### Fire TV 机型（2023 起欧洲，2024 起北美 / 全球主力线）（2023 至今，fireos Fire OS 7（Android 9 内核）/ Fire OS 8（Android 11 内核））

- 认：首页正中央一条大横幅 banner、顶部是 Fire TV 的标签栏（松下官方判据），遥控器上印 Alexa 麦克风键和 Prime Video 快捷键。2024 年起这是松下的主力系统，也是松下时隔十年重返美国市场用的系统。
- 值不值：值得做，而且理由很直接：Fire TV 的首页横幅就是广告位，亚马逊把广告当成常态收入。Fire OS 底层是 Android，ADB 走亚马逊自己那套入口，官方文档公开。硬件是 2024/2025 的新机，提速收益不大，主要图去广告和少上报。

**Fire OS 7 / 8 机型（亚马逊官方通用流程，松下机型未见专门实测）** · 证据：官方
1. 「设置 → My Fire TV（我的电视）→ 关于」，高亮设备名称，确认键连按 7 次，提示已进入开发者模式
2. 返回「My Fire TV → 开发者选项」，打开「ADB 调试」和「来自未知来源的应用」
3. 在「设置 → My Fire TV → 关于 → 网络」里看电视 IP（注意不是「设置 → 网络」那一页，亚马逊官方专门提醒过这两页不一样）
4. 电脑上 adb connect 电视IP:5555（端口可在 5555–5585 范围内）
5. 第一次连接电视上会弹「是否允许 USB 调试？」，勾「一律允许来自这台计算机」再确定
- 授权弹窗：有；端口 5555
- ⚠ 亚马逊官方流程，但松下品牌的 Fire TV 机型上没有找到实测确认。Fire OS 的包名体系（com.amazon.*）和 Android TV 完全不同，本条目里其它系列的包名清单一条都不能套用。
- 来源：[Connect to Fire TV Through ADB（Amazon 官方开发者文档）](https://developer.amazon.com/docs/fire-tv/connecting-adb-to-device.html) 
- 来源：[Fire OS Overview（Amazon 官方：松下各 Fire TV 机型的设备代号与 Fire OS / Android 版本对照表）](https://developer.amazon.com/docs/fire-tv/fire-os-overview.html) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（没有找到松下 Fire TV 机型的 pm 实测。Fire OS 上亚马逊的核心服务包与 Android TV 差异很大，需要单独调研一轮 Fire OS 的 debloat 清单，本轮不覆盖。）
- 坑：🔴 Fire OS 的包名和 Android TV 完全是两套。本文件其它系列列的 com.google.android.* / com.sony.dtv.* 一条都不能拿到 Fire TV 机型上用。松下 Fire TV 的 must_keep / junk 清单本轮没做，需要另立一条 Fire TV 平台级调研（覆盖亚马逊、松下、JVC、Toshiba 等所有 Fire TV 电视）。
- 坑：亚马逊 2025 年起在部分新设备上换用了自研的 Vega OS（不是 Android 派生，如 Fire TV Stick 4K Select）。目前松下机型全部还在 Fire OS 8 上，但如果以后出现 Vega OS 的松下机型，ADB 这条路会直接断掉。

**待验证 · 欢迎投稿**

- 松下自有预装包名（com.panasonic.* 之类）一条都没找到有来源的清单。哪个包是信号源 / 调谐器、哪个是遥控、哪个是松下自己的推荐位，全部空白——这是本条目最大的缺口，必须在真机上 pm list packages 才能补。
- 松下 Android TV 机型的 ADB 入口只有第三方 YouTube 教程作为直接证据，松下官方从未发过开发者选项 / ADB 的支持文档。「TV OS Build」这个字段名是否在所有机型上一致，未核实。
- 松下 Android TV 各机型出厂 Android 版本和后续升级到哪个版本，没有逐一核实。松下全球支持页按「年份 + 地区」发布固件下载信息，要逐个型号页去翻才能确认。
- 松下 Android TV 机型上 pm disable-user 是否可用、有没有被厂商限制，零实测记录。
- 2021 年 JX 系列里到底哪些型号是 Android TV、哪些是 My Home Screen，只查到「JX700 是 Android、JX800 及以上是 My Home Screen」这个方向性结论，没有逐型号清单。
- 松下在中国大陆销售的电视用的是什么系统（是否有国行 Android 机型），本轮未查。本条目按 region=global 处理。
- 松下 Fire TV 机型（2024 起的主力线）的 must_keep / junk 包名清单完全空白，需要一条独立的 Fire OS 平台级调研。
- 松下 2025 年终止了欧洲和东南亚机型的内置 Google 助理服务，这对已上市 Android TV 机型的 com.google.android.katniss 等包造成了什么实际影响（是被 OTA 移除还是留着变成死包），未核实。
- 社区有 root / 解锁相关讨论，本项目不采用，也未检索其内容。

## 飞利浦 / TP Vision · 部分适用

飞利浦是本项目里系统血统最杂的品牌：同一个牌子下同时卖 Android TV / Google TV、SAPHI、Titan OS、北美 Roku TV、国行京东OS 联名款五套互不相通的系统，只有 Android TV / Google TV 那一支能做 ADB；其余四支不是安卓，只能走「关广告 / 关推荐」的设置项。动手前必须先用型号和关于页确认自己是哪一支。

**怎么认机型**

- remote_has_digits：分血统：Android TV / Google TV 机型多配无数字键的精简遥控（带 Google 助理麦克风键、Netflix / YouTube 等快捷键）；SAPHI 与 Titan OS 机型多配带 0–9 数字键的传统长条遥控；北美 Roku TV 机型是紫色圆形方向盘（Roku 紫色 d-pad）加四个流媒体快捷键。遥控只是旁证，最终以关于页为准。
- system_name_shown_at：欧版 / 亚太：设置 → 全部设置 → Android 设置 / 关于（Android 机型这一层会出现 Android TV OS Build 字样）。北美 Roku 机型：设置 → System → About（显示 Roku OS 版本与 Roku TV support）。SAPHI / Titan OS 只有「软件版本」，整页不出现 Android 字样。
- about_page_fields：Android TV OS Build / 版本号（只有 Android TV、Google TV 机型有）；Android 安全补丁级别；Roku OS 版本 + Roku TV support 厂商联系方式（北美 Roku TV 机型）；软件版本 / Software version（SAPHI、Titan OS 只有这一项）
- model_naming_rule：欧版 / 亚太型号末位数字 = 上市年份：…1=2016、2=2017、3=2018、4=2019、5=2020、6=2021、7=2022、8=2023、9=2024、0=2025（FlatpanelsHD 长期维护的对照规律）。字母段表示面板与市场：PUS / PUH / PUD / PUL 为 4K LCD，OLED 打头为 OLED，MLED 为 miniLED。北美型号形如 55PUL7875/F7（PUL 前缀 + /F7 市场后缀），基本都是 Roku TV。国行京东联名款型号带 /T3 后缀，如 65MLED621/T3、32PFF6899/T3。
- sticker_location：机身背面带条形码的贴纸上印有型号（Model）与序列号（Serial），这是官方给出的第一种查法；机内第二种查法是服务菜单 / 关于页。
- year_ranges：Android TV 约 2014–2023（中高端）；Google TV 2022 起（仅高端，2025 年只剩 OLED950 / OLED910 / OLED810 等）；SAPHI 约 2016–2024（中低端）；Titan OS 2024 起，2025 年几乎全部 LCD 与入门 OLED760 都改成 Titan OS；北美 Roku TV 常年在售。

### Android TV（欧版 / 亚太，PUS / PUH / PUD / OLED 系列，Android 5.1–10）（约2014–2021（按型号末位数字 4 及以前，加上 2020/2021 的部分中高端），android 5.1–10）

- 认：开机后主屏是 Android TV 原生启动器（横向卡片行，左上角有 Google 助理搜索图标）；官方支持文档给的判据就是「Android TV 可以通过主页左上角的 Google 助理搜索图标认出来」。设置里能找到独立的一层「Android 设置」，关于页有「Android TV OS Build」。遥控通常有 Google 助理麦克风键。
- 值不值：值得做。这批机器普遍 1.5–2GB 内存、机龄 5–10 年，Android 9/10 的启动器与后台服务是卡顿主因，禁用不用的预装流媒体与演示类应用收益明显。机龄越老、内存越小，收益越大。

**Android TV 5.1–10 的飞利浦机型（欧版 / 亚太 / 国行同路径）** · 证据：多源
1. 遥控按「设置」齿轮键 → 全部设置（All settings）
2. 进入「Android 设置」（Android Settings）
3. 设备偏好设置 / 系统 → 关于（About）
4. 把光标移到「Android TV OS Build」或「版本号」那一行，连按 OK 键 7 次，直到提示「您现在已是开发者」
5. 返回上一级 → 开发者选项（Developer options）
6. 打开「USB 调试」（部分固件只有 USB 调试、没有独立的 ADB 调试开关）
7. 设置 → 网络和互联网 → 状态，记下电视的 192.168.x.x 地址
8. 电脑上执行 adb connect <电视IP>:5555，电视端会弹授权框，选「允许」
- 授权弹窗：有；端口 5555
- ⚠ 飞利浦的菜单比其他 Android TV 品牌多一层：必须先进「Android 设置」，直接在飞利浦自己的设置树里翻是找不到关于页的。另有用户报告部分飞利浦固件的开发者选项里只有「USB 调试」没有「ADB 调试」，打开 USB 调试即可，不要因为找不到同名开关就以为失败。
- 来源：[HowTo: Enable Android Developer Options on Philips TV（给出 Settings > All settings > Android Settings 这一层飞利浦特有的菜单结构）](https://blog.codetitans.pl/post/howto-enable-android-developer-options-on-philips-tv) 
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[飞利浦电视安装第三方应用要开开发者模式吗](https://g.pconline.com.cn/ask/239350.html) 2026-05-14

**老款飞利浦部分机型（隐藏工厂菜单；年代与具体机型均未查到）** · 证据：单源
1. 设置 → 本机信息
2. 用遥控方向键依次输入：上 上 下 下 左 右 左 右
3. 进入工厂模式后在「高级设置」里找 ADB 开关，只把这一项打开
- 授权弹窗：无；端口 5555
- ⚠ 🔴 工厂菜单里只改 ADB 这一项，别的一律不碰——工厂菜单同屏还有面板类型、背光电流、出厂复位等会把电视改砖或改花屏的项目。该来源本身还提醒：网上有教程把康佳 K1 系列的型号和出厂密码错误地写进飞利浦教程，容易串台。优先用上面的开发者选项，这条只作为开发者选项被屏蔽时的兜底，且未找到第二来源印证。
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 命令能力：disable-user=unknown · hide=no · uninstall-user0=unknown（没有找到飞利浦机型的 pm disable-user / pm uninstall --user 0 实测记录。这一代机器是标准 Android TV，理论上与其他 Android TV 品牌一致，但按本项目「有证据才写 yes/no」的口径记为 unknown，实际操作请先在自己机器上拿一个无关紧要的包试一次再批量做。pm hide 记 no 的依据见第一轮 general.json：该命令自 Android 7.0 起已被系统锁定，多数设备直接抛权限异常，与品牌无关。）
- 坑：同一年份、同一系列里不同尺寸可能是不同系统。2019 年的 PUS6814 就是从 Android TV 换成飞利浦自研 SAPHI 的典型，型号只差一位却完全不能套用 ADB 流程。买二手或帮别人看机器时，型号对上了也要开机看一眼主屏。

### Google TV / Android TV 11+（高端 OLED 与 PUS9000 等，MediaTek 平台）（2022至今（2025 年仅 OLED950 / OLED910 / OLED810 保留 Google TV，其余全线 Titan OS），android 11–14）

- 认：主屏是 Google TV 的「为你推荐」大图海报流（不是 Android TV 的横向应用卡片行），右上角有 Google 账号头像。关于页写「Android TV OS Build」且 Android 版本 ≥ 11。2025 款 Google TV 机型出厂即 Android 14，硬件是 MediaTek 方案、只有两个 HDMI 2.1 口（Titan OS 机型是 Novatek 方案、四个 HDMI 2.1 口），这个接口数差异可以在不开机的情况下从机背区分。
- 值不值：中等。机龄新、内存一般 3GB 以上，卡顿收益不如老机器明显；主要收益是关掉 Google TV 首页的推荐流与预装流媒体的后台唤醒。机器还在保修期内的话建议先只做 disable-user 这一档。

**Android 11 及以上的飞利浦 Google TV 机型** · 证据：多源
1. 设置 → 系统 → 关于（Google TV 上不再需要先进「Android 设置」这一层）
2. 光标移到「Android TV OS Build」，连按 OK 键 7 次，提示「您现在已是开发者」
3. 返回 → 系统 → 开发者选项
4. 打开「USB 调试」；如果同时有「无线调试」开关，也打开
5. 开发者选项 → 无线调试 → 使用配对码配对设备，电视会显示 IP、配对端口和 6 位配对码
6. 电脑执行 adb pair <电视IP>:<配对端口>，输入 6 位配对码
7. 配对成功后 adb connect <电视IP>:<调试端口>（这个端口与配对端口不同，在无线调试主页面显示）
- 授权弹窗：有；需配对码
- ⚠ Android 11 起的无线调试端口是随机分配的，关掉「配对设备」弹窗后配对码和端口都会变；不要按老教程写死 5555。飞利浦部分固件只给 USB 调试、不给无线调试，这时要么插 USB 线走 adb tcpip 5555，要么放弃。
- 来源：[Android Debug Bridge (adb) — Wireless debugging / adb pair 官方说明](https://developer.android.com/tools/adb) 
- 来源：[Phillips Android TV no network debugging option?（飞利浦机型上无线调试开关存在与否随固件而异的用户报告）](https://www.reddit.com/r/AndroidTV/comments/j5yyee/phillips_android_tv_no_network_debugging_option) 
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 命令能力：disable-user=unknown · hide=no · uninstall-user0=unknown（同上一系列，无飞利浦机型专属实测。Android 12 以后部分厂商会把自家启动器和商店标为不可禁用，飞利浦机型是否如此未核实。）

### SAPHI（TP Vision 自研 Linux 系统）（约2016–2024（中低端；2024 年起被 Titan OS 取代），saphi 不适用（非 Android，没有 ADB、没有开发者选项））

- 认：开机主屏不是 Android TV 的卡片行，也没有 Google 助理搜索图标；应用是固定的一排图标、装不了 Google Play；关于页只有「软件版本」，整页不出现 Android 字样。官方支持文档把 Saphi 与 Titan OS、Google、Android、Linux 并列为五种不同平台，说明这不是 Android 的皮肤而是另一套系统。
- 值不值：不适用。不是安卓，没有 pm 命令，本项目的清理流程一条也用不上。这类机器的卡顿只能靠减少后台应用、恢复出厂、或外接一个安卓电视盒子解决。
- 坑：🔴 SAPHI 机型不能做 ADB。替代动作：设置 → 通用设置 / General → 隐私设置（Privacy settings）→ 隐私偏好 → 更多选择 → 选「全部拒绝」；再在全部设置里找「广告 ID」重置 ID，并打开「限制广告跟踪」。飞利浦把这两组开关放在同一棵设置树下，SAPHI 与 Titan OS 的路径基本一致（下面 Titan OS 那条给的实拍视频是 32PFS6939）。

### Titan OS（Linux，2024 年起的新中低端平台）（2024至今，other 不适用（Titan OS 是轻量 Linux 系统，非 Android，无 ADB））

- 认：主屏是 Titan OS 的自有排版（无 Google 助理图标、无 Google 账号头像），应用不需要「安装」、点开即用；关于页只有「软件版本」。硬件线索：Titan OS 机型用 Novatek 方案、机背有四个 HDMI 2.1 带宽接口，同代 Google TV 机型用 MediaTek 方案、只有两个。2024 年起飞利浦把 OLED759 / OLED769 以下的整条线都换成了 Titan OS，2025 年全线 LCD 都是 Titan OS。
- 值不值：不适用。非安卓，本项目流程用不上。机龄也新，卡顿本来就不是主要矛盾。
- 坑：🔴 Titan OS 机型不能做 ADB。替代动作（32PFS6939 实拍）：遥控齿轮键 → General（通用）→ Privacy settings（隐私设置）→ Privacy Preferences → More choices（更多选择）→ 选「Reject all（全部拒绝）」并确认；再按齿轮键 → All settings → Advertising ID → Reset ID（重置广告 ID）→ 打开 Limited Ad Tracking（限制广告跟踪）。改完要等流媒体频道列表刷新才看得出效果。

### 北美飞利浦 Roku TV（PUL… /F7 型号）（约2019至今（北美市场，由授权厂商生产），roku 不适用（Roku OS 非 Android，无 ADB））

- 认：机身正面右下角有 Roku TV 标；遥控是紫色圆形方向盘加四个流媒体快捷键；主屏是 Roku 的九宫格图标网格。设置 → System → About 里显示 Roku OS 版本，并有一行「Roku TV support」写着实际制造商的联系方式。型号是 PUL 前缀 + /F7 后缀。
- 值不值：不适用。Roku OS 不是安卓，没有 pm。Roku 机型本身占用低、不太卡，主要困扰是首页广告位和 ACR 跟踪，走设置里关即可。
- 坑：🔴 北美飞利浦 = Roku TV，不能做 ADB，也不要把欧版飞利浦的 Android 教程套上来。替代动作：Settings → Privacy → Smart TV Experience → 取消勾选「Use Info from TV Inputs」（这一项就是 Roku 的 ACR）；Settings → Privacy → Advertising → 勾上「Limit Ad Tracking」。详细见本项目的 roku.json。

### 国行「京东OS 联名款」（/T3 后缀）（2025至今，other 未核实（京东OS 是否基于 AOSP、有无开发者选项，均未找到公开说明））

- 认：只在国内电商（主要是京东）卖，商品标题里直接写「京东OS」「京东联名款」「开机无广告」；型号末尾是 /T3。开机不播开机广告、直接进桌面，桌面是京东OS 的自有排版，不是 Android TV 卡片行也不是 Google TV 海报流。
- 值不值：待核实。这条线的卖点本来就是「开机无广告」，本项目最主要的收益（去广告）已经被厂商在出厂时做掉了一半；剩下的内存与后台收益要等确认系统底层之后才谈得上。
- 坑：京东OS 不是飞利浦一家的系统层：创维 / 酷开的 JD01 联名款、KTC 的 43/50 英寸机型也都用它（KTC 那批标的是「京东OS 2.0」），说明它是一套可以移植到不同厂商硬件上的系统。百度百科条目称京东OS 电视「基于自研系统，开机后可直达桌面，且不包含开机广告」，但没说底层是不是 AOSP，也没有任何开发者选项 / ADB 的实测记录。在拿到实机确认之前，不要按 Android TV 的路径去试。

**待验证 · 欢迎投稿**

- 没有找到任何飞利浦机型的预装包名清单（com.tpvision.* 之类），所以 must_keep / junk / ask_user 三组全是空的。这是本文件最大的空白：拿到实机后跑一次 adb shell pm list packages -s 就能补上。
- 飞利浦机型的 pm disable-user / pm uninstall --user 0 没有任何实测记录，pm_caps 全部只能记 unknown。
- 国行「京东OS 联名款」的系统底层（是否基于 AOSP）、有无开发者选项与 ADB，均未找到公开资料；本文件只能确认它是与 Android TV / SAPHI / Titan OS 并列的第四套国行血统。
- SAPHI 机型在国行市场的具体型号列表未核实（现有证据都是欧版，如 PUS6814）。
- 隐藏工厂菜单「上上下下左右左右」适用的具体飞利浦机型和年代范围只有单一中文论坛来源，未找到官方或第二来源印证。
- 官方支持文档 How to tell if the Philips TV is a Titan OS, Google, Android, Saphi or Linux TV 是识别血统最权威的一页，但该页是 Salesforce 单页应用、抓取工具取不到正文，本文件只引用到了搜索引擎摘要里那句「Android TV 可以通过主页左上角的 Google 助理搜索图标认出来」。需要人工打开确认其余四种平台的官方识别判据。

## 夏普（国行） · 部分适用

夏普这个牌子下面其实是三种完全不同的机器，先分清是哪一种再动手：① 2012-2014 年的大屏贴牌机，壳子印夏普、系统是联想 ideatv，adb 出厂就开着但什么都改不了；② 2015-2021 年的国行安卓机（型号 LCD-xxSU/SF/MY/TX...A），标准的连点版本号法有一部分机型被固件屏蔽，得靠装第三方桌面或工具 App 绕出原生设置；③ 2016 年鸿海（富士康）入主后的 Android TV / Google TV 机型（型号 4T-C / 2T-C 开头），走 Android TV 标准路径最省事。国行夏普是「买了两年后被强推开机广告」的典型，清理需求真实存在。

**怎么认机型**

- remote_has_digits：未核实：国行夏普不同代遥控器差异大，未找到官方汇总
- system_name_shown_at：Android TV / Google TV 机型：设置 → 系统 → 关于 → 软件版本（夏普官方手册路径，按信息主页键进设定）。国行老安卓机：设置 → 本机信息（有的固件把「关于」叫「本机信息」）
- about_page_fields：Android TV OS Build / 软件版本；型号；Android 版本（Android TV 机型才有）
- model_naming_rule：🔴 型号前缀就能分代：`LCD-` 开头 + 结尾带 A 的是国行，如 LCD-70SU575A、LCD-60SU678A、LCD-60SU470A、LCD-40SF466A、LCD-45SF470A、LCD-50MY6100A、LCD-80X818A、LCD-80LX850A；中间两位字母是产品线代号（SU / SF / MY / TX / LX），不是年份，夏普未公开对应关系。`4T-C` / `2T-C` 开头（如 4T-C65FV1X、2T-C42BG1X）是鸿海体系的 Android TV / Google TV 命名，4T=4K、2T=Full HD，主要见于港台与海外市场，国行是否同款未核实
- sticker_location：机身背面铭牌与包装箱（未找到夏普官方对国行机型的统一说明）
- year_ranges：2012-2014 联想 ideatv 贴牌大屏 → 2015-2021 国行 LCD-xxxxA 安卓机（开机广告争议集中在这一段）→ 2016-04 鸿海收购夏普 66% 股权后逐步转向 Android TV / Google TV 平台

### 夏普×联想 ideatv 贴牌大屏（Android 4.0.4）（约 2012-2014，android 4.0.4（ICS））

- 认：80 寸级别的老大屏；系统设置里只有电视功能项，没有安卓设置；adb 连上后 getprop ro.build.fingerprint 显示 lenovo/ideatv/ideatv_K82:4.0.4/P0031.42/0083 —— 外壳是夏普，系统主体是联想 ideatv。详细条目见 lenovo.json
- 值不值：🔴 连得上改不了：adb 能进，但系统包一个都停不掉（pm disable 报 SecurityException、无 pm hide、pm uninstall 无 --user），清理不产生任何内存变化。卡顿是 1GB 内存 + Android 4.0.4 的硬件天花板，只能外接盒子

**夏普 LCD-80LX850A（本机实测）** · 证据：本机实测
1. 电视上无需任何操作：固件里没有 com.android.settings，不存在开发者选项
2. 电视与电脑同一局域网，记下电视 IP
3. 电脑执行 adb connect <电视IP>:5555，直接连上，电视上不弹授权框
- 授权弹窗：无；端口 5555
- ⚠ 5555 端口出厂监听且无授权确认，同网段任何设备都能直连，属于该固件的既有状态
- 命令能力：disable-user=no · hide=no · uninstall-user0=no（本机实测：pm disable 对系统包 SecurityException；无 pm hide；pm uninstall 不支持 --user；无 su；66 个系统包全部不可动，仅 /data/app 下第三方包可卸）

### 国行安卓智能电视（LCD-xxSU/SF/MY/TX...A 系列）（约 2015-2021，android 未核实：未找到夏普国行逐机型的 Android 版本说明；社区固件帖显示该段机型跑的是带夏普自家 UI 的安卓系统，不是原生 Android TV）

- 认：型号是 LCD- 开头、结尾字母 A；桌面是夏普自家的横向卡片 UI，不是 Android TV 那种横排「频道」首页；设置里通常把「关于」叫【本机信息】
- 值不值：值得做：这批机器是国行夏普开机广告投诉最集中的一段（有用户反馈买了两年后开始强推开机广告且无法取消），机龄 5-10 年、内存小，清广告与推荐类预装收益明显

**未被固件屏蔽的机型（标准安卓路径）** · 证据：多源
1. 设置 → 系统 → 关于（部分固件叫【本机信息】）
2. 找到【版本号】或【型号】，连续点击 7 次左右，提示进入开发者模式
3. 返回设置 → 开发者选项 → 打开【ADB 调试】/【USB 调试】
4. adb connect <电视IP>:5555，在电视上确认授权弹窗
- 授权弹窗：有；端口 5555
- ⚠ znds 原帖原话是「如果没屏蔽，进入设置后连续点击版本号即可打开开发者模式；夏普电视部分型号又被屏蔽」——没有给出被屏蔽的型号清单，所以这条路走不通属于已知情况，换下面那条
- 来源：[夏普电视如何进入开发者模式？（提到部分型号被屏蔽）](https://www.znds.com/tv-1028264-1-1.html) 
- 来源：[电视 adb 调试怎么打开？2026 各品牌电视打开 ADB 调试的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[智能电视隐藏设置：ADB 开关这样开（夏普条目写的入口名是「本机信息 → 连点型号」）](https://post.smzdm.com/p/a4qlqmzl) 2026-03-12

**标准路径被屏蔽的机型（借第三方 App 唤出原生设置，实测机型 LCD-70SU575A）** · 证据：单源
1. 电脑上下载一个通用 ADB 工具 App（原帖用的是 LM Ultimate），拷进 U 盘
2. U 盘插进夏普电视，弹出安装提示后装上并打开
3. 在这个 App 里选择「ENABLE STOCK LAUNCHER」（唤出系统原生桌面/设置）
4. 如果这台机器支持，电视上会弹出【允许 USB 调试吗？】，选允许
5. 随后弹出的界面里进【开发者选项】→【调试】，即可开启 ADB 调试
- 授权弹窗：有；端口 5555
- ⚠ 🔴 这条路要先在电视上装一个来路不明的第三方 apk（原帖给的是网盘链接），属于引入外部软件，普通家庭用户请谨慎；同一帖回复里也有人说「装个开发者选项 App + 甲壳虫 ADB 就行」，思路一样都是靠第三方应用绕过被屏蔽的入口。原帖只在 LCD-70SU575A 上验证过，不保证其他型号一样
- 来源：[夏普 LCD-70SU575A 怎么开启 adb？2024 夏普电视开 ADB 教程](https://www.znds.com/tv-1245728-1-1.html) 2024-02-23

**工厂菜单（借第三方桌面把工厂菜单 Activity 加到快捷入口）** · 证据：单源
1. 在电视上安装一个可自定义快捷方式的第三方桌面（原帖用的是「水滴轻桌面 TV 版」）
2. 进入该桌面主题后，在顶部图标（如「电视信源」）上按右键 → 自定义增加程序
3. 在程序列表里选择名字里带「工厂菜单」字样的那一项
4. 返回顶部图标，按确定键即可打开工厂菜单
- 授权弹窗：无
- ⚠ 🔴 工厂菜单里只改你要改的那一项，改完立刻退出。这个菜单里有屏参、背光、图像校正等参数，改错会让画面异常且没有「恢复默认」按钮，不要逐项试；也不要在里面执行任何清除/写入类操作
- 来源：[夏普 LCD-70SU575A 怎么开启 adb？2024 夏普电视开 ADB 教程](https://www.znds.com/tv-1245728-1-1.html) 2024-02-23
- 来源：[夏普安卓电视进入工厂菜单方法(ADB)](https://www.znds.com/tv-1216533-1-1.html) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未找到国行夏普机型上 pm 命令的实测记录。海外/日版社区给的做法是走系统里的应用列表逐个「停用」，不走命令行（见 sources），国行 UI 是否有同样的停用按钮未核实）
- 坑：国行夏普的开机广告是被投诉最多的一项：有用户反映买的时候没有广告，用了约两年后系统更新推来了无法跳过的开机广告。也就是说「我这台以前没广告」不代表以后不会有，这是随固件更新加上去的

### Android TV / Google TV 机型（4T-C / 2T-C 系列，鸿海入主后）（2016-04 鸿海收购夏普 66% 股权后至今，android 按年代 Android 8 ~ 14（夏普官方发布过 Android 11 与 Android 14 版操作手册；台版 AQUOS R3 于 2020-03-30 开放升级至 Android 10））

- 认：首页是 Android TV / Google TV 那种横排内容频道，遥控器上通常有 Google 助理键和 Netflix/YouTube 快捷键；设置里能看到「Android TV OS Build」字样；型号 4T-C / 2T-C 开头。🔴 这套命名主要出现在港台与海外市场，国行是否有同款未核实
- 值不值：机龄较新、内存相对宽裕，广告主要来自内容推荐行而非系统弹窗；比起 adb 清理，先在「设置 → 账号与安全」里按账号关掉助理/推荐更省事

**Android TV / Google TV 夏普机型（标准路径）** · 证据：多源
1. 设置 → 系统 → 关于 → 找到【Android TV OS Build】或【软件版本】
2. 连续点击 7 次，提示已成为开发者
3. 设置 → 系统 → 开发者选项 → 打开【ADB 调试】/【USB 调试】
4. adb connect <电视IP>:5555，在电视上确认授权弹窗
- 授权弹窗：有；端口 5555
- ⚠ 夏普官方手册只写到查看软件版本，没有写开发者选项；连点解锁属于 Android TV 通用行为而非夏普官方说明
- 来源：[电视 adb 调试怎么打开？2026 各品牌电视打开 ADB 调试的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01
- 来源：[夏普 LED 背光电视/显示器操作手册（简体中文，Android 11 版，含「设定 → 系统 → 关于 → 软件版本」路径）](https://global.sharp/restricted/support/aquos/emanual/asia/pdf/gu/android11/zhs/gu_zhs.pdf) 
- 命令能力：disable-user=unknown · hide=unknown · uninstall-user0=unknown（未找到该平台上的 pm 实测；夏普 Android 14 官方手册确认可在「账号与安全」下按账号单独开关 Google 助理，这类需求不必动 adb）

**待验证 · 欢迎投稿**

- 国行夏普 2016 年鸿海入主后到底用的是哪套系统：是标准 Android TV（带 GMS），还是保留夏普自家 UI 的定制安卓？只找到港台/日本/东南亚市场的手册与机型页，没有国行专属资料
- 国行是否存在 4T-C / 2T-C 命名的机型，还是国行一直沿用 LCD-xxxxA 命名，未核实
- LCD- 型号中间那两位字母（SU / SF / MY / TX / LX）对应的产品线定位与年份，夏普未公开
- 「部分型号连点版本号被屏蔽」具体是哪些型号，以及是不是某个固件版本之后统一屏蔽的，没有清单
- 国行夏普各代的预装包名（广告、推荐、上报）一个都没有可引用来源，must_keep / junk 暂时空着
- 国行夏普开机广告是否有官方的关闭入口或客服口径，未找到官方说明

## 夏普 AQUOS（海外：日本 / 亚太 / 欧洲 / 北美） · 部分适用

2016 年鸿海入主之后，夏普海外机型主力是标准 Android TV（MStar / MediaTek 平台），开发者选项就是原生的「连点 Build number 七次」，能做 ADB；但同一个 AQUOS 品牌在北美还有 Roku TV 版、在 2015 年前后有自研 SmartCentral / AQUOS NET+ 版，这两类不能做。2016–2019 那批 Android 7/8 老机是本项目收益最高的一档。

**怎么认机型**

- remote_has_digits：日版 / 亚太 Android TV 遥控通常保留 0–9 数字键（要选地面波频道），同时有 Google 助理麦克风键和 Netflix / YouTube / Prime Video 快捷键；北美 Roku 版遥控是紫色圆形方向盘、没有数字键；2015 年前后的 SmartCentral 机型遥控有一个写着 SmartCentral 的专用键。
- system_name_shown_at：Android 机型：设置 → 设备偏好设置 / 系统 → 关于（About）；部分固件这一页叫「本机信息」。北美 Roku 版：Settings → System → About。
- about_page_fields：Android TV OS Build / 版本号（Build number）；Android 版本与安全补丁级别；型号（4T-C… / 8T-C… / LC-…）；Roku OS 版本 + Roku TV support（只有北美 Roku 版有）
- model_naming_rule：日版 / 亚太现行型号形如 4T-C65DN1：开头 4T = 4K 电视、8T = 8K 电视；C = 彩色电视；中间两位是尺寸（65 英寸）；后面是系列代号，其中首字母按世代递进（B→C→D→E→F…，B 约 2019、C 约 2020、D 约 2021，依次后推），第二个字母是档位（N / L / J / P / S / Q / W / X 等，X、W 为 8K 旗舰）；亚太出口型号在系列代号后多一个 X（如 4T-C70DL1X、4TC70AL1X）。欧洲型号形如 65FN2EA / 43FH2EA / 65FP2EA：两位尺寸 + 两位系列 + 区域后缀 EA / EW。北美 2015 年前后的老机型是 LC- 打头（LC-70UH30U、LC-43UB30U），北美 Roku 版是 LC-…LBU591C 一类。
- sticker_location：机身背面 / 侧面接口区附近的铭牌贴纸上印 Model 与 S/N；壁挂后看不到时用关于页。
- year_ranges：自研 SmartCentral / AQUOS NET+：约 2013–2016；Android TV：2015 年北美 UE / UH 系列首发，2016 年鸿海入主后成为海外主力，延续至今；北美 Roku TV 版：约 2016 年起（部分由海信代工）。

### AQUOS Android TV 老款（约 2016–2019，Android 7 / 8，MStar 平台）（2016–2019，android 7.0–8.0（部分 2019 款可升到 9））

- 认：关于页 Android 版本显示 7.x 或 8.x；主屏是 Android TV 原生启动器（横向应用卡片行，左上角 Google 助理搜索图标），不是 Google TV 的海报流。日版系列代号首字母是 A / B，亚太出口型号带 X 后缀（AL1X）。应用列表里会出现 com.mediatek.wwtv.tvcenter 这类 MediaTek / MStar 平台自带的电视中心应用。
- 值不值：🔴 本品牌收益最高的一档，优先做。理由三条都占：机龄 7–10 年；这批普遍只有 1.5–2GB 内存；出厂预装里塞了卖场演示（epopdemo、fusion demo）、电子说明书、地区性预装（中东机型的祷告提醒 azan）等一堆几乎没人用的应用，禁掉后开机和切台明显变快。

**Android 7 / 8 的夏普 AQUOS 海外机型** · 证据：多源
1. 设置 → 设备偏好设置 / 系统 → 关于（About）；部分固件这一页叫「本机信息」
2. 光标移到「Build number」/「版本号」/「型号」那一行，连按 OK 键 7 次，提示「您现在已是开发者」
3. 返回 → 开发者选项（Developer options）
4. 打开「USB 调试」/「ADB 调试」
5. 设置 → 网络 → 查看电视的 192.168.x.x 地址
6. 电脑执行 adb connect <电视IP>:5555，电视端弹授权框选「允许」
7. （顺手）开发者选项 → 窗口动画 / 过渡动画 / Animator 时长缩放，三项都调到 0 或 0.5x
- 授权弹窗：有；端口 5555
- ⚠ znds tv-1028264 明确提到「夏普电视部分型号的连点版本号入口被固件屏蔽」，但没给被屏蔽的型号清单。连点七次没反应时先换一行试（Build number / 版本号 / 型号三行在不同固件上位置不一样），仍无效则这台机器走不通，不要去找工厂菜单硬闯。
- 来源：[Optimize Your Sharp Smart TV: Disable Built-in Apps for Faster Performance（夏普实机：Settings > About 连点 Build number 七次 → adb connect 192.168.254.101 → 逐条 pm uninstall）](https://blog.mdminhazulhaque.io/optimize-your-sharp-smart-tv-disable-built-in-apps-for-faster-performance) 2024-06-24
- 来源：[夏普电视如何进入开发者模式？](https://www.znds.com/tv-1028264-1-1.html) 
- 来源：[电视adb调试怎么打开？2026各品牌电视打开ADB调式的方法汇总](https://www.znds.com/tv-1270973-1-1.html) 2026-06-01

**夏普机型的另一种菜单叫法（隐藏入口，单一来源）** · 证据：单源
1. 设置 → 网络 → 本机信息
2. 连点「型号」那一行
3. 进入开发者选项 → 开启调试
- 授权弹窗：无；端口 5555
- ⚠ 这条与上面的通用法本质是同一套（连点型号 = 连点版本号），不是独立的第二条路径。收录它只是为了记下「关于页在夏普部分固件上叫『本机信息』、且挂在网络菜单下面」这个叫法差异，免得按标准教程找不到入口就放弃。
- 来源：[智能电视隐藏设置：ADB开关这样开，告别卡顿与广告](https://post.smzdm.com/p/a4qlqmzl) 2026-03-12
- 命令能力：disable-user=unknown · hide=no · uninstall-user0=yes（uninstall_user0 记 yes 的依据：有一份夏普实机记录逐条执行 pm uninstall --user 0 清掉了十个预装包并给出完整包名。disable_user 在夏普机上没有实测记录，记 unknown——但按破坏性排序 disable-user < uninstall --user 0，既然更重的那一档能跑通，更轻的一档跑不通的可能性很小，建议优先试 disable-user。pm hide 自 Android 7.0 起已被系统锁定，与品牌无关（见第一轮 general.json）。）
- 禁停（2）：`com.mediatek.wwtv.tvcenter`、`rcota.android.av.sharp.co.jp.rcota`
- 可停（4）：`fusion.android.tv.demo`、`jp.co.sharp.av.android.epopdemo`、`com.mstar.android.tv.disclaimercustomization`、`jp.co.sharp.av.android.emanual`
- 看你用不用（4）：`azan.android.av.sharp.co.jp`、`com.amazon.amazonvideo.livingroom`、`com.google.android.play.games`、`com.mediatek.wwtv.mediaplayer`
- 坑：🔴 那份夏普实机记录用的是 pm uninstall --user 0（把包从当前用户卸掉），本项目默认应该先用更轻的 pm disable-user --user 0。两者都可逆：前者用 pm install-existing <包名> 还原，后者用 pm enable <包名> 还原；但 uninstall --user 0 在恢复出厂或大版本升级时行为更难预料。除非 disable-user 被固件拒绝，否则不要一上来就 uninstall。

### AQUOS Android TV / Google TV 新款（约 2020 至今，Android 9–14）（2020至今，android 9–14）

- 认：关于页 Android 版本 ≥ 9；日版系列代号首字母到了 C / D 及以后（4T-C70CN1、4T-C70DN1），亚太出口型号带 X 后缀（4T-C70DL1X、4T-C70DK1X、8T-C70DW1X），欧洲型号是 FH / FG / FI / FK / FN / FP 两位系列 + EA / EW 后缀。Android 11 及以上的开发者选项里会多一个「无线调试」开关。台版 AQUOS 在 2020-03-30 开放升级到 Android 10 是这一代的时间锚点。
- 值不值：中等。机龄 3–6 年、内存多为 2–3GB，卡顿没有老机那么严重；收益主要在关掉演示类应用与预装流媒体的后台唤醒。Android 14 的机器还在保修期内，建议只做 disable-user。

**Android 9 / 10 的夏普 AQUOS（无线调试开关尚未普及）** · 证据：多源
1. 设置 → 设备偏好设置 → 关于
2. 连按「Build number」7 次
3. 返回 → 开发者选项 → 打开 USB 调试 / ADB 调试
4. adb connect <电视IP>:5555，电视端弹框选「允许」
- 授权弹窗：有；端口 5555
- ⚠ 夏普官方 Android 11 / Android 14 版操作手册里没有「开发者选项」这一章（官方手册只讲到账号与安全、Google 助理开关这一层），所以这条路径的证据来自社区实机而非官方文档。
- 来源：[夏普电视如何进入开发者模式？](https://www.znds.com/tv-1028264-1-1.html) 
- 来源：[Optimize Your Sharp Smart TV: Disable Built-in Apps for Faster Performance](https://blog.mdminhazulhaque.io/optimize-your-sharp-smart-tv-disable-built-in-apps-for-faster-performance) 2024-06-24

**Android 11 及以上的夏普 AQUOS（Google TV / Android TV 11+）** · 证据：官方
1. 设置 → 系统 → 关于 → 连按 Build number 7 次
2. 系统 → 开发者选项 → 打开「无线调试」
3. 无线调试 → 使用配对码配对设备，电视显示 IP、配对端口、6 位配对码
4. 电脑执行 adb pair <电视IP>:<配对端口>，输入配对码
5. 配对成功后 adb connect <电视IP>:<无线调试页面显示的调试端口>
- 授权弹窗：有；需配对码
- ⚠ 证据等级标「官方」指的是 adb pair 这套流程本身出自 Android 官方文档；夏普机型上具体开关的位置与命名没有夏普官方文档印证。配对端口每次打开弹窗都会变，不要写死。
- 来源：[Android Debug Bridge (adb) — Wireless debugging / adb pair（Android 11 起的官方配对流程）](https://developer.android.com/tools/adb) 
- 来源：[SHARP AQUOS Android 14 Operation Manual（确认新款 AQUOS 已到 Android 14；其「账号与安全」章可按账号单独关 Google 助理）](https://global.sharp/restricted/support/aquos/emanual/asia/pdf/hl/android14/eng/hl_eng.pdf) 
- 命令能力：disable-user=unknown · hide=no · uninstall-user0=unknown（新款没有夏普专属的 pm 实测记录。老款那份记录用的是 Android 7/8 时代的机器，不能直接外推到 Android 12–14（这几代对系统应用的保护更严）。先拿一个确认没用的包试一次再批量做。）
- 坑：夏普官方 Android 14 手册里有一个不用 ADB 就能做的事：在「账号与安全」下按账号单独开关 Google 助理。嫌首页推荐和语音唤醒烦的话，先试这一步，不一定要动 ADB。

### 北美 AQUOS Roku TV 版（约2016至今（北美 / 部分欧洲市场；部分机型由海信代工），roku 不适用（Roku OS 非 Android，无 ADB））

- 认：机身正面有 SHARP 字样加右下角 Roku TV 标；遥控是紫色圆形方向盘、没有数字键；主屏是 Roku 九宫格图标网格；设置 → System → About 显示 Roku OS 版本和「Roku TV support」里的实际制造商。维基百科的智能电视平台对照表把 Sharp 同时列在 Android TV、Roku OS、AQUOS NET+、SmartCentral 四栏下，就是这件事。
- 值不值：不适用。Roku OS 不是安卓，没有 pm。这类机器本身不太卡，困扰主要是首页广告位和 ACR 跟踪，走设置关即可。
- 坑：🔴 北美夏普有 Roku 版，不能套用日版 / 亚太的 Android 教程。替代动作：Settings → Privacy → Smart TV Experience → 取消勾选「Use Info from TV Inputs」（Roku 的 ACR）；Settings → Privacy → Advertising → 勾上「Limit Ad Tracking」。详见本项目 roku.json。

### SmartCentral / AQUOS NET+（2015 年前后的夏普自研系统，已停产）（约2013–2016，other 不适用（夏普自研，非 Android，无 ADB））

- 认：遥控上有一个写着「SmartCentral」的专用键；主屏是一条横向缩略图带、不是 Android TV 卡片行；装不了 Google Play。2015 年北美那批 AQUOS UHD 里，UE / UH 系列是 Android TV + 精简版 SmartCentral 双系统，最入门的 UB 系列（LC-43UB30U 到 LC-65UB30U）用的是完整版 SmartCentral，也就是纯自研、没有 Android。
- 值不值：不适用，且机器已经十年上下，多数已到寿命末期。要救只能外接安卓电视盒子，把电视当显示器用。
- 坑：同一年同一批「夏普 AQUOS 4K」里，好 / 更好 / 最好三档用的是两套系统：UB（入门）是自研 SmartCentral，UE / UH（中高端）才是 Android TV。看到「2015 年夏普 AQUOS」不能默认它是安卓，要按具体型号查。

**待验证 · 欢迎投稿**

- 「部分夏普型号的连点版本号入口被固件屏蔽」只有中文论坛的一句话，没有被屏蔽的型号清单，也没有替代入口。这是本文件最需要实机补的一条。
- 包名清单全部来自一份 2024 年的个人博客（一台 MediaTek/MStar 平台的夏普机），没有第二来源交叉印证；其中 com.mediatek.wwtv.tvcenter 与 rcota.android.av.sharp.co.jp.rcota 两个包被本项目从原文的「卸载清单」改判为 must_keep，这个改判是基于功能推断（电视信号源、OTA 升级），没有实机验证过卸掉之后到底会坏成什么样。
- 新款（Android 11–14）夏普机型的预装包名一个都没有；老款那批包名能不能对得上新款未知。
- 欧洲市场的夏普（FH / FG / FI / FK / FN / FP 系列）在 2015–2017 年曾由斯洛伐克 UMC 代工，之后被夏普买回。那段 UMC 时期的欧版机器用的是哪套系统，未核实。
- 日版系列代号首字母与年份的对应关系（B≈2019、C≈2020、D≈2021）是从官方内容下载器页面的机型清单加各年新品发布时间推出来的，夏普没有公开发布过这张对照表，按推断对待。
- 社区有 root / 刷机的讨论，本项目不采用。

## TCL / 雷鸟 / FFALCON · 部分适用

国行 TCL / 雷鸟（Android 内核，瀑布式桌面或灵控系统）都能开 ADB，入口是品牌专属的「关于本机 → 本机信息 → 上下左右」，冻结/卸载预装都已被国行用户实测跑通；海外 Android TV / Google TV 机型走标准「连点 Build 七次」；但海外的 TCL Roku TV 是 Roku OS、不是安卓，没有 ADB 也没有 pm，只能在设置里限制广告跟踪。

**怎么认机型**

- remote_has_digits：分型号：国行雷鸟与 TCL 现售机型的遥控器多为无数字键的简约款（只有方向键/OK/返回/主页/信源）；老款 TCL 与部分海外机型带完整 0-9 数字键——只有带数字键的机型才能走「对比度 → 1950」工厂菜单路径
- system_name_shown_at：国行：设置 → 系统 → 关于本机 → 本机信息；海外 Android TV / Google TV：Settings → System → About（看 Android TV OS build）；Roku TV：设置 → 系统 → 关于（显示 Roku OS 版本，没有 Android 字样）
- about_page_fields：型号（国行如 65R675C、55S515D、85Q10K；海外如 65R646、55P635）；机芯号 / 固件版本（国行形如 V8-A963T01-LF1V167、V8-T652T03，机芯号能直接定位平台）；系统版本 / 灵控桌面版本（国行灵控机型）；Android TV OS build（海外 Android TV / Google TV）；Roku OS 版本（Roku TV，且整页没有任何 Android 字样）
- model_naming_rule：TCL 国行 = 尺寸 + 系列前缀（X / C / Q / T / V，由高到低大致 X > Q > C > T > V）+ 数字 + 代际后缀字母，例 85Q10K = 85 英寸、Q 系列、K 代（2024）。雷鸟国行按鸟名分档，由高到低 凤 > 鹤 > 鹏 > 雀（另有青鸟入门线）：凤/鹤走 R 字头（R645C、R675C 鹤7 Pro、R685C 鹤7 24 款），鹏/雀走 S 字头（S575C 鹏7 Pro、S315C、S515D），末位字母 C/D 表代际。海外北美按 3/4/5/6-Series 命名，Roku 机型型号常见 R615 / S425 / S435，Google TV 机型常见 R646 / S546；欧洲用 P / C / X 系（P635、P725、C645、C725、C825、X925、C935）。
- sticker_location：机身背面铭牌或包装箱标签上的「型号 / MODEL」栏；国行也可直接在 设置 → 关于本机 页读到型号与机芯号
- year_ranges：国行：瀑布式桌面约 2019–2023 为主力，灵控桌面 2022 年推出、2023 年大面积推送，灵控系统 3.0 于 2024-12 随伏羲 AI 大模型发布，灵控桌面 3.5 传为 2026 Q1（存疑）。海外：Roku TV 2014 年起持续在售至今，Android TV 约 2019–2021 款，Google TV 自 2021 款 5/6-Series 起。

### 国行 · 瀑布式桌面（TCL 智屏 UI / 雷鸟旧系统）（约 2019–2023 为出厂主力，老机型至今仍在用，android 未逐型确认；社区教程涉及的机型多为 Android 7 / 9 一代）

- 认：开机有几秒广告倒计时（可跳过或不可跳过）；首页是上下滚动的「瀑布式」信息流大图推荐，没有可自由拖动的卡片；设置里能找到「关于本机 → 本机信息」，页面上有机型、机芯号、MAC；系统里没有「灵控桌面」这个词。
- 值不值：最值得做的一档：这代机型内存普遍 2GB/3GB，开机广告 + 首页信息流 + 预装教育/新闻/购物一起吃内存，机龄多在 3 年以上，冻结完提速最明显。

**国行 TCL / 雷鸟通用（这一代所有机型，遥控器不需要数字键）** · 证据：多源
1. 设置 → 系统 → 关于本机 → 本机信息（部分固件写作「设置 → 关于 → 本机信息」）
2. 停在这个信息页上，用遥控器依次按 上、下、左、右 各一次（是按顺序点四个方向键，不是连点某一个键）
3. 页面下方会多出隐藏项；部分固件需要先把「商场模式」打开，ADB 开关才会出现在它下面
4. 把「ADB」开关置为开启
5. 电脑与电视连同一局域网，执行 adb connect <电视IP>:5555，电视上会弹授权框，选「允许/一律允许当前电脑调试」
- 授权弹窗：有；端口 5555
- ⚠ 这是 TCL / 雷鸟 专属路径，和其他品牌「连点版本号 7 次」不是一回事，别互相套用。若页面上同时出现「商场模式」，只把它打开用来露出 ADB 开关，装完建议关回去——商场模式会改变待机、亮度与演示行为。
- 来源：[雷鸟电视删除系统自带软件、去开机广告、禁止自动更新（ZNDS）](https://www.znds.com/tv-1188736-1-1.html) 2021-01-19
- 来源：[TCL/雷鸟电视删除系统内置广告及应用方法，无需 root](https://zhuanlan.zhihu.com/p/535301521) 2022-07-14
- 来源：[卸载电视预装 APP（以雷鸟电视为例）](https://www.ggdoc.cn/archives/473) 2023-03-15
- 来源：[雷鸟电视如何打开 adb？雷鸟电视开 adb 解除三方软件安装限制](https://www.znds.com/tv-1248438-1-1.html) unknown
- 来源：[TCL/雷鸟电视安装当贝桌面等第三方软件（含「先开商场模式再开 ADB」）](https://youthlin.com/20261889.html) 2026-02-08

**老款 TCL / 部分海外同源机型，遥控器带 0-9 数字键（工厂菜单路径，仅在上面那条走不通时才用）** · 证据：多源
1. 按遥控器【信源】键，在弹窗里选择【模拟接收】，按 OK
2. 菜单键 → 设置 → OK
3. 显示 → 焦点右移到「专业设定」→ OK
4. 光标移到「对比度」这一项
5. 光标停在对比度上时，立刻依次按数字键 1、9、5、0，弹出隐藏的工厂菜单（Factory menu）
6. 焦点移到「Other」一栏 → OK
7. 找到「ADB」，按左右方向键切换为开启，然后原路退出
- 授权弹窗：有；端口 5555
- ⚠ 只改「Other」里的 ADB 这一项，改完原路退出，别动同页的画质、白平衡、屏参、面板类型等参数——这是面板服务菜单，改错会让画面偏色甚至花屏，且恢复出厂不一定能改回来。同系列密码（1950 / 9735 / 6428 / 9705 / 6405 / 6425）不是 ADB 专用，不要挨个试。
- 来源：[TCL 电视禁止第三方安装软件？如何远程给安卓智能电视装应用](https://blog.csdn.net/weixin_42036647/article/details/105942579) 2020-05-06
- 来源：[【超全】2024 各品牌电视打开 ADB 调试方法汇总（TCL 章节 1.2）](https://www.znds.com/tv-1251785-1-1.html) 2024-08-15
- 来源：[智能电视隐藏设置：ADB 开关这样开，告别卡顿与广告（并列列出 TCL 三条路径）](https://post.smzdm.com/p/a4qlqmzl) 2026-03-12
- 命令能力：disable-user=yes · hide=yes · uninstall-user0=yes（三条都有国行实测：`pm uninstall --user 0 <包名>` 对雷鸟教育/开机广告/头条新闻返回 Success；`pm hide <包名>` / `pm unhide <包名>` 被同一篇用于冻结更新组件；`pm disable-user <包名>` 被 2026 年的国行教程用于临时禁用安装器。社区共识仍是「能冻结就别删除」——uninstall --user 0 不可逆，只能靠 `pm install-existing <包名>` 碰运气找回，部分固件升级后该命令失效。）
- 禁停（2）：`com.android.packageinstaller`、`com.tcl.suspension`
- 可停（3）：`com.tcl.bootadservice`、`com.tcl.article.news`、`com.tcl.ffeducation`
- 看你用不用（3）：`com.tcl.versionUpdateApp`、`com.tcl.update`、`com.tcl.MultiScreenInteraction_TV`
- 坑：国行 adb install 第三方 apk 报 Failure [INSTALL_FAILED_VERIFICATION_FAILURE]：多为系统安装校验拦截。两条路——① `adb shell settings put global verifier_verify_adb_installs 0` 与 `adb shell settings put global package_verifier_enable 0`；② 临时 `pm disable-user com.android.packageinstaller`，装完立刻 `pm enable` 回来。第二条不 enable 回去，重启就开不了机。
- 坑：国行 TCL / 雷鸟系统层面会点名封杀某些 apk：当贝市场能装，当贝桌面装不上——apk 已经推到电视上也照样提示无法安装。这不是 U 盘或 apk 损坏，而是系统黑名单，只能靠上面那条临时禁用安装器绕过。
- 坑：U 盘装机入口在很多 TCL / 雷鸟机型的常规「应用管理 / 我的应用」里是空的，要进「应用卸载」入口才看得到 U 盘里的 apk；识别不到时可把后缀从 .apk 改成 .apk1 再改回来。另有一条不走 ADB 的官方旁路：应用商店 →「应用管家」页（页面上有 U盘安装/清理加速/网络测速/应用置顶）→ 按上下左右 → 出二维码 → 扫码进「超级大屏」小程序 → 从微信推 apk 到电视。
- 坑：adb devices 显示 unauthorized：要在电视端弹窗点「允许」。部分 TCL 机型首次连接的确认弹窗措辞是「模拟接受」这类非标准 Android 说法，容易被当成「没有授权提示」而漏点。
- 坑：网上流传的 TCL / 雷鸟「解除安装限制」命令有时效性：2022 年 7 月后就有人反馈原教程命令失效，2026 年仍有人照抄旧帖报错。照抄前先看发帖日期与机芯号是否对得上自己这台。
- 坑：禁停（包名未查到）：恢复出厂 -- 系统重置与工厂模式入口 有用户把「重置系统的那个 app」一并卸掉后，既不能恢复出厂、也进不了 recovery，连工厂菜单输四位数字都直接跳回主界面，最后只能靠 U 盘强刷救回来。凡是名字里带 factory / reset / recovery / update 的系统包，先放着别动。
- 坑：禁停（包名未查到）：工厂组件（国行，具体包名未查到） -- 系统重置与工厂模式入口 有用户把「重置系统的那个 app」一并卸掉后，既不能恢复出厂、也进不了 recovery，连工厂菜单输四位数字都直接跳回主界面，最后只能靠 U 盘强刷救回来。凡是名字里带 factory / reset / recovery / update 的系统包，先放着别动。
- 坑：禁停（包名未查到）：「天气」系统应用（国行，具体包名未查到） -- 系统内置天气组件 知乎社区反馈：删掉天气应用后设置界面直接打不开，只能恢复出厂或想办法找回包名重装。这是国行独立案例，不是海外清单里的条目。

### 国行 · 灵控桌面 1.0 / 2.0（2022 年推出 1.0，2023 年 8 月前后大面积推送并出现「灵控桌面·追光版」，2024 年被 3.0 取代，android 未确认（官方与媒体稿均未标注底层 Android 版本））

- 认：开机直接进桌面、没有开机广告；首页是一格格可自定义、可拖动的大卡片（不是上下滚动的信息流）；系统设置里能找到「灵控桌面」这个名字，且能在「标准桌面 / 灵控桌面」之间来回切换。早期是作为一个可安装/可升级的桌面推送给老机型的，不少机型是后装上去的。
- 值不值：中等：这一代已经没有开机广告，提速空间主要来自预装应用和首页推荐，收益不如瀑布式那一代明显；但机龄已到 2–4 年、内存 2GB/3GB 的机型仍值得冻一轮。

**国行 TCL / 雷鸟灵控 1.0 / 2.0 机型** · 证据：多源
1. 设置 → 系统 → 关于本机 → 本机信息
2. 在该页依次按遥控器 上、下、左、右 各一次
3. 出现隐藏项后把「ADB」开关打开（部分固件要先打开上面的「商场模式」，ADB 开关才在它下面露出来）
4. adb connect <电视IP>:5555，电视端弹窗选「允许 / 一律允许当前电脑调试」
- 授权弹窗：有；端口 5555
- ⚠ 和上一代同一条路径，说明这个专属入口至少从 2021 年一直延续到 2026 年的国行固件，没有随灵控换代改掉。
- 来源：[雷鸟常用设置及软件（1.1 打开 adb：设置→关于本机→本机信息→上下左右）](https://zhuanlan.zhihu.com/p/655763952) 2023-09
- 来源：[简洁！雷鸟电视如何轻松安装灵控桌面？（按上下左右在本机信息下调出 adb 菜单）](https://post.smzdm.com/p/apmmqv52) 2024-11-25
- 来源：[TCL 雷鸟电视安装当贝桌面 & 卸载自带软件（设置→系统→本机信息→上下左右）](https://blog.5625.cn/index.php/archives/46/) 2025-01-25
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（disable_user 有 2026 年国行灵控机型上的实操记录（临时禁用安装器）；uninstall --user 0 是这代教程的主流做法。hide 在灵控代没有直接实测记录，标 unknown（瀑布式那代有实测）。）
- 禁停（1）：`com.android.packageinstaller`
- 可停（1）：`com.tcl.article.news`
- 看你用不用（2）：`com.tcl.versionUpdateApp`、`com.tcl.update`
- 坑：灵控桌面不是所有机型都能装。第三方教程明确限 MT9652（及 9653/9655）芯片机型，社区实测成功名单是 TCL Q10G、雷鸟 R645C、鹤6、鹤6 Pro、鹏6、鹏6 Pro。硬装到不在名单上的机型上，轻则装不上、重则桌面循环重启。
- 坑：禁停（包名未查到）：灵控桌面本体（包名未查到） -- 当前默认桌面 灵控机型上灵控桌面就是 launcher。没有先装好并设为默认的第三方桌面就把它冻掉，开机会没有可操作的 UI——这是 Android TV 上通用的 launcher 自伤模式，国行同样成立。

### 国行 · 灵控系统 3.0（及传闻中的 3.5）（3.0 于 2024-12 随伏羲 AI 大模型在 TCL 发布会上推出，2026 年仍是国行主力；「灵控桌面 3.5」传为 2026 Q1 大版本，仅见视频标题，未见官方公告，android 未确认（官方从不标注底层 Android 版本））

- 认：出厂就是灵控（不是后装的），首页是可自由编排的卡片桌面；设置里能看到「灵控系统 3.0」字样，带「灵动卡片」「艺术画廊 / AI 壁纸」「全景刮削墙海报」这些 3.0 专属功能；TCL 侧还会有「伏羲 AI」相关入口。上面那份 2024-07 的官方升级名单（鹤7 24 款 / 鹤6 Pro 24 款 / 鹤6 24 款 / 鹏7 24 款）是出厂自带灵控的分界线。
- 值不值：收益最低的一档：全系无开机广告、实测 2 秒开机，内存普遍 4GB+，机龄 1–2 年。除非确实要装第三方桌面或卸特定预装，否则不值得动 ADB——先用设置里的「应用 → 应用商店 → 关闭自动推荐应用」把应用层推送关掉即可。

**国行灵控系统 3.0 机型（2024–2026 款 TCL / 雷鸟）** · 证据：多源
1. 设置 → 系统 → 关于本机 → 本机信息
2. 在该页依次按遥控器 上、下、左、右 各一次
3. 先打开出现的「商场模式」，它下面就会露出 ADB 开关
4. 打开「ADB」
5. adb connect <电视IP>:5555，电视端弹窗选「允许 / 一律允许当前电脑调试」
6. 用完建议把商场模式关回去
- 授权弹窗：有；端口 5555
- ⚠ 商场模式（演示模式）会改变待机、亮度、自动演示等行为，只用它露出 ADB 开关，别长期开着。
- 来源：[TCL/雷鸟电视安装当贝桌面等第三方软件（2026 年灵控机型实操，含商场模式细节）](https://youthlin.com/20261889.html) 2026-02-08
- 来源：[智能电视隐藏设置：ADB 开关这样开，告别卡顿与广告](https://post.smzdm.com/p/a4qlqmzl) 2026-03-12
- 来源：[TCL 电视到手先去广告装 App？三条路线加一个雷区讲清楚](https://post.smzdm.com/p/anvqdmn7) 2026-08-08

**部分较新雷鸟机型 / 固件（疑似标准 Android TV 路径，与上一条并列出现但适用机型未定）** · 证据：单源
1. 设置 → 系统 → 关于本机，连续点击「版本号」7 次激活开发者选项
2. 返回上级，进入「开发者选项」，开启「USB 调试」与「网络 ADB 调试」
3. adb connect <电视IP>:5555
- 授权弹窗：有；端口 5555
- ⚠ 和「上下左右」路径的机型分界没查清，不排除是作者把通用 Android TV 教程套到了 TCL 上。实操顺序建议：先试「上下左右」，不灵再试连点版本号 7 次。见 open_questions。
- 来源：[雷鸟电视预装应用无法卸载怎么办？](https://ask.csdn.net/questions/8859200) 2025-10-17
- 来源：[智能电视隐藏设置：ADB 开关这样开（把「连点版本号 7 次」和「上下左右」并列为 TCL 的两条路）](https://post.smzdm.com/p/a4qlqmzl) 2026-03-12
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=unknown（disable_user 有 2026 年国行实操记录。3.0 机型上直接 `pm uninstall --user 0` 系统包的成功回显没查到（老帖都是 2021–2022 年的瀑布式机型），未敢标 yes。这一代默认无开机广告，需要卸的系统包本来也少。）
- 禁停（1）：`com.android.packageinstaller`
- 看你用不用（2）：`com.tcl.versionUpdateApp`、`com.tcl.update`
- 坑：这一代先别急着上 ADB：官方路线已经够用——设置 → 应用 → 应用商店 → 关闭「自动推荐应用」，就能压掉应用层的推送广告；开机广告本身这代就没有。
- 坑：「灵控桌面 3.5」目前只有一条 B 站视频标题提到（称 26 年 Q1 大版本），没有官方公告或第二来源佐证。别把它当作已发布版本来判断机型代际。
- 坑：社区有 root / 刷机讨论（ZNDS 有雷鸟 root 与强刷帖），本项目不采用。
- 坑：禁停（包名未查到）：恢复出厂 -- 系统重置、recovery 与工厂模式入口 雷鸟「雀」系列 2025 款实例：卸掉重置系统的 app 后，恢复出厂、recovery、工厂菜单四位密码全部失效，最后靠 U 盘强刷救回。这是这一代最新的国行自伤案例。
- 坑：禁停（包名未查到）：工厂组件（包名未查到） -- 系统重置、recovery 与工厂模式入口 雷鸟「雀」系列 2025 款实例：卸掉重置系统的 app 后，恢复出厂、recovery、工厂菜单四位密码全部失效，最后靠 U 盘强刷救回。这是这一代最新的国行自伤案例。
- 坑：禁停（包名未查到）：灵控桌面本体（包名未查到） -- 默认桌面 launcher 这代出厂桌面就是灵控。没设好替代桌面就冻它，开机没有 UI。

### 海外 · TCL Android TV（非 Google TV，2019–2021 款为主）（约 2019–2021 款；北美 3-Series / 4-Series，欧洲 P / C 系，android Android TV 7 / 9 / 10 / 11）

- 认：首页是 Android TV 的横向「频道行」布局（Play 商店 + 一行行推荐），左上角是 Android TV 字样而不是 Google TV；Settings → Device Preferences → About 里能看到「Android TV OS build」；遥控器有 Google Assistant 麦克风键，但没有 Roku 紫色标识。
- 值不值：值得做：这代普遍 1.5GB/2GB 内存，Android TV 原生首页推荐 + TCL 自家频道一起拖慢，机龄 4 年以上，冻结收益明显。

**TCL 海外 Android TV 机型（标准 Android TV 路径）** · 证据：多源
1. Settings → Device Preferences → About
2. 滚到「Build」/「Android TV OS build」，连按 OK 键 7 次（部分机型要 8 次），直到提示 You are now a developer
3. 返回 Settings → Device Preferences → Developer options
4. 打开「USB debugging」/「ADB debugging」（部分机型叫 Network debugging）
5. 在网络设置里查到电视 IP，电脑执行 adb connect <TV_IP>:5555
6. 电视上会弹 Allow USB debugging 对话框，选 Allow（可勾 Always allow from this computer）
- 授权弹窗：有；端口 5555
- ⚠ Android 10 及以下才是固定 5555 端口的老式无线 ADB；这代机型多在此范围内。
- 来源：[Debugging（Google 官方：Settings → About → 连点 Build 多次成为开发者，再开 USB debugging）](https://developers.google.com/cast/docs/android_tv_receiver/debugging) unknown
- 来源：[Take Control of Your Android Phone (or TV) with These Tips（含 adb connect 与授权弹窗流程）](https://www.esper.io/blog/android-dessert-bites-2-tips-tricks-3156978) unknown
- 来源：[TCL series 4 AndroidTV：用户在电视上开 developer options + USB debug 后用 ADB 控制](https://community.home-assistant.io/t/tcl-series-4-androidtv-is-integrated-to-ha-and-i-can-control-it-via-adb-bridge-commands-i-can-turn-it-off-via-call-service-but-i-cant-turnin-back-on/362160) unknown
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（海外 debloat 脚本与指南主推 `pm disable-user --user 0 <pkg>`（可逆，`pm enable` 恢复）；`pm uninstall --user 0` 也可用但不可逆，只能靠 `pm install-existing` 碰运气。hide 在海外资料里没见实测。）
- 禁停（8）：`com.tcl.suspension`、`com.tcl.tv`、`com.tcl.tvinput`、`com.google.android.tv.remote.service`、`com.tcl.tcl_bt_rcu_service`、`com.tcl.autopair`、`com.android.location.fused`、`com.google.android.tvlauncher`
- 可停（30）：`com.tcl.bootadservice`、`com.tcl.channelplus`、`com.tcl.dashboard`、`com.tcl.esticker`、`com.tcl.exhibit`、`com.tcl.gamebar`、`com.tcl.hotelmenu`、`com.tcl.initsetup`、`com.tcl.interactive`、`com.tcl.logkit`、`com.tcl.partnercustomizer`、`com.tcl.repairguide`、`com.tcl.smartlink.core`、`com.tcl.useragreement`、`com.tcl.tv.plus`、`com.tcl.ui_mediaCenter`、`com.tcl.UpdatePeripheral`、`com.tcl.guard`、`com.tcl.browser`、`com.tcl.t_solo`、`com.tcl.tvweishi`、`com.tcl.usercenter`、`com.tcl.gallery`、`com.tcl.notereminder`、`com.tcl.messagebox`、`com.tcl.audioplayer`、`com.tcl.imageplayer`、`com.tcl.videoplayer`、`com.google.android.leanbacklauncher.recommendations`、`com.google.android.tvrecommendations`
- 看你用不用（9）：`com.tcl.miracast`、`com.tcl.smartalexa`、`com.google.android.gms`、`com.google.android.gsf`、`com.android.vending`、`com.netflix.ninja`、`com.netflix.tokenmanager`、`com.amazon.amazonvideo.livingroom`、`com.google.android.youtube.tv`
- 坑：同一个包名在不同机型/固件上表现不一致：com.tcl.waterfall.overseas 在个别欧版机型（如 55P635）上禁用会导致 Google TV logo 卡死无法开机，但在多数其他机型上可安全禁用。别照抄别人机型的清单，逐台小批量验证。
- 坑：包名的字面意思会骗人：com.tcl.suspension 看起来像可有可无的组件，实际控制信号源菜单，禁用即刻让遥控器「输入」键失灵。动手前用 `adb shell pm list packages` 取真实包名，不要凭名字猜用途。
- 坑：部分 TCL 机型的 Developer options 页面在非英文系统语言下滚不动 / 选不中，用户把系统语言临时改成 English 后恢复正常。
- 坑：禁停（包名未查到）：含 .inputmethod. 字段的包（如 Gboard） -- 屏幕软键盘 关掉后遥控器无法输入文字，搜索、登录账号会卡死。

### 海外 · TCL Google TV（2021 款 5/6-Series 起，含 Android TV 13/14 固件）（2021 年 8 月 TCL 首次把 Google TV 用在 5-Series / 6-Series 上，此后逐步铺开；Roku OS 机型同期继续在售，android Android 10 / 11 / 12 / 13 / 14（Google TV 界面层））

- 认：首页顶部是 Google TV 的「For you / Live / Movies / Shows / Apps / Library」标签栏，会按账号推荐影片；遥控器有 Google Assistant 麦克风键和 Google TV 品牌标，没有 Roku 紫色标；Settings → System → About 里写 Android TV OS build 且带 Google TV 版本号。
- 值不值：中等：内存普遍 2GB/3GB，首页推荐与赞助内容是主要负担，机龄 1–4 年。想彻底去广告通常要换第三方桌面（FLauncher / Projectivy），那一步比冻包名收益大得多。

**TCL Google TV，系统底层 Android 10 及以下** · 证据：多源
1. Settings → System → About
2. 找到「Android TV OS build」，连按 OK 键 7 次（部分机型 8 次），提示 You are now a developer
3. Settings → System → Developer options，打开「USB debugging」
4. adb connect <TV_IP>:5555
5. 电视上弹 Allow USB debugging，选 Allow
- 授权弹窗：有；端口 5555
- ⚠ Android 10 及以下才有固定 5555 端口的老式无线 ADB。
- 来源：[Debugging | Google Cast 官方文档（Settings → About → 连点 Build → USB debugging）](https://developers.google.com/cast/docs/android_tv_receiver/debugging) unknown
- 来源：[TCL Google TV: How To Enable Developer Options（Settings → System → About → Android TV OS build）](https://www.youtube.com/watch?v=uphHPwwZObc) unknown

**TCL Google TV，系统底层 Android 11 及以上（含 Android TV 13/14 固件）** · 证据：多源
1. Settings → System → About → 连点「Android TV OS build」7 次，成为开发者
2. Settings → System → Developer options → 打开「Wireless debugging」（无线调试），只开 USB debugging 已经不够
3. 在 Wireless debugging 页里选「Pair device with pairing code」，屏幕上会给出 IP、配对端口和 6 位配对码（关掉弹窗就作废，端口和码每次都变）
4. 电脑执行 adb pair <TV_IP>:<配对端口> 然后输入 6 位配对码
5. 配对成功后回到 Wireless debugging 主页读「连接端口」（与配对端口不同，随机分配），执行 adb connect <TV_IP>:<连接端口>
6. 电视上选 Allow / Always allow
- 授权弹窗：有；需配对码
- ⚠ 配对端口和配对码是一次性的，弹窗关掉就失效；连接端口在重启前不变。电脑和电视必须在同一 Wi-Fi 网段，一端走以太网一端走 Wi-Fi 常配对失败。
- 来源：[Android 12 Developer options adb Wireless debugging（Android 11 起改为 adb pair + 随机端口）](https://xdaforums.com/t/android-12-developer-options-adb-wireless-debugging-option-keeps-turning-off.4461375) unknown
- 来源：[A Complete Guide to Android ADB Wireless Debugging（配对端口与连接端口是两个不同端口）](https://gadgetguideonline.com/android/android-adb-wireless-debugging) unknown
- 来源：[Google Breaks ADB On Android TV 14 - Lets FIX IT NOW（Android TV 14 上必须开 Wireless debugging 并出配对码）](https://www.youtube.com/watch?v=WVRBXktSw0c) unknown
- 命令能力：disable-user=yes · hide=unknown · uninstall-user0=yes（与 Android TV 那代一致；Google TV 上换桌面的标准命令是 `pm disable-user --user 0 com.google.android.apps.tv.launcherx`（Android TV 则是 com.google.android.tvlauncher）。）
- 禁停（5）：`com.google.android.apps.tv.launcherx`、`com.tcl.suspension`、`com.android.location.fused`、`com.google.android.tv.remote.service`、`com.tcl.autopair`
- 可停（7）：`com.tcl.waterfall.overseas`、`com.tcl.overseas.waterfall`、`com.tcl.bootadservice`、`com.tcl.guard`、`com.tcl.browser`、`com.tcl.tv.plus`、`com.tcl.channelplus`
- 看你用不用（5）：`com.google.android.gms`、`com.android.vending`、`com.netflix.ninja`、`com.amazon.amazonvideo.livingroom`、`com.google.android.youtube.tv`
- 坑：Android TV 14 之后 USB debugging 单独打开已经连不上了，必须开 Wireless debugging 并走 adb pair 配对码流程；很多老教程和一些家庭自动化集成（如 Home Assistant ADB 集成）还停留在 5555 端口，会一直报 Failed to connect。
- 坑：不想动 ADB 的话，Google TV 机型有官方的广告收敛开关：首页 → Settings → Privacy → Ads → Delete Advertising ID（也可重置）。TCL 官方支持页明说「广告无法完全关闭，但可以控制广告 ID 怎么被用」。
- 坑：部分 TCL 机型的 Developer options 在非英文系统语言下无法滚动或选中，临时把系统语言改成 English 可绕过。

### 海外 · TCL Roku TV（ADB 不适用）（2014 年起在北美等市场持续在售；2021 年 TCL 增加 Google TV 机型后，Roku OS 机型仍继续销售，两条线长期并存，roku 不适用——Roku OS 不是 Android，没有 Android 版本号）

- 认：遥控器上有紫色 Roku 标（多数还带 Netflix / Disney+ 等一排彩色快捷键）；首页是左侧竖排菜单 + 右侧方块频道网格的 Roku 界面；设置里写「Roku OS 版本」，整个「关于」页没有任何 Android 字样；开机画面是 Roku 的紫色 logo。
- 值不值：不值得折腾 ADB——根本没有这个东西。Roku OS 机型卡顿主要靠减少频道和关掉推荐来缓解；真嫌慢就外接一个便宜的流媒体盒子从 HDMI 用。
- 命令能力：disable-user=no · hide=no · uninstall-user0=no（Roku OS 不是 Android，没有 adb、没有 pm、没有 package 概念。Roku 官方的「developer mode」是另一回事：在电视上用遥控器按特定序列打开后，通过浏览器访问电视 IP、用 rokudev 账号登录，进入 Development Application Installer 侧载 Roku 频道（.zip），只能装 Roku 频道，不能列出或停用系统组件。）
- 坑：替代动作（这一代唯一能做的事）：设置 → Privacy → Advertising → Limit Ad Tracking 打开；设置 → Privacy → Advertising → Reset Advertising Identifier 重置广告 ID；设置 → Privacy → Smart TV Experience → Use Info from TV Inputs 关掉（这一条是 ACR 自动内容识别，关掉能挡住电视扫描你在 HDMI 上看什么）。首页推荐条则在 Home screen 的设置里减少频道来收敛。
- 坑：买之前认平台：同一个 TCL 型号系列（4-Series / 5-Series / 6-Series）在北美同时有 Roku TV 和 Google TV 两个版本，硬件接近但系统完全不同——Google TV 版能做 ADB，Roku 版做不了。认遥控器上的紫色 Roku 标最快。
- 坑：社区有对 Roku 设备做 root / 越狱的讨论，本项目不采用。

**待验证 · 欢迎投稿**

- 国行灵控系统 3.0 / 3.5 下的广告与推荐组件到底叫什么包名？目前国行有实测回显的只有 2021 年瀑布式那代的 com.tcl.bootadservice / com.tcl.article.news / com.tcl.ffeducation / com.tcl.MultiScreenInteraction_TV / com.tcl.versionUpdateApp / com.tcl.update 六个；2024 年后的灵控机型没有找到任何一份 `pm list packages` 的公开输出可比对。
- com.tcl.suspension、com.tcl.autopair、com.tcl.tcl_bt_rcu_service 这些「不可停」包名在国行灵控系统上是否同样存在、行为是否一致，完全没有国行实测记录——本文件已把它们标为海外证据，不要当成国行已验证结论向用户陈述。
- 国行 TCL / 雷鸟是否有标准 Android 11+ 的「无线调试 + 配对码」流程？查到的国行资料全部只有「本机信息 → 上下左右 → ADB 开关 → 5555」，没有任何国行配对码流程的截图或步骤记录。
- 「设置 → 关于本机 → 连点版本号 7 次 → 开发者选项」与「本机信息 → 上下左右」两条国行路径的机型分界仍未确定。2026-03 的值得买文章把两条并列为 TCL 的可选路径，但没说哪些型号走哪条，也可能只是把通用 Android TV 教程一并抄录了。
- 「商场模式」是否是所有国行固件开 ADB 的必经前置？2026 年的两篇教程提到要先打开商场模式，2021–2025 年的教程都只说按完上下左右就能看到 ADB 开关——不确定是新固件的变化，还是不同机型的差异。
- 国行「恢复出厂 / 工厂组件」和「天气」这两个致命包的实际包名未查到，目前只能给用户「名字里带 factory / reset / recovery / weather 的先别动」这种模糊指引。
- 「灵控桌面 3.5」只有一条视频标题提到（称 2026 Q1 大版本），没有官方公告或第二来源，发布时间与版本变化均不可采信。
- 工厂菜单路径（信源 → 模拟接收 → 对比度 → 1950）的适用型号清单未查到，只知道限于遥控器带数字键的机型（偏老款与部分海外机），国行现售机型是否仍保留该入口未经验证。
- TCL 海外 Roku TV 在中国大陆没有销售，本文件的 Roku 分支只服务海外用户；国内不会遇到这一支。

## 荣耀智慧屏 · 不适用

荣耀官方客服两次公开答复「智慧屏系列产品不支持 ADB 命令」，开发人员选项里只有演示模式一类选项，没有 USB 调试 / ADB / HDC 开关；X1 上曾经出现过的 HDC 开关也在 2024 年的一次系统升级后被下线。网上能搜到的「荣耀智慧屏开 ADB 教程」对应的是升级前的老固件，现在的机器照着做会发现开关根本不存在。好消息是荣耀智慧屏本来就以「开关机无广告」为卖点，不做也损失不大。

**怎么认机型**

- remote_has_digits：未核实：未找到荣耀官方对智慧屏遥控器按键的说明
- system_name_shown_at：智慧屏主页 → 设置 → 系统 → 关于本机（或「客户服务支持」），可看当前版本信息（荣耀官方手册）
- about_page_fields：系统版本（用户自述形如「鸿蒙 1.0.1」）；设备型号：设置 → 系统 → 关于本机 → 设备型号；序列号 SN：设置 → 系统 → 关于本机 → 序列号
- model_naming_rule：产品名直接叫「荣耀智慧屏 X1 / X2 / X3 / X3i / 荣耀智慧屏 5」，官网按这个名字分产品页；未找到荣耀官方公布的智慧屏内部型号编码规律
- sticker_location：产品包装上，或智慧屏背面的产品铭牌上（荣耀官方手册明确写了这两处）
- year_ranges：2019-08 荣耀智慧屏 / Pro 首发（当时荣耀仍属华为）→ 2020-11 荣耀独立 → 2021 起 X1/X2/X3/X3i 延续至今，2026 年在售的是荣耀智慧屏 5 与 X3 系列

**为什么不适用**：荣耀客服（「荣耀答答团」）在 2024-02-14（X2 提问）与 2024-10-21（X1 提问）两次公开答复：智慧屏系列产品不支持 ADB 命令。用户实测进入【设置 → 系统和更新 → 开发人员选项】后，里面只有演示模式等少数选项，没有 USB 调试 / ADB / HDC 任何一个开关。更关键的是 X1 用户在 2024-03-25 反馈：早期「鸿蒙 1.0.1」固件上原本有【HDC 调试】开关，系统升级之后这个开关直接消失了——也就是说这是厂商主动收掉的能力，不是没找到入口。

**替代动作**：荣耀智慧屏从首发就主打「开关机无广告」（2019 年发布会宣传口径），系统层面没有开机广告需要关。剩下能做的是这几件，都在遥控器上完成，不需要电脑：① 关掉升级弹窗：设置 → 系统 → 自动更新，按确认键关闭（荣耀官方手册）；② 关个性化推荐：应用市场 → 我的 → 设置 → 内容推荐服务 → 关闭个性化推荐（🔴 这条是荣耀官方针对应用市场写的通用说明，智慧屏端是否同一路径未核实，进去找不到就跳过）；③ 首页那些推荐位/内容卡片有没有关闭开关，没找到任何官方说明，见 open_questions。

### 荣耀智慧屏 / 荣耀智慧屏 Pro（首发代，鸿蒙 1.0）（2019-08，harmony 华为未公开；这批机器与同期华为智慧屏同源，底层是 Android/AOSP 分支，2019 年知乎上有过「荣耀电视系统设置里有 ADB 调试」的讨论（原帖正文未抓取成功，只能确认讨论存在））

- 认：2019 年买的、带升降 AI 摄像头、开机没有广告、当时宣传语是「不是电视，是电视的未来」；那会儿荣耀还是华为子品牌（2020-11 才独立）
- 值不值：机龄 7 年，硬件确实老；但没有可用的 ADB 入口，卡顿只能靠少装应用和恢复出厂设置缓解

### 荣耀智慧屏 X1（含 X1 4G 内存版 2022 款）（2020-2022（2024 年仍在用），harmony 用户自述固件标注为「鸿蒙 1.0.1」，长期停在早期版本号；荣耀未公开对应的 Android 版本）

- 认：关于本机里系统版本显示 1.0.x；官网产品线里单列「荣耀智慧屏 X1 4G 内存版(2022款)」
- 值不值：🔴 连不上：早期固件上那个【HDC 调试】开关已被升级下线，升级后的机器开发人员选项里空空如也。X1 标准版内存偏小，但没有可用的清理通道
- 坑：🔴 X1 用户 2024-03-25 发帖：「鸿蒙 1.0.1」时期机器上是有【HDC 调试】开关的，升级系统之后开关没有了、adb 也打不开；同一串帖子在 2024-10-21 由荣耀官方回复「智慧屏系列产品不支持 ADB 命令」收尾。所以网上早年的荣耀开 ADB 教程不是假的，只是对应的固件已经被官方升级抹掉了——照着做找不到开关属于正常现象，不是操作失误

### 荣耀智慧屏 X2 / X3 / X3i / 荣耀智慧屏 5（独立后的 TV 固件）（2021-2026，harmony 荣耀未公开；2020-11 荣耀独立后固件路线与华为智慧屏分道，不能拿华为智慧屏的结论套用）

- 认：2021 年以后买的荣耀电视，官网产品页在「智慧屏」分类下；开发人员选项能打开，但里面只有演示模式一类选项
- 值不值：🔴 不用做：没有 ADB 入口；荣耀智慧屏产品线延续「开关机无广告」定位，能关的都在遥控器可达的设置里

**待验证 · 欢迎投稿**

- 荣耀智慧屏首页的推荐位 / 内容卡片有没有官方的关闭开关（类似手机端的「个性化推荐」），在荣耀官网智慧屏支持文档里没找到对应条目
- 应用市场里那条「内容推荐服务 → 关闭个性化推荐」的路径在智慧屏端是否存在（官方文档是按手机端写的，未核实 TV 版）
- 荣耀客服 2024 年的「智慧屏系列产品不支持 ADB 命令」是针对 X1/X2 的答复，是否覆盖 2019 年首发代和 2025-2026 年的荣耀智慧屏 5，没有覆盖全系的官方文档，只能当强证据
- 2019 年荣耀还是华为子品牌时首发的荣耀智慧屏/Pro，固件上是否确实支持标准 ADB（知乎原帖正文抓取失败）
- 荣耀是否提供 ADB/HDC 之外的官方调试或应用管理通道（如荣耀开发者平台的远程真机），未查到文档

## LG · 不适用

LG 电视全线用自家的 webOS，不是 Android，没有 adb、没有 pm 命令，本项目的清理流程一条也用不上。但 LG 的广告与跟踪开关是所有品牌里最齐的：首页推广位、屏保广告、ACR（Live Plus）、广告 ID 四组都能在设置里关掉，值得逐条做一遍。

**怎么认机型**

- remote_has_digits：标志性的是「Magic Remote」魔法遥控：手柄细长、中间一个可滚动的滚轮，挥动遥控屏幕上会出现一个圆形光标（像用鼠标一样点电视）。有数字键的版本和没有的版本都存在，光标才是 LG 的硬特征。
- system_name_shown_at：设置 → 全部设置 → 常规 → 关于本机（About This TV）；这一页显示 webOS 版本，不会出现任何 Android 字样。
- about_page_fields：型号 / Model；软件版本（webOS 版本，形如 webOS 23 / 24 / 25）；用户协议（User Agreements）入口；序列号
- model_naming_rule：型号形如 OLED65C4PUA 或 65QNED85TUA：OLED / QNED / NANO / UT / UQ 是产品线；数字段里的两位是尺寸；紧跟产品线档位后的那个字母是年份代际（…=2020 N、2021 P、2022 Q、2023 R、2024 S/T、2025 U 一类，LG 每年换一个字母且不完全规则），最后两三位是市场后缀（PUA 北美、PCA 国行、ALA 欧洲等）。想快速判年份最稳的办法不是猜字母，是开机看关于页里的 webOS 版本。
- sticker_location：机身背面铭牌贴纸印 Model 与 Serial；壁挂后看不到就走设置 → 全部设置 → 常规 → 关于本机。
- year_ranges：webOS 自 2014 年起用于全部 LG 智能电视，至今未变；2014 年之前是 LG 自研的 NetCast（同样不是 Android，那一代的 ACR 开关叫 LivePlus，在 设置 → 选项 里）。

**为什么不适用**：LG 电视用 webOS（LG 自研，基于 Linux 的系统），不是 Android，因此不存在 adb、开发者选项里的 USB 调试、pm disable-user 这一整套东西。LG 官方有一个面向应用开发者的 Developer Mode 应用（配合 webOS TV SDK 推自己写的应用上电视），性质与三星 Tizen 的开发者模式相同——它是给开发者调试自己的应用用的，不提供操作系统预装应用的能力，本项目不使用、也不建议普通用户开。

**替代动作**：LG 的可关项是各品牌里最多的，建议四组全做一遍（菜单措辞随 webOS 大版本略有出入，按语义找）：

【1. ACR 跟踪：Live Plus】设置（遥控齿轮键）→ 全部设置 → 常规 → 系统 → 附加设置（Additional Settings）→ 把「Live Plus」关掉。这就是 LG 的自动内容识别，开着时电视会持续识别屏幕上正在播什么（包括 HDMI 接的机顶盒、游戏机）并据此推荐和投广告。LG 自己在设置里的说明写得很直白：「开启 Live Plus 即表示您理解电视上显示的内容可被识别，观看信息可用于提供内容推荐和广告」。

【2. 首页推广位与推荐】同一层：常规 → 系统 → 附加设置 → 主屏设置（Home Settings）→ 关掉「主页推广（Home Promotion）」和「内容推荐（Content Recommendation）」。关掉后首页顶部那条大横幅广告会变成一个普通的「转到应用」按钮。同一页的「开机画面（Power On Screen）」可以从「主页」改成「最近的输入源」，这样开机直接进上次用的 HDMI 口，不再弹首页。

【3. 广告 ID】附加设置 → 广告（Advertisement）→ 打开「限制广告跟踪（Limit Ad Tracking）」→ 再点「重置广告 ID」清掉已积累的画像。

【4. 屏保广告】附加设置 / 主屏设置同层里找「屏幕保护程序促销（Screensaver Promotions）」关掉，去掉待机屏保上的广告。

【5. 用户协议里的勾选项】设置 → 支持 → 隐私与条款（Privacy & Terms）→ 用户协议（User Agreements）→ 取消「观看信息协议 / Viewing Information」「个性化广告 / Personalized Advertising」「语音信息 / Voice Information」「营销通讯」四项（使用条款和隐私政策是必选的，取消不了，别在那两条上较劲）。同一层的「删除我的观看信息 / Delete My Viewing Information」可以清掉电视已经存下的历史。部分地区还有「不出售我的个人信息」开关。

【旧机型】webOS 之前的 NetCast 机型：设置 → 选项 → 关掉 LivePlus。

代价要讲清楚：关掉 Live Plus 之后首页那一排「猜你在看」的推荐会整个消失；即使全关，LG 仍会在首页放通用（非个性化）的推广位，那部分没有开关。

### webOS（2014 至今，全线）（2014至今，webos 不适用）

- 认：开机后主屏底部弹出一条横向的应用磁贴带（Launcher Bar），挥动 Magic Remote 屏幕上出现圆形光标。设置页里找不到任何「Android 版本」「Build number」字样，关于本机显示的是 webOS 版本号。
- 值不值：不适用：不是安卓，本项目流程用不上。不过上面那五组设置开关关完，首页和屏保的观感变化比很多安卓机做 ADB 清理还明显，LG 用户建议优先做这个。
- 坑：菜单层级在 webOS 22 / 23 / 24 / 25 之间会挪位置：同一个 Live Plus 开关，不同资料分别写成「常规 → 系统 → 附加设置」和「常规 → 高级设置」。按语义找（先进「常规」，再找带「系统」或「附加/高级」字样的那一层），不要因为路径对不上就以为自己的机器没这个开关。
- 坑：社区有教程说改电视的地理位置区域、或在初始设置里退掉非必选条款可以减少个性化推荐。这类做法可能触发电视重启、并且效果与直接关 Live Plus 不等价，本项目不推荐，优先走上面的正规开关。

**待验证 · 欢迎投稿**

- 国行 LG 电视的广告形态与菜单路径是否与欧美一致，未找到国行机型的实拍确认；现有来源全部来自北美与欧洲市场。
- webOS 22 / 23 / 24 / 25 各版本之间「附加设置 / 高级设置」这一层的确切命名，未逐版本核实，本文件按语义描述处理。
- LG 在 2026 年通过 webOS 更新向全部机型自动安装 Copilot 的做法（有报道提及）之后，这个应用能不能在设置里禁用或卸载，未核实。

## Roku TV（含 TCL / 海信 / 夏普 / 飞利浦 / RCA 等贴牌机型） · 不适用

Roku TV 用的是 Roku OS，不是 Android，没有 adb、没有 pm 命令，本项目的清理流程一条也用不上。🔴 最需要提醒的一点：TCL、海信、夏普、飞利浦这几个牌子同时卖 Android/Google TV 版和 Roku 版，中国大陆基本见不到 Roku 版、北美非常常见——所以品牌对得上不代表系统对得上，必须先看主屏和遥控。

**怎么认机型**

- remote_has_digits：Roku 遥控是本项目里最好认的：没有 0–9 数字键，正中间一个紫色的圆形方向盘（Roku 的商标性紫色 d-pad），下方一排四个彩色的流媒体快捷键（Netflix、Disney+ 等，具体几个随年份变）。看到紫色圆盘基本可以直接定性。
- system_name_shown_at：设置（Settings）→ 系统（System）→ 关于（About）——这是 Roku 官方给的查法。
- about_page_fields：Model —— 显示 Roku 的机型号；Roku OS 版本；Roku TV support —— 这一行写的是实际制造商（TCL / 海信 / 夏普 / 飞利浦…）的售后联系方式，是判断「谁造的这台机器」最直接的证据；序列号
- model_naming_rule：Roku 自有品牌的电视和贴牌机型型号规则不统一，各家按自己的体系走（夏普的 LC-43LBU591C、飞利浦的 55PUL7875/F7 等）。判定是不是 Roku TV 不靠型号，靠主屏和遥控。
- sticker_location：各家贴牌机型的铭牌位置不同（背面、侧面接口区、底边），但 Roku 官方给的可靠办法是走 设置 → 系统 → 关于。
- year_ranges：Roku TV 自 2014 年起由多家厂商贴牌生产至今；Roku 自有品牌电视自 2023 年起加入。中国大陆市场没有 Roku TV 正式渠道。

**为什么不适用**：Roku OS 是 Roku 自研的封闭系统（基于 Linux，非 Android），没有开发者选项里的 USB 调试、没有 adb、没有 pm。Roku 确实有面向开发者的 Developer Settings（主屏连按 首页×3、上×2、右、左、右、左、右 进入，用于把自己写的频道侧载到电视上调试），但那是 Roku 自己的 BrightScript 频道开发通道，不提供任何操作系统预装应用的能力，与本项目要做的事没有关系。

**替代动作**：Roku 的可关项集中在 设置 → 隐私 一棵树下，做三件事：

【1. ACR：关掉「Use Info from TV Inputs」】按遥控 Home 键 → 设置（Settings）→ 隐私（Privacy）→ 智能电视体验（Smart TV Experience）→ 取消勾选「使用来自电视输入源的信息（Use Info from TV Inputs）」。这是 Roku 的自动内容识别，开着时会对 HDMI、天线、USB 等所有信号源做音频指纹和画面识别。较新的 Roku OS 上这一项直接标成「Automatic Content Recognition (ACR)」，同页还有一个「Content Viewing Disclosure」也一并取消。

【2. 广告 ID】设置 → 隐私 → 广告（Advertising）→ 勾上「限制广告跟踪（Limit Ad Tracking）」；较新版本这一项叫「个性化广告（Personalized ads）」，取消勾选即可。

【3. 麦克风】设置 → 隐私 → 语音（Voice）→ 频道麦克风访问（Channel Microphone Access）→ 选「从不允许（Never Allow）」，阻止所有频道调用遥控或手机 App 上的麦克风。

还有一个 隐私选择（Privacy Choices）页，可以选择不出售 / 不共享个人数据、限制敏感信息使用。

三条边界必须讲清楚：
① 关掉 ACR 会连带关掉「Enable Auto Notifications」，也就是 Roku 的「More Ways to Watch」（正在播的节目提示你从头看、或告诉你还有哪几集）会一起失效；
② 关掉 ACR 只挡住「电视输入源」这一路，Roku 仍然会收集你在 Roku 上用了哪些频道 / 流媒体应用的数据；
③ 已经采集走的历史数据删不掉。

### Roku OS（全部 Roku TV 贴牌机型与 Roku 自有品牌电视）（2014至今，roku 不适用）

- 认：主屏是九宫格式的应用图标网格（每个频道一个方块磁贴），左侧一列是 Home / 设置等菜单；不是 Android TV 的横向卡片行，也不是 Google TV 的海报流。遥控紫色圆盘。机身正面通常是制造商的牌子（TCL / Hisense / SHARP / PHILIPS），右下角另有一个 Roku TV 小标；Roku 自有品牌的机器则是正面正中下方一个 Roku 标。
- 值不值：不适用：不是安卓，本项目流程用不上。Roku OS 本身轻量、在低配硬件上也不太卡，主要困扰是首页广告位和 ACR，走上面的设置开关处理。
- 坑：🔴 同一个电视品牌下 Roku 版和 Android/Google TV 版并存，这是本项目最容易出错的地方：TCL 在北美同时卖 TCL Roku TV、TCL Google TV、TCL Fire TV 三种；海信、夏普、飞利浦也都有 Roku 版。所以「我家是 TCL / 海信」这句话不足以定系统。先按遥控（有没有紫色圆盘）和主屏（九宫格 vs 卡片行）判一次，再去 设置 → 系统 → 关于 核实。TCL 官方支持页就是按 Roku TV / Google TV / Fire TV 三套分别给查型号步骤的。
- 坑：Roku 有一个「secret menu」/ 服务菜单（遥控按键序列进入），网上有不少视频教怎么开。那是面板与出厂参数菜单，不提供任何清理预装应用的能力，反而有改花屏的风险。本项目不使用。

**待验证 · 欢迎投稿**

- Roku OS 新旧版本里这两项的命名不一致（老版「Use Info from TV Inputs」/ 新版「Automatic Content Recognition (ACR)」+「Content Viewing Disclosure」；老版「Limit Ad Tracking」/ 新版「Personalized ads」），具体从哪个 Roku OS 大版本开始改名，未核实。
- Roku 开发者模式（Developer Settings）的按键序列本文件按公开资料记述，未实机验证；它与本项目无关，不建议用户去开。
- 中国大陆没有 Roku TV 正式销售渠道，本文件对国行用户的意义主要是「排除」——确认自己不是这一类。海淘 / 水货 Roku TV 在国内的可用性未核实。

## 三星 · 不适用

三星电视全线用自家的 Tizen 系统，不是 Android，没有 adb、没有 pm 命令，本项目的清理流程一条也用不上。三星确实有「开发者模式」，但那是给 Tizen 应用开发者装自己写的 .wgt 应用用的，动不了系统预装。能做的只有关 ACR 跟踪和个性化广告。

**怎么认机型**

- remote_has_digits：近年三星标配的是短款「One Remote」智能遥控：没有 0–9 数字键，只有一圈方向盘、麦克风键和 Netflix / Prime Video / Disney+ 等彩色快捷键；中低端和早期机型另配带数字键的长遥控。遥控背面通常是太阳能充电板（2021 年起的 SolarCell Remote）。
- system_name_shown_at：设置 → 支持 → 关于本机 / Terms & Privacy；主屏左下角的 Smart Hub 横条本身就是 Tizen 的标志性界面。
- about_page_fields：型号代码（Model Code）；软件版本 / Software Version（形如 T-xxxxxxx，不是 Android 版本号）；Smart Hub 条款与隐私政策入口
- model_naming_rule：型号形如 QN65QN90DAFXZA 或 UA65CU8000JXXZ：第 3–4 位是尺寸，紧接着是产品线代号（QN=Neo QLED、Q=QLED、CU/AU/TU=UHD LCD、S9x=OLED），再后面一位字母是年份代际（…U=2025、D=2024、C=2023、B=2022、A=2021、T=2020），最后是市场后缀（FXZA 北美、JXXZ 国行、XXU 英国等）。想快速判年份就看产品线代号后面那个字母。
- sticker_location：机身背面右下角或接口区旁的铭牌贴纸印 Model Code 与 Serial；壁挂后看不到就走设置 → 支持 → 关于本机。
- year_ranges：Tizen 自 2015 年起用于全部三星智能电视，至今未变；2015 年之前是三星自家的 Orsay / Smart Hub（同样不是 Android）。

**为什么不适用**：三星电视用 Tizen（三星自研、基于 Linux 的系统），不是 Android，因此不存在 adb、开发者选项里的 USB 调试、pm disable-user 这一整套东西。🔴 需要特别说明的一点：Tizen 电视确实有「开发者模式」，进法是 Apps → App Settings（或主屏按 Apps 后用遥控数字键输入 12345）→ 打开 Developer mode → 填入电脑的 IP → 长按电源键 2 秒重启。但这个模式的用途是让开发者把自己打包的 .wgt 网页应用通过 Tizen Studio / sdb 推到电视上调试（Tizen Studio 里的 Device Manager、Web Inspector 这一套），它既不是 adb，也不提供任何操作系统预装应用的能力——三星官方开发者文档里关于卸载的部分只讲「通过 Tizen Studio 卸载你自己装的那个应用」，失败时让你去电视的 Apps 面板里手动删，压根没有触及系统应用的接口。所以不要因为看到「三星也有开发者模式」就以为能照本项目的流程做。

**替代动作**：能做的是关掉 ACR 跟踪和个性化广告，三条开关，按机型年份分：

【2022 年及以后的机型】主屏 → 光标移到左侧边栏 → 「隐私选择（Privacy Choices）」→ 「条款和条件、隐私政策（Terms & Conditions, Privacy Policy）」→ 取消勾选「查看信息服务（Viewing Information Services）」，这一项就是三星的 ACR，关掉之后电视不再对 HDMI、有线盒子、游戏机、天线等所有信号源做画面指纹识别 → 同页再取消勾选「基于兴趣的广告（Interest-Based Advertisements）」→ 拉到页面底部按「确定」保存。另一条等价入口：设置 → 常规与隐私 → 条款和隐私。

【2025 年机型】隐私选择下会多出「隐私中心」「安全中心」两项，前两个勾选框位置不变；页面底部另有「不出售或共享我的个人信息（Do Not Sell or Share My Personal Information）」。

【2021 年及更早的机型】设置 → 支持 → 条款和政策（Terms & Policy）→ 关掉「查看信息服务」「基于兴趣的广告」「语音识别服务」；更老的 Smart Hub 机型这一页叫「SyncPlus 和营销」。

【语音】设置里的「语音识别服务」关掉后就彻底没有语音功能了——三星不提供「要语音但不上传」的选项，这一条要问过人再动。

注意边界：关掉这些只是停止「个性化」和画面指纹采集，Smart Hub 首页仍然会出现三星自家应用和产品的横幅推广位，那部分在系统里没有开关。

### Tizen（2015 至今，全线）（2015至今，tizen 不适用）

- 认：开机后主屏底部是一条横向的 Smart Hub 带（左侧一列图标：搜索、Ambient、游戏、媒体、菜单），不是 Android TV 的卡片行、也不是 Roku 的九宫格。设置页里找不到任何「Android 版本」「Build number」字样，软件版本形如 T-KTMAKUC-1520.3。遥控是短款 One Remote。
- 值不值：不适用：不是安卓，本项目流程用不上。真想换掉体验只能外接一个安卓电视盒子，把三星当显示器用。
- 坑：🔴 Tizen 的「开发者模式」不等于 ADB。网上有大量「三星电视开发者模式怎么开」的教程（Apps → App Settings → 输入 12345 → 打开 Developer mode → 填电脑 IP → 长按电源键重启），照着做能开，但开完之后你能用的是 Tizen Studio 和 sdb，用途是往电视里推自己打包的 .wgt 网页应用，动不了系统预装的一个包。别在这上面花时间。
- 坑：有用户在路由器 / DNS 层面屏蔽 samsungads.com、ads.samsung.com 来拦截广告请求。这属于网络层手段，不是电视系统里的开关，也不在本项目范围内；会不会影响 Smart Hub 的正常功能（比如应用商店列表加载）没有可靠结论，本项目不推荐。

**待验证 · 欢迎投稿**

- 国行三星电视的隐私菜单命名与路径是否与北美 / 欧版完全一致，未找到国行机型的实拍确认；现有来源全部来自北美与欧洲市场。
- 关掉「查看信息服务」和「基于兴趣的广告」之后，Smart Hub 首页那些三星自家产品的横幅推广位到底还剩多少，没有前后对比的实测记录。
- 2015 年以前的 Orsay / 旧版 Smart Hub 机型（非 Tizen）的隐私开关位置，只查到 FlatpanelsHD 一句「设置 → 支持 → 条款和政策 → 关掉 SyncPlus 和营销」，未逐版本核实。

## Vizio · 不适用

Vizio 用自家的 SmartCast / VIZIO OS，不是 Android，没有 adb、没有 pm 命令，本项目的清理流程一条也用不上。Vizio 是最早被 FTC 罚过「未经告知采集观看数据」的电视厂商，它的 ACR 叫 Viewing Data（早期叫 Smart Interactivity），设置里能关，而且关掉会触发删除该设备已存的观看历史。仅北美等海外市场销售，国行见不到。

**怎么认机型**

- remote_has_digits：Vizio 遥控普遍是长条形、带 0–9 数字键，下方一排流媒体快捷键（WatchFree+、Netflix、Prime Video 等）；部分年份的遥控正面印着 VIZIO 字样和一个 V 键。没有 Roku 那种紫色圆盘，也没有 Google 助理麦克风键（Vizio 靠外部的 Alexa / Google Home 设备控制，不做机内语音助手）。
- system_name_shown_at：菜单（Menu）→ 设置 → 全部设置（All Settings）→ 系统 / 管理与隐私；主屏顶部的 SmartCast 横条本身就是标志。
- about_page_fields：型号 / Model；系统版本（SmartCast / VIZIO OS 版本，不是 Android 版本号）；序列号；隐私与法律（Privacy & Legal）/ 管理与隐私（Admin & Privacy）入口
- model_naming_rule：型号形如 M65Q7-J01、V505-J09、P75Q9-H1：开头字母是产品线档位（V=入门 V-Series、M=中端 M-Series、P/PQ=高端 P-Series、OLED=OLED），紧接着的数字是尺寸，Q 表示量子点，最后一位字母是年份代际（…H=2020、J=2021、K/L 依次后推），末尾数字是版本号。
- sticker_location：机身背面铭牌贴纸印 Model 与 Serial。
- year_ranges：SmartCast 自 2016 年起用于 Vizio 智能电视，2023 年前后官方逐步改称 VIZIO OS；2016 年之前是 Vizio Internet Apps / Vizio Internet Apps Plus（部分早期机型基于 Google TV，但那是 2012 年前后的极小一批，早已停产停更）。2024 年 Vizio 被沃尔玛收购。

**为什么不适用**：Vizio 的 SmartCast / VIZIO OS 是封闭自研系统（非 Android），没有开发者选项、没有 USB 调试、没有 adb、没有 pm，也没有对外开放的应用侧载通道——连三星 Tizen、LG webOS 那种「给开发者调试自家应用」的模式都没有公开提供。本项目的整套流程在 Vizio 上无从下手。

**替代动作**：Vizio 的可关项就两组，但都要做，而且新老机型菜单位置不同：

【1. ACR：Viewing Data】
· 新机型（VIZIO OS）：菜单 → 设置 → 全部设置（All Settings）→ 隐私与法律（Privacy & Legal）→ 观看数据（Viewing Data）→ 关掉。
· 略早的机型：全部设置 → 管理与隐私（Admin & Privacy）→ 观看数据 → 关掉。
· 更老的机型：按遥控 MENU 键（或打开 HDTV Settings 应用）→ 系统（System）→ 重置与管理（Reset & Admin）→ 光标停在「观看数据（Viewing Data）」或旧称「智能交互（Smart Interactivity）」→ 按右方向键改成 Off。
Vizio 官方隐私政策明确写了两件好事：关掉 Viewing Data 不影响电视和任何在线服务的性能；关掉会触发删除该设备上已有的 Viewing Data 历史日志。

【2. 广告与活动数据的边界（这条是坏消息，必须讲）】Vizio 官方同时写明：关掉 Viewing Data 不会停止 Activity Data 的采集——那是你在 SmartCast 首页和 WatchFree+ 页面上点了什么、搜了什么，用于推荐内容和在部分应用内投放定向广告。Activity Data 在设置里没有独立开关，官方给的唯一办法是把电视恢复出厂设置（Factory Reset）。而且初始设置时如果拒绝 Activity Data，SmartCast 的音视频流媒体功能就不能用了——也就是说这一项实际上是绑定的。

【3. 恢复出厂后要重做】官方提醒：任何一次恢复出厂之后，设备回到默认状态，Viewing Data 会被重置为 OFF（这一条对用户有利），但你之前做的其他设置也都没了，记得重新检查一遍。

实在找不到 Viewing Data 这一项时，Vizio 官方给了联系方式：privacy@vizio.com 或 1-855-472-8817。

### SmartCast / VIZIO OS（2016 至今，全线）（2016至今，other 不适用）

- 认：开机后主屏顶部是 SmartCast 的横向频道带，首页固定有一块 WatchFree+ 的免费频道入口（Vizio 自家的免费直播流媒体，这是 Vizio 最独特的界面特征）。设置树里找不到任何「Android 版本」「Build number」字样，也没有 Google Play。遥控是带数字键的长条，无语音麦克风键。
- 值不值：不适用：不是安卓，本项目流程用不上。而且 Vizio 的商业模式高度依赖广告与观看数据（Inscape 数据业务），机内可关的项目本身就是被设计成有限的。
- 坑：🔴 Vizio 的「关掉 Viewing Data」不等于「关掉全部采集」。官方白纸黑字写着 Activity Data（你在 SmartCast 首页与 WatchFree+ 上的点击、搜索行为）不受这个开关影响，也没有独立开关，唯一办法是恢复出厂；而初始设置时拒绝 Activity Data 会导致 SmartCast 的流媒体功能不可用。给用户交代这条设置时必须同时说明这个边界，不要说成「关了就不采集了」。
- 坑：同一个开关在不同年份的机器上有三个位置、两个名字（Smart Interactivity → Viewing Data；Reset & Admin → Admin & Privacy → Privacy & Legal）。按名字找不到就按语义找，三条路径都试一遍。

**待验证 · 欢迎投稿**

- 2012 年前后有极小一批 Vizio 机型基于 Google TV（Co-Star 系列与少数电视），理论上是 Android。这批机器早已停产停更、存量极少，没有去核实它们的开发者选项与 ADB 情况，本文件按「不适用」整体处理。
- 2024 年沃尔玛收购 Vizio 之后，隐私菜单与数据政策是否有变化，未核实；本文件引用的官方隐私政策抓取于 2026-09-15。
- 中国大陆没有 Vizio 正式销售渠道，本文件对国行用户的意义主要是「排除」。
