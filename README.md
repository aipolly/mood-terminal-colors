# Mood Terminal Colors

一个为 AI 编程助手设计的 Skill：根据当天的心情，为 **Ghostty** 和 **Kitty** 定制协调、易读的终端配色。

## 功能

- 支持任意自然语言描述的心情，包括混合情绪；未提供心情时先询问。
- 参考个人偏好设计配色，为两个终端生成一致的前景、背景、光标、选区和 16 色 ANSI 调色板。
- 检查现有配置及主题，将新配置写入当前工作目录，保留非颜色设置，不修改用户默认配置。
- 尽可能验证配置和文字对比度，并说明透明背景、终端版本或无法预览等限制。

这是提供给支持 Skills 的 AI 助手使用的**指令文件**，不是终端插件或自动运行的程序；每次使用时由助手根据环境读取配置、设计并生成结果。

## 安装

将仓库克隆到助手的 Skills 目录，例如：

```bash
git clone <仓库地址> ~/.agents/skills/mood-terminal-colors
```

具体 Skills 目录和加载方式取决于所使用的助手；如果该目录已存在，请先备份或选择其他目录，不要覆盖现有文件。安装后可能需要重新加载 Skills 或重启助手。

## 使用

在支持此 Skill 的助手中提出请求，例如：

> 我今天很兴奋，请根据我的心情定制 Ghostty 和 Kitty 配色，生成配置到当前目录。

助手会检查当前环境，生成独立配置并给出启动命令。配置文件仍应在实际终端中预览：视觉感受、背景透明度与主题效果会因设备和个人喜好而异。

## 设计原则

颜色与情绪之间是受文化、个人经验和具体情境影响的关联，不是诊断，也不保证改变情绪。设计优先考虑个人喜好、和谐度和可读性。

## 参考资料

- [美国心理学会：颜色与情绪](https://www.apa.org/news/podcasts/speaking-of-psychology/color-emotion)
- [Ghostty 主题](https://ghostty.org/docs/features/theme)与[配置参考](https://ghostty.org/docs/config/reference)
- [Kitty 配置参考](https://sw.kovidgoyal.net/kitty/conf/)
- [WCAG 文字对比度](https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html)
