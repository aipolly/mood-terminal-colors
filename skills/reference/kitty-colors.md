# Kitty 配色实操参考

来源：[kitty.conf 官方参考](https://sw.kovidgoyal.net/kitty/conf/)、[启动参数](https://sw.kovidgoyal.net/kitty/invocation/)、[主题 kitten](https://sw.kovidgoyal.net/kitty/kittens/themes/)、[远程控制](https://sw.kovidgoyal.net/kitty/remote-control/)。检索日期 2026-10-28；以本机 `kitty --version` 与各命令 `--help` 为准。

## 完整配色模板

每行 `选项 值`，独立注释行以 `#` 开头。示例值可直接作为工作流起点：

```conf
# 基础
foreground           #d8dee9
background           #202838
# 光标
cursor               #d8dee9
cursor_text_color    #202838
# 选区（两者都设 none 时为反相行为，none 不是普通 RGB）
selection_foreground #ffffff
selection_background #49566d
# ANSI 0-7：黑 红 绿 黄 蓝 品红 青 白
color0  #202838
color1  #e06c75
color2  #98c379
color3  #e5c07b
color4  #61afef
color5  #c678dd
color6  #56b6c2
color7  #d8dee9
# ANSI 8-15：对应的明亮版本
color8  #596579
color9  #ff7b86
color10 #b5e890
color11 #ffd68a
color12 #7fc4ff
color13 #e2a6ff
color14 #8be9f0
color15 #ffffff
# 可选 UI 色（按原配置实际用到的写，不必无条件全写）
active_tab_foreground   #202838
active_tab_background   #98c379
inactive_tab_foreground #d8dee9
inactive_tab_background #3b4252
active_border_color     #98c379
inactive_border_color   #3b4252
url_color               #61afef
```

## 选项速查

| 语义 | 选项 |
|---|---|
| 文字 / 背景 | `foreground`, `background` |
| 光标 / 光标文字 | `cursor`, `cursor_text_color` |
| 选区文字 / 背景 | `selection_foreground`, `selection_background` |
| ANSI 调色板 | `color0` … `color15`（更高索引也可配置） |
| 标签页 | `active_tab_foreground/background`, `inactive_tab_foreground/background` |
| 窗口边框 | `active_border_color`, `inactive_border_color`, `bell_border_color` |
| 链接 / 标记 | `url_color`, `mark1_foreground/background` 等 |

终端内的程序也可能直接输出 RGB 真彩色，不经过 16 色表；调色板只约束按索引取色的程序。

## 配置发现与加载优先级

1. 版本与位置：`kitty --version`。未指定 `-c` 时按 `$XDG_CONFIG_HOME/kitty/kitty.conf` → `~/.config/kitty/kitty.conf` → `$XDG_CONFIG_DIRS/kitty/kitty.conf` 取第一个存在的文件；`KITTY_CONFIG_DIRECTORY` 另行覆盖。
2. `kitty -c <文件>`：指定文件**合并到内置默认值之上**（不是完全替代；完全不加载用特殊值 `NONE`）。可多次指定 `-c`，按顺序合并，后覆盖先。
3. `include 路径`：相对路径相对于**包含它的文件**；加载顺序即优先级，后加载的覆盖先加载的。
4. 主题机制三件事：
   - `kitten themes` 交互选择并预览，但**会改写**用户配置和 `current-theme.conf`——本任务禁用。
   - 只读查看某主题：`kitten themes --dump-theme '主题名'`（行为以本机帮助为准）。
   - kitty ≥0.38：`dark-theme.auto.conf` / `light-theme.auto.conf` / `no-preference-theme.auto.conf` 存在时，所选自动主题覆盖其他一切颜色设置，**包括命令行 override**。
5. 运行中实例改色须走远程控制 `kitten @ set-colors`，受 kitty.conf 权限配置约束；未经同意不使用，也不为配色擅自开启或扩大远程控制。

## 生成独立配置检查单

- [ ] 新文件不 include `current-theme.conf`、任何 `*-theme.auto.conf`
- [ ] 原配置中的非颜色项已搬入新文件（`font_size`、`background_opacity` 等保持原值）
- [ ] 原 include 依赖已复制到工作目录并改为绝对路径；其中的颜色项已处理，非颜色项保留
- [ ] 文件名不与工作目录已有文件冲突

## 验证

解析检查（内部 API 随版本可能变化，先看签名）：

```bash
# 1. 确认本机 load_config 签名
kitty +runpy "from kitty.config import load_config; help(load_config)"
# 2. 解析检查：坏行会抛异常、退出码非 0；全部通过则打印"解析通过"
kitty +runpy "from kitty.config import load_config; load_config('/绝对路径/新配置.conf'); print('解析通过')"
```

之后人工核对最终生效值：背景、前景、光标、选区与全部 16 个索引逐项对照设计清单——语法解析、最终生效值、GUI 实际显示是三级不同的验证，未做的级别如实报告。透明背景下的计算对比度不代表实际观感。

独立启动：`kitty -c /绝对路径/新配置.conf`（合并到内置默认值之上，不加载用户配置；可多个 `-c` 按序合并）。
