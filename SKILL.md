---
name: mood-terminal-colors
description: 根据用户当天的任意心情，定制和谐、易读的 Ghostty 和 Kitty 终端配色，并基于现有配置在工作目录生成独立配置文件。用户提到心情配色、情绪主题、根据心情改变 Ghostty 或 Kitty 颜色、mood terminal theme 时使用；不修改原配置，也不改变非颜色设置。
---

# 心情终端配色

## 交互

调用时先询问用户今天的心情；若本次请求已明确说明则不重复询问。接受任意自然语言描述，包括混合情绪，不限制于预设选项；无法判断时询问。默认以舒适、自然、可读为先，结合用户个人喜好决定是表达心情还是营造不同氛围。仅设计颜色，不修改字体、透明度、快捷键、窗口布局等非颜色设置。

## 配色设计

颜色和情绪是统计性的、受个人与文化影响的关联，不是诊断，也不保证改变情绪。喜悦可考虑暖黄、珊瑚或杏色点缀；低落或紧张可考虑柔和蓝绿、灰紫或温暖中性色，避免假定用户想要深色或者需要被安抚。其他情绪按用户描述、审美和舒适度灵活设计。避免大面积高饱和荧光色；尽量让终端前景与背景对比度达到 WCAG 普通文本 4.5:1（按 sRGB 计算，不是对终端整体的无障碍认证），同时检查选中文字、光标和 ANSI 色彩可辨认。透明背景会改变实际对比度，应说明测试按不透明背景计算。

可参考 Ghostty 的内置主题（`ghostty +list-themes`；本地主题一般在 `/usr/share/ghostty/themes/`，实际路径因安装方式而异），可据此调整而非生硬复制。根据 Ghostty 和 Kitty 的语义使用同一套背景、前景、光标、选区和 16 色 ANSI 色表；保留红绿黄蓝的常见语义。参考资料：
- 美国心理学会的色彩心理研究者访谈：https://www.apa.org/news/podcasts/speaking-of-psychology/color-emotion
- Ghostty 主题：https://ghostty.org/docs/features/theme
- Ghostty 配置：https://ghostty.org/docs/config/reference
- Kitty 配置：https://sw.kovidgoyal.net/kitty/conf/
- WCAG 对比度：https://www.w3.org/WAI/WCAG22/Understanding/contrast-minimum.html

## 生成配置

1. 确定工作目录，避免覆盖已有文件；如目标文件已存在，选择新名字或询问。识别并读取两种终端实际默认配置（尊重 XDG、Kitty 的 `KITTY_CONFIG_DIRECTORY` 和平台规则），检查系统配置、所有 include/config-file 和主题以及它们的优先级。若找不到配置，告知用户并从内置默认值开始，不要声称读取了不存在的配置。不要执行配置内的 shell 命令或盲目暴露敏感信息。
2. 在工作目录生成独立的新 Ghostty 和 Kitty 配置，保留原有非颜色配置及其行为。不能修改原始配置及其依赖，也不能把透明度调整为 1。原有 theme、颜色或包括颜色的嵌套配置可能覆盖新配色：根据各终端实际规则确保最终生效的是新配色。必要时重写或复制依赖到工作目录并修正相对路径，保留非颜色设置；不要简单地只在文件末尾追加 Ghostty 颜色，因为 `config-file` 嵌套文件在整个父配置之后加载。优先使用绝对路径，避免工作目录变化导致 include 或相对路径失效。原配置如果存在条件逻辑或不安全/不确定的依赖，明确报告而非假装验证成功。
3. Ghostty 用 `foreground = #...`、`background = #...`、`palette = 0=#...` 等；Kitty 用 `foreground #...`、`background #...`、`color0 #...` 等。将主题有关的标题栏、标签、选区、光标等颜色冲突一并检查。不要依赖安装了某个主题才能使用生成的文件。
4. 若可用，运行 `ghostty +validate-config --config-file=<新文件>`，注意部分版本的动作不支持 `--config-default-files` 或 `+show-config --config-file`；先查阅动作帮助，无法检查最终有效配置就如实报告。Kitty 可通过 `kitty +runpy` 使用 `kitty.config.load_config` 并收集 `accumulate_bad_lines` 检查解析；API 随版本可能变化。核对颜色值与全部 16 项 ANSI 配色。区分语法验证、有效配置验证及实际 GUI 预览；没有 GUI 就不要声称视觉验证通过。
5. 向用户报告生成文件的绝对路径、配色思路、验证结果和未解决的问题。给出引用绝对路径的启动命令：`kitty -c <文件>`；Ghostty 可使用 `ghostty --config-default-files=false --config-file=<文件>`（以该版本支持情况为准）。说明 Ghostty macOS 应通过 `open -na Ghostty.app --args ...` 启动。不要自动启动或修改用户默认配置，除非用户另行要求。
