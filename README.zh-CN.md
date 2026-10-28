# Mood Terminal Colors（心情终端配色）

[English version](README.md)

一个为 AI 编程助手设计的 Skill：根据当天的心情定制协调、易读的终端配色，支持 **Ghostty、Kitty、WezTerm、Alacritty、iTerm2、Terminal.app、Warp、GNOME Terminal、Konsole**。

## 为什么做这个仓库？

你花了一整晚挑颜色、对对比度、存下那套主题——一周后坐在终端前，却还是觉得哪里不对。配置没变，变的是你：心情每天都在变，而一套固定的配色跟不上这种变化。

这个 Skill 围绕今天的心情设计配色——兴奋、疲惫、平静，或介于之间；可以表达当下，也可以换个氛围，但始终以可读为先。

"颜色和情绪有关"并不是玄学，它被测量过：

- 一篇系统综述汇总了 1895–2022 年间的 132 篇同行评审研究、覆盖 64 个国家共 42,266 名参与者，发现颜色与情绪的关联系统而稳定、并非随机——但也是多对多的，由色相、明度、饱和度共同塑造（[Do we feel colours?](https://doi.org/10.3758/s13423-024-02615-z)）。
- 一项覆盖 30 国、4,598 名参与者的研究发现存在跨文化的共同模式——黄橙更常关联愉悦、蓝绿更常关联平静——同时也存在真实的语言与地区差异（[Jonauskaite 等，2020](https://pubmed.ncbi.nlm.nih.gov/32900287/)）。

关联不是处方：Skill 把这些结论当作起手参考，你的偏好永远优先（详见[心情与颜色](skills/reference/mood-and-color.md)）。

## 功能

- 支持任意自然语言描述的心情，包括混合情绪；未提供心情时先询问。
- 参考个人偏好设计配色，为用户指定的每个终端生成一致的前景、背景、光标、选区和 16 色 ANSI 调色板（各终端的能力差异见对应参考文档）。
- 检查现有配置及主题，按用户指定的终端把带日期的配置写入 `~/.config/mood_terminal`，生成含启动函数的 shell 文件并告知 source 命令；保留非颜色设置，不修改用户默认配置。
- 尽可能验证配置和文字对比度，并说明透明背景、终端版本或无法预览等限制。

这是提供给支持 Skills 的 AI 助手使用的**指令文件**，不是终端插件或自动运行的程序；每次使用时由助手根据环境读取配置、设计并生成结果。

## 安装

仓库根目录是说明文档，Skill 本体在 [`skills/`](skills/) 目录（`SKILL.md` + `reference/`）。将其链接或复制到助手的 Skills 目录，例如：

```bash
git clone <仓库地址> ~/mood-terminal-colors
ln -s ~/mood-terminal-colors/skills ~/.agents/skills/mood-terminal-colors
```

具体 Skills 目录和加载方式取决于所使用的助手；目标路径已存在时请先备份，不要覆盖现有文件。安装后可能需要重新加载 Skills 或重启助手。

## 使用

在支持此 Skill 的助手中提出请求，例如：

> 我今天很兴奋，请根据我的心情定制 Ghostty 和 Kitty 配色，生成配置到 `~/.config/mood_terminal`。

助手会检查当前环境，生成带日期的独立配置与启动函数文件，并告知应 source 哪个文件（之后用 `mood-kitty` / `mood-ghostty` 启动）。配置文件仍应在实际终端中预览：视觉感受、背景透明度与主题效果会因设备和个人喜好而异。

## 设计原则

颜色与情绪之间是受文化、个人经验和具体情境影响的关联，不是诊断，也不保证改变情绪。设计优先考虑个人喜好、和谐度和可读性。

## 参考资料

Skill 内部参考文档见 [`skills/reference/`](skills/reference/)——每个终端一份实操文档（Ghostty、Kitty、WezTerm、Alacritty、iTerm2、Terminal.app、Warp、GNOME Terminal、Konsole），另有[心情与颜色](skills/reference/mood-and-color.md)。

外部来源：

- [美国心理学会：颜色与情绪](https://www.apa.org/news/podcasts/speaking-of-psychology/color-emotion)
- [Ghostty 主题](https://ghostty.org/docs/features/theme)与[配置参考](https://ghostty.org/docs/config/reference)
- [Kitty 配置参考](https://sw.kovidgoyal.net/kitty/conf/)
- [WCAG 文字对比度](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
