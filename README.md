# sansheng-tv-adb · 让 AI 替你清理电视

> **把一段话发给 AI，它连上你家电视，去掉开机广告、停掉预装应用、腾出内存——不卸载、不 root，每一步都能原样还原。**

**中文** | [English](./README_EN.md)

一台 2017 年的海信电视，2 GB 内存，清完开机广告没了、可用内存从 551 MB 涨到 988 MB，我在电脑上没敲一条命令（全过程见文章[《Claude Code 清理家里的电视：开机广告没了，内存多出 437 MB》](https://mp.weixin.qq.com/s/kUsc1V7OzcItR09xsixNzA)）。这个仓库把那一晚的做法整理成一个 AI Agent Skill，再加上 **23 个品牌、82 个系列**的开发者模式入口库和十二道安全闸，让下一个人不用再自己摸。

## 30 秒判断：你的电视能不能做、值不值得

| 你的电视 | 能不能 |
|---|---|
| **安卓 5–10**<br><sub>老款海信 / Vidda、TCL / 雷鸟、小米、创维 / 酷开、康佳、长虹、索尼 / 飞利浦 / 夏普 Android TV、华为智慧屏 HarmonyOS ≤ 4</sub> | ✅ **能做，收益最大**<br><sub>开机广告、桌面推荐位、上报服务都能停</sub> |
| **安卓 11 以上 / Google TV**<br><sub>Fire OS（Fire TV 全系）、Shield、Chromecast / onn</sub> | ✅ **能做**<br><sub>Google TV 13 / 14 要先配对码，步骤不同</sub> |
| **安卓 4.x**<br><sub>2012–2015 的老联想、夏普 × 联想等</sub> | ⚠️ **连得上，改不了**<br><sub>系统不允许停用自带应用，别折腾</sub> |
| **不是安卓**<br><sub>三星 Tizen、LG webOS、Roku、Vizio、海信海外 VIDAA（Linux）、飞利浦 SAPHI / Titan、阿里 YunOS、荣耀智慧屏、华为 MateTV</sub> | ❌ **不适用**<br><sub>不开放 ADB；库里给了每家「关广告在设置哪里」</sub> |
| **非智能电视** | ❌ **没有系统可改** |

值得做的三个信号：**有开机广告或桌面推荐位；内存 ≤ 2 GB；机龄三年以上**。命中任一就值得；三个都不中，收益小于折腾。

看不出自己是哪个系列？入口库每个系列都写了「怎么认」：遥控器有没有数字键、「设置 → 关于」里写的系统名、型号字母的含义。让 AI 问你三个问题就能落到系列。

## 三种用法（挑最省事的）

1. **把网址发给 AI（推荐）**：对 Claude Code / Codex 说「按 sanshengai.top/tools/tv-adb 清理我的电视，型号 X，IP Y」。它会读页面上给 AI 准备的入口数据和流程，什么都不用装。
2. **装这个 Skill**：想要脚本级的安全闸（备份、回滚预演、真重启验证、黑屏固定流程都是代码保证而不是靠 AI 记住）。安装见下面。
3. **复制提示词**：AI 不能上网、或用网页版对话时，把 `references/prompt-zh.md` 里那段贴过去，自己照步骤做。

## 安装

让 AI 自己装（最省事）：把这个仓库地址发给 Claude Code，说「装这个 skill」。

手动：

```bash
# Claude Code
git clone https://github.com/sanshengai/sansheng-tv-adb ~/.claude/skills/sansheng-tv-adb
# Codex（读 ~/.agents/skills）
ln -s ~/.claude/skills/sansheng-tv-adb ~/.agents/skills/sansheng-tv-adb
```

需要：Python 3.10+；`adb`（macOS `brew install android-platform-tools`，Windows 装 Google platform-tools 加 PATH，Linux `apt install adb`）；电脑和电视同一 Wi-Fi。国内 GitHub 打不开可走 sanshengai.top/tools/github 的加速镜像。

## 它怎么工作：十个阶段，每一步停下来等你

```
0 认机型 → 1 开门（你拿遥控器）→ 2 连接·判资格 → 3 备份 → 4 回滚预演
→ 5 分组（可停 / 看你用不用 / 禁停）→ 6 停一批（≤10）→ 你测四样 → 7 真重启验证
→ 8 出岔子：查状态 → 还原 → 重启 → 9 报告 + 结果卡
```

AI 每一步都会告诉你三件事：现在做什么、影响多大、怎么退回。你随时可以说「停」「恢复」「跳过」。

### 长什么样

跑完是这样（假 adb 回放海信 2017 录制夹具，与 09-14 真机过程一致：预演发现 `disable-user` 被封自动换 `hide`、第一批停完真重启黑屏 → `recover` 还原重启 → 重做 → 通过）：

![tvadb 从 probe 到 report 的终端记录](assets/03-terminal-replay.png)

网页版入口库先替你判断能不能做、值不值：

![判定卡：海信 + 数字键遥控器 + 安卓 5–10 → 可以做，分系列看](assets/01-judge-card.png)

品牌卡里按系列写清怎么认机型、值不值、开发者模式入口步骤、坑与来源：

![海信 · 老款 VIDAA 系列：认机型、值不值、工厂菜单入口步骤与坑](assets/02-hisense-entry.png)

## 安全承诺（写在脚本里，AI 绕不过）

- **没备份不许停**：先把应用清单、系统设置、内存快照存到你电脑上。
- **先预演再动手**：拿一个广告包「停 → 还原」走一遍，证明这台机的还原命令真的能用；命令被厂商封了就自动换等价的。
- **禁停硬名单**：桌面、信号源、输入法、安装器、系统设置、遥控器蓝牙、工厂/恢复出厂/OTA、电视核心服务、WebView、无障碍、HDMI-CEC、DRM，脚本层直接跳过。
- **一批最多 10 个**，停完等你测信号源、音量、图像设置、播片，你说没问题才下一批。
- **每停一个包写一行还原命令**到 `rollback.sh`，先写行再算成功；`sh rollback.sh` 全部还原。
- **每批真重启验证**（遥控器关机是待机，不算），检查画面、桌面、内存，以及停用状态有没有被系统悄悄恢复。
- **黑屏固定流程**：先证明系统在运行，再还原最近一批，再真重启；不让你拔电源。
- **不 root、不刷机、不卸系统应用、老电视不换桌面。**

白话免责：工厂菜单改错会伤电视，只改说明里那一项；本工具不卸载系统应用、每一步可还原，但电视是你的，请按步骤做、每批都测。

## 入口库里有什么

`data/brands/*.json`，23 个品牌 82 个系列，每条入口带来源与证据等级（本机实测 > 官方 > 多源 > 单源）。人读版在 `references/entry-by-brand.md`。实测案例包含 2017 海信 VIDAA、2013 夏普×联想，以及贡献者实测的 2024 乐视 Q85 巨幕（[开机广告复盘见这里](./references/case-letv-q85.md)），其余按证据等级如实标；查不到的写在各品牌 `open_questions` 里等投稿。

命令行速查（AI 用，你也可以自己跑）：

```bash
python3 scripts/tvadb.py identify --brand 海信 --version 5.1   # 认机型
python3 scripts/tvadb.py entry --brand 小米                     # 某品牌的入口步骤
python3 scripts/tvadb.py probe 192.168.1.34                     # 连接 + 判资格 + 建工作目录
python3 scripts/tvadb.py backup && python3 scripts/tvadb.py dryrun && python3 scripts/tvadb.py plan
python3 scripts/tvadb.py batch --n 1 && python3 scripts/tvadb.py verify
python3 scripts/tvadb.py restore --all                          # 后悔了
python3 scripts/tvadb.py report --card                          # 报告 + 结果卡
```

## 出了问题

- 黑屏：AI 会先告诉你「电视没坏」，然后按固定流程还原。你只需要别拔电源。
- 重启后连不上：等一分钟；电视重新弹授权框就再点一次「一律允许」。
- 想全部还原：`adb connect 电视IP:5555` 然后 `sh ~/tv-adb/<你的目录>/rollback.sh`。
- 命令被拒：正常现象，AI 会换一条；都不行就是「连得上改不了」，结束。

## 投稿你的机型

同一个品牌不同年份入口不一样，库里最缺的是**你实机走通的步骤**。开一个 issue 用「投稿一台电视的入口」表单，或直接改 `data/brands/` 提 PR（跑 `scripts/validate_data.py`）。带备份目录截图和前后数字的投稿会标「本机实测」并上「已验证机型墙」。见 [CONTRIBUTING.md](./CONTRIBUTING.md)。

## 致谢

- 思路来自 [Mert Cobanov](https://github.com/cobanov) 的 tv.cobanov.dev：一段提示词让 Claude Code 清理 Android TV。本项目独立编写，未复用其文本；国内品牌入口、命令降级策略和黑屏处理来自本人实测与公开资料整理。
- 品牌数据引用的每个来源都写在对应条目的 `sources` 里。

## 作者

叁笙 · [sanshengai.top](https://sanshengai.top) · 公众号「叁笙早安AI」。MIT 许可。
