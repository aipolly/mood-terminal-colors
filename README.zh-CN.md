# Mood Terminal Colors（心情终端配色）

[English version](README.md)

一个给 AI 编程助手用的 Skill：按你今天的心情设计终端配色。当前支持 Ghostty、Kitty、WezTerm、Alacritty；另有五个终端暂时停用（见下表）。

## 为什么做这个仓库？

人会按心情穿衣服：有精神的日子穿件挺括的衬衫，慵懒的周日套上旧卫衣。终端也该这样：你每天盯着它好几个小时，它却一年到头都是同一身配色。

你花了一整晚挑颜色、对对比度、存下那套主题。一周后坐在终端前，还是觉得哪里不对。配置没变，变的是你：心情每天都在变，一套固定的配色跟不上。

所以这个 Skill 只为今天设计，就像出门前挑一身今天要穿的衣服。可以表达当下的心情，也可以在想要换换感觉的时候营造另一种氛围，可读性始终是底线。

颜色和情绪的联系被测量过，不是凭感觉说的：

- 一篇系统综述汇总了 1895-2022 年的 132 篇同行评审研究、64 个国家共 42,266 名参与者，发现颜色与情绪的关联系统而稳定，但属于多对多，受色相、明度、饱和度共同影响（[Do we feel colours?](https://doi.org/10.3758/s13423-024-02615-z)）。
- 一项覆盖 30 国、4,598 人的研究发现了跨文化的共同模式（黄橙偏愉悦、蓝绿偏平静），也有真实的地区差异（[Jonauskaite 等，2020](https://pubmed.ncbi.nlm.nih.gov/32900287/)）。

把这些结论当起点，最终由你的偏好决定。

## 终端支持

| 终端 | 状态 | 带日期配置如何启动 |
|---|---|---|
| Ghostty | 可用 | `ghostty --config-default-files=false --config-file=<文件>` |
| Kitty | 可用 | `kitty -c <文件>` |
| WezTerm | 可用 | `wezterm start --config-file <文件>` |
| Alacritty | 可用 | `alacritty --config-file <文件>` |
| iTerm2 | 暂不可用 | 预设导入会改应用设置；无法用配置文件启动 |
| Terminal.app | 暂不可用 | 颜色只能写进用户设置，ANSI 色板仅 GUI 可改 |
| Warp | 暂不可用 | 主题须复制进 `~/.warp/themes` 并在 GUI 里选择 |
| GNOME Terminal | 暂不可用 | 没有配置文件；dconf 直接写用户 profile |
| Konsole | 暂不可用 | 配色须安装进 `~/.local/share/konsole` |

严格是有意的。心情配色是一次一天的试验：所有文件都落在 `~/.config/mood_terminal`，用显式命令启动，你的真实配置一个字都不会动。做不到这一点的终端先停用，直到有安全的做法。停用终端的格式仍保留在 `skills/reference/`，欢迎用 PR 提供安全方案。

## 它做什么

- 用日常语言描述任何心情（包括混合情绪）。描述含糊时会先问几个简短问题（不超过五个），先定大类、再定具体感受，然后才开始配色。
- 设计一套调色板（背景、前景、光标、选区、16 个 ANSI 色），按 WCAG 公式核对对比度不低于 4.5:1。
- 把带日期的配置和启动函数文件写进 `~/.config/mood_terminal`，保留非颜色设置，不动你的真实配置。
- 如实报告验证了什么、没验证什么：语法、颜色、GUI 预览、透明度注意事项。

这个仓库是给支持 Skills 的助手看的指令文件，本身不运行任何东西；每次使用由助手读取你的环境、设计并写出文件。

## 安装

```bash
git clone https://github.com/aipolly/mood-terminal-colors.git ~/mood-terminal-colors
ln -s ~/mood-terminal-colors/skills ~/.agents/skills/mood-terminal-colors
```

目录按你的助手实际情况调整。替用户安装的 agent 可以参考 [FOR-AGENTS.md](FOR-AGENTS.md)。装完可能需要重载 Skills 或重启助手。

## 使用

> 我今天很兴奋，给我按心情配一套 Kitty 配色。

助手可能先问几个小问题（是明亮的兴奋，还是坐不住的兴奋？深色还是浅色？），然后写出带日期的配置和启动文件，并告诉你确切的 source 命令。source 之后用 `mood-kitty` 启动。请在真实窗口里看一看效果：屏幕、透明度和个人口味都不一样。

## 关于 vibe coding

这个项目是 vibe coding 出来的：和 AI 一起快速写成，图个舒服，背后没有测试套件，不保证适配所有终端版本。发现问题或想改进，欢迎提 PR。

## 设计原则

颜色与情绪的关联受文化、经历和情境影响。配色不是诊断，也治不好糟糕的一天。个人偏好、和谐、可读性优先。

## 参考资料

`skills/reference/` 下每个终端一份实操文档，另有[心情与颜色](skills/reference/mood-and-color.md)（研究边界、五问心情确认、起手调色板、对比度方法、ANSI 语义）。外部来源：

- [APA：颜色与情绪](https://www.apa.org/news/podcasts/speaking-of-psychology/color-emotion)
- [Ghostty](https://ghostty.org/docs/config)、[Kitty](https://sw.kovidgoyal.net/kitty/conf/)、[WezTerm](https://wezterm.org/config/files.html)、[Alacritty](https://github.com/alacritty/alacritty/blob/master/extra/man/alacritty.5.scd) 文档
- [WCAG 对比度](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
