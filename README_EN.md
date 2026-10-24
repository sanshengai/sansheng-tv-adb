# sansheng-tv-adb · Let an AI agent debloat your TV

> **Hand one paragraph to an AI agent; it connects to your TV over ADB, removes boot ads, disables preinstalled apps and frees memory — nothing uninstalled, no root, every step reversible.**

[中文](./README.md) | **English**

A 2017 Hisense TV with 2 GB of RAM: after the cleanup the boot ad was gone and available memory went from 551 MB to 988 MB, and I never typed a command (the full story, in Chinese: [the article](https://mp.weixin.qq.com/s/kUsc1V7OzcItR09xsixNzA)). This repository turns that evening into an Agent Skill, plus a developer-mode entry library covering **23 brands and 82 series** and twelve safety gates, so the next person does not have to figure it out alone.

## 30-second check: can your TV be done, and is it worth it?

| Your TV | Doable? |
|---|---|
| **Android 5–10**<br><sub>older Hisense / Vidda, TCL / FFALCON, Xiaomi, Skyworth / Coocaa, Konka, Changhong, Sony / Philips / Sharp Android TV, Huawei Vision HarmonyOS ≤ 4</sub> | ✅ **Yes, biggest payoff**<br><sub>boot ads, home-screen recommendation rows and telemetry can all be disabled</sub> |
| **Android 11+ / Google TV**<br><sub>Fire OS (all Fire TV), Shield, Chromecast / onn</sub> | ✅ **Yes**<br><sub>Google TV on Android 13 / 14 needs a pairing code first; different steps</sub> |
| **Android 4.x**<br><sub>2012–2015 Lenovo ideatv, Sharp × Lenovo, etc.</sub> | ⚠️ **Connects, cannot change**<br><sub>the system refuses to disable built-in apps; not worth it</sub> |
| **Not Android**<br><sub>Samsung Tizen, LG webOS, Roku, Vizio, Hisense VIDAA (Linux), Philips SAPHI / Titan, Alibaba YunOS, Honor Vision, Huawei MateTV</sub> | ❌ **Not applicable**<br><sub>ADB not exposed; the library lists where each one's ad switches live</sub> |
| **Non-smart TV** | ❌ **Nothing to change** |

Three signals that it is worth it: **a boot ad or recommendation rows on the home screen; RAM ≤ 2 GB; the set is three years old or more.** Any one of them is enough.

Not sure which series you own? Every series in the library has an "how to recognise it" note: whether the remote has number keys, what the About page calls the system, what the letters in the model number mean. Let the agent ask you three questions.

## Three ways to use it (pick the laziest)

1. **Send the URL to your agent (recommended)**: tell Claude Code / Codex "clean up my TV following sanshengai.top/tools/tv-adb, model X, IP Y". It reads the agent-facing data and flow from the page. Nothing to install.
2. **Install this skill**: when you want script-level safety gates — backup, rollback rehearsal, real-reboot verification and the black-screen procedure are enforced by code, not by the model's memory.
3. **Copy the prompt**: if your agent has no web access, paste `references/prompt-en.md` and follow the steps yourself.

## Install

Easiest: paste this repository URL into Claude Code and say "install this skill".

Manual:

```bash
# Claude Code
git clone https://github.com/sanshengai/sansheng-tv-adb ~/.claude/skills/sansheng-tv-adb
# Codex (reads ~/.agents/skills)
ln -s ~/.claude/skills/sansheng-tv-adb ~/.agents/skills/sansheng-tv-adb
```

Requires Python 3.10+, `adb` (macOS `brew install android-platform-tools`; Windows: Google platform-tools on PATH; Linux `apt install adb`), and the computer on the same Wi-Fi as the TV.

## How it works: ten stages, and it stops at each one

```
0 identify → 1 open the door (you, with the remote) → 2 connect + eligibility → 3 backup → 4 rollback rehearsal
→ 5 group (safe / ask me / never) → 6 disable one batch (≤10) → you test four things → 7 real-reboot verification
→ 8 if anything goes wrong: check state → restore → reboot → 9 report + share card
```

At every step the agent tells you three things: what it is doing now, how big the blast radius is, how to undo it. You can say "stop", "restore" or "skip" at any time.

### What it looks like

A full run (fake-adb replay of the Hisense 2017 fixture, mirroring the real 09-14 session: the rehearsal finds `disable-user` blocked and switches to `hide`, the first real reboot goes black → `recover` restores and reboots → redo → pass):

![tvadb terminal transcript from probe to report](assets/03-terminal-replay.png)

The web entry library first tells you whether your TV can be done and whether it is worth it:

![Verdict card: Hisense + numeric remote + Android 5–10 → doable, pick your series](assets/01-judge-card.png)

Each brand card explains how to identify your model, the developer-mode entry steps per series, pitfalls and sources (the site is in Chinese; the agent reads it for you):

![Hisense · old VIDAA series: how to identify it, is it worth it, factory-menu entry steps and pitfalls](assets/02-hisense-entry.png)

## Safety promises (enforced in the scripts, the agent cannot bypass them)

- **No backup, no disabling**: package lists, system settings and a memory snapshot are saved to your computer first.
- **Rehearse before touching anything**: one ad package goes through "disable → restore" to prove the restore command really works on this TV; if the vendor blocked a command, an equivalent one is used automatically.
- **Hard never-touch list**: launcher, TV input/HDMI, input method, package installer, settings, remote/Bluetooth, factory/reset/OTA, core TV services, WebView, accessibility, HDMI-CEC, DRM — skipped at script level.
- **At most 10 per batch**, then it waits while you test input source, volume, picture settings and playback.
- **One restore line per package** appended to `rollback.sh` before the step counts as done; `sh rollback.sh` undoes everything.
- **Real reboot after every batch** (the remote's power button is standby), checking picture, launcher, memory, and whether the system silently re-enabled anything.
- **Fixed black-screen procedure**: prove the system is alive, restore the last batch, real reboot. It will never tell you to pull the plug.
- **No root, no flashing, no uninstalling system apps, no launcher replacement on old TVs.**

Plain-language disclaimer: changing the wrong item in a factory menu can hurt the TV — change only the one item the instructions name. Nothing here uninstalls system apps and every step can be undone, but it is your TV: follow the steps and test after every batch.

## What is in the entry library

`data/brands/*.json`: 23 brands, 82 series, every entry with sources and an evidence level (tested-by-us > official > multiple sources > single source). Human-readable version in `references/entry-by-brand.md`. Only two sets have been tested by us so far (2017 Hisense VIDAA, 2013 Sharp/Lenovo); everything else is labelled honestly, and gaps are listed under each brand's `open_questions` waiting for contributions.

CLI quick reference:

```bash
python3 scripts/tvadb.py identify --brand sony --version 9    # identify series
python3 scripts/tvadb.py entry --brand "fire tv"              # entry steps for a brand
python3 scripts/tvadb.py probe 192.168.1.34                    # connect + eligibility + working directory
python3 scripts/tvadb.py backup && python3 scripts/tvadb.py dryrun && python3 scripts/tvadb.py plan
python3 scripts/tvadb.py batch --n 1 && python3 scripts/tvadb.py verify
python3 scripts/tvadb.py restore --all                         # changed your mind
python3 scripts/tvadb.py report --card                         # report + share card
```

## When something goes wrong

- Black screen: the agent first tells you the TV is not broken, then restores by the fixed procedure. Just do not pull the plug.
- Not reconnecting after reboot: wait a minute; if the TV shows the authorization prompt again, tap "Always allow".
- Undo everything: `adb connect TV_IP:5555` then `sh ~/tv-adb/<your dir>/rollback.sh`.
- Command denied: normal; the agent tries the next method. If none work, the verdict is "connects but cannot change" and it stops.

## Contribute your TV

Entries differ by year even within one brand, and what the library lacks most is **steps you actually walked through on a real set**. Open an issue with the "Submit a TV entry" form, or edit `data/brands/` and send a PR (run `scripts/validate_data.py`). Submissions with a backup-folder screenshot and before/after numbers are marked "tested" and go on the verified-models wall. See [CONTRIBUTING.md](./CONTRIBUTING.md).

## Credits

- The idea comes from [Mert Cobanov](https://github.com/cobanov)'s tv.cobanov.dev — one prompt that lets Claude Code debloat an Android TV. This project is written independently and reuses none of its text; the Chinese-brand entries, command fallback strategy and black-screen procedure come from our own testing and public sources.
- Every source used for brand data is listed in that entry's `sources`.

## Author

叁笙 (sansheng) · [sanshengai.top](https://sanshengai.top). MIT licensed.
