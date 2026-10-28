# Ghostty 配色实操参考

来源：[配置说明](https://ghostty.org/docs/config)、[配置项参考](https://ghostty.org/docs/config/reference)、[官方主题说明](https://ghostty.org/docs/features/theme)。检索日期 2026-10-28；以本机 `ghostty +version` 与各动作 `--help` 为准。

## 完整配色模板

`key = value`，键名区分大小写，注释必须单独成行。示例值可直接作为工作流起点：

```conf
# 基础
foreground = #d8dee9
background = #202838
# 光标
cursor-color = #d8dee9
cursor-text  = #202838
# 选区
selection-foreground = #ffffff
selection-background = #49566d
# ANSI 0-15（palette 支持 0-255，配色先写全 0-15）
palette = 0=#202838
palette = 1=#e06c75
palette = 2=#98c379
palette = 3=#e5c07b
palette = 4=#61afef
palette = 5=#c678dd
palette = 6=#56b6c2
palette = 7=#d8dee9
palette = 8=#596579
palette = 9=#ff7b86
palette = 10=#b5e890
palette = 11=#ffd68a
palette = 12=#7fc4ff
palette = 13=#e2a6ff
palette = 14=#8be9f0
palette = 15=#ffffff
```

新配置不写 `theme =`、不写 `config-file`，即完全不依赖已安装主题。

## 键名对照（Ghostty ↔ Kitty）

| 语义 | Ghostty | Kitty |
|---|---|---|
| 文字 / 背景 | `foreground`, `background` | 同名 |
| 光标 / 光标文字 | `cursor-color`, `cursor-text` | `cursor`, `cursor_text_color` |
| 选区 | `selection-foreground`, `selection-background` | `selection_foreground`, `selection_background` |
| ANSI 索引 | `palette = N=#RRGGBB` | `colorN #RRGGBB` |

`palette` 支持 0–255；程序也可能直接输出真彩色，不经过调色板。

## 配置发现与加载优先级

1. 版本：`ghostty +version`。
2. 位置与顺序（1.2.3 起文件名为 `config.ghostty`，之前为 `config`）：
   - `$XDG_CONFIG_HOME/ghostty/config.ghostty`，其次 `$XDG_CONFIG_HOME/ghostty/config`（XDG 未设置时即 `~/.config/ghostty/`）；
   - macOS 另加载 `~/Library/Application Support/com.mitchellh.ghostty/config.ghostty`、`.../config`，XDG 文件先加载；
   - 多文件并存时按上述顺序加载，后加载的冲突项覆盖先加载的。
3. 嵌套与主题的覆盖规则（生成独立配置的关键）：
   - `theme = 主题名` 先加载，主配置中的同名选项覆盖主题；主题可能含非颜色选项，不信任的主题不要加载；
   - `config-file = 路径` 引入的文件在**整个父配置文件之后**加载——即使颜色写在父文件末尾也会被嵌套文件覆盖；嵌套文件可再引文件；相对路径基于包含该指令的文件；`?` 前缀表示文件不存在则忽略；循环引用会被检测并警告。
4. 重载：默认快捷键 Linux `ctrl+shift+,`、macOS `cmd+shift+,`；部分选项不可动态重载或仅对新终端生效。未经同意不重载用户会话。

## 生成独立配置检查单

- [ ] 首选：新文件自包含——全部颜色写全，不写 `theme =`，不写 `config-file`
- [ ] 需保留原配置行为时：原主配置与其全部 `config-file` 依赖复制到工作目录，改用绝对路径；依赖中的颜色项覆盖或删除，非颜色项保留
- [ ] 禁止：只复制父配置后在其末尾追加颜色（嵌套文件会在其后加载并覆盖它）
- [ ] 非颜色项（如 `background-opacity`、字体）保持原值，不为对比度调整

## 验证与启动

```bash
# 看本版本动作支持的参数（动作参数与普通启动不同）
ghostty +validate-config --help
# 语法验证（本版本支持时）
ghostty +validate-config --config-file=/绝对路径/新配置
# 输出全部默认配置与文档，核对键名拼法
ghostty +show-config --default --docs | less
```

部分版本的动作不支持 `--config-default-files` 或 `+show-config --config-file`；无法检查最终生效配置时如实报告，不假装验证通过。语法验证、最终生效值、GUI 实际显示是三级不同的验证。

独立启动：

```bash
# Linux
ghostty --config-default-files=false --config-file=/绝对路径/新配置
# macOS
open -na Ghostty.app --args --config-default-files=false --config-file=/绝对路径/新配置
```

参数支持以本机 `ghostty --help` 为准。
