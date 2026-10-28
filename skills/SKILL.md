---
name: mood-terminal-colors
description: 根据用户当天的心情，为 Ghostty 和 Kitty 设计和谐、易读的终端配色，并在工作目录生成独立配置文件。用户提到心情配色、情绪主题、根据心情改变 Ghostty 或 Kitty 颜色、mood terminal theme 时使用。按六阶段流程执行：确认心情与偏好 → 勘察现有配置 → 设计调色板并核算对比度 → 生成两份独立配置 → 验证 → 交付报告。全程不修改用户原配置和非颜色设置，不依赖任何外部脚本。
---

# 心情终端配色（Ghostty + Kitty）

按六个阶段顺序执行，每阶段给出动作、产出与完成标准；上一阶段产出是下一阶段输入。细节不在本文重复，按各阶段标注查阅 `reference/` 对应文件。

## 阶段 1：确认心情与偏好

1. 用户已说明心情则直接采用；未说明先询问。接受任意自然语言描述（含混合情绪），不限于预设选项；无法判断就问，不要猜。
2. 用户未主动提及时，确认三件事：
   - 表达当前心情，还是营造不同的氛围（如疲惫时想要清爽感）？
   - 深色还是浅色背景？
   - 有无明确喜欢或排斥的色系？
3. 仅设计颜色。字体、透明度、快捷键、窗口布局等非颜色设置一律不动。

设计立场：舒适、自然、可读优先。颜色与情绪的关联是统计性的、受文化与个人差异影响，不是诊断，也不保证改变情绪；不要假定低落的用户需要安抚、紧张的用户需要冷静蓝。

完成标准：心情描述、表达/换氛围取向、深浅偏好、色系禁忌四项齐备。
色彩方向与起手调色板见 `reference/mood-and-color.md`。

## 阶段 2：勘察现有配置

逐项查明两个终端"影响最终颜色"的完整配置链。只读，不修改：

- Kitty：`kitty --version` 记录版本。配置位置依次为 `KITTY_CONFIG_DIRECTORY`、`$XDG_CONFIG_HOME/kitty/kitty.conf`、`~/.config/kitty/kitty.conf`。读出主配置及全部 `include` 依赖（相对路径相对于包含它的文件）；检查是否 include 了 `current-theme.conf`；版本 ≥0.38 时检查 `dark-theme.auto.conf` / `light-theme.auto.conf` / `no-preference-theme.auto.conf`——存在时会覆盖包括命令行在内的其他颜色设置。
- Ghostty：`ghostty +version` 记录版本。配置为 `$XDG_CONFIG_HOME/ghostty/config.ghostty`（旧版本为 `config`；未设置 XDG 时通常在 `~/.config/ghostty/`），macOS 另有 `~/Library/Application Support/com.mitchellh.ghostty/`，多文件按文档顺序加载、后者覆盖前者。读出 `theme =` 与 `config-file` 嵌套链（嵌套文件在整个父配置之后加载，可再嵌套）。
- 找不到配置：如实告知，从内置默认值开始，不得声称读取了不存在的配置。
- 安全：不执行配置中的 shell 命令或快捷键绑定（kitty `launch`/`map`、Ghostty `command` 等），不盲目复述可能含密钥的内容。

产出：两个终端各自的"颜色相关配置链"清单（文件绝对路径 + 相关行号与内容）。
完成标准：能回答"用户终端最终生效的颜色由哪些文件、哪些行决定"。
详见 `reference/kitty-colors.md`、`reference/ghostty-colors.md` 的「配置发现与加载优先级」。

## 阶段 3：设计调色板

一套颜色两个终端共用：background、foreground、cursor 与光标文字、选区前景/背景、color0–color15。

1. 按阶段 1 的心情与偏好，从 `reference/mood-and-color.md` 的「心情族 → 起手方向」取起点，再按用户审美调整；避免大面积高饱和荧光色，高饱和只作点缀。
2. 保留 ANSI 红/绿/黄/蓝的常见语义（错误/成功/警告/链接与目录），逐项过该文件的「16 色语义检查表」。
3. 手算对比度（不依赖脚本）：公式、必查对清单与算例见 `reference/mood-and-color.md` 的「对比度核算（手算方法）」。最低要求：foreground/background、选区前景/背景、光标文字/光标、常用作前景的 ANSI 色（1–7、9–15，尤其 7 和 15）对背景 ≥4.5:1；color0/color8 可更低但须可辨认。按不透明背景计算；用户开透明背景时，在交付报告中说明实际观感会不同。

产出：24 色清单（hex）+ 每个必查对的对比度数值。
完成标准：必查对全部达标，或例外有明确理由（如 color0 贴近背景）。

## 阶段 4：生成配置文件

1. 写入用户指定或当前工作目录；目标文件已存在则改名或询问，不覆盖。
2. 语法对照（完整模板见两份 reference 的「完整配色模板」）：

   | 语义 | Ghostty | Kitty |
   |---|---|---|
   | 前景 / 背景 | `foreground` / `background` | 同名 |
   | 光标 / 光标文字 | `cursor-color` / `cursor-text` | `cursor` / `cursor_text_color` |
   | 选区前景 / 背景 | `selection-foreground` / `selection-background` | `selection_foreground` / `selection_background` |
   | ANSI N | `palette = N=#RRGGBB` | `colorN #RRGGBB` |

3. 保留非颜色行为：把原配置中的非颜色项搬进新文件；原配置引用的文件复制到工作目录并改用绝对路径；透明度、字体等保持原值，不得为对比度调整。
4. 保证新配色最终生效（常见覆盖陷阱）：
   - Ghostty：首选新文件自包含（不写 `theme =`、不写 `config-file`）；若必须保留原嵌套配置，复制全部依赖到工作目录并处理其中的颜色项。禁止只复制父配置后在其末尾追加颜色——嵌套文件在整个父配置之后加载，会覆盖它。
   - Kitty：新文件不 include `current-theme.conf` 和任何 `*-theme.auto.conf`；复制原配置链时去掉或改写其中的主题 include。
   - 标题栏、标签页、边框、`url_color` 等与颜色冲突的项一并检查（选项清单见 `reference/kitty-colors.md`）。

产出：两份新配置及其全部依赖，均在同一工作目录、以绝对路径互相引用。
完成标准：非颜色设置与原配置行为一致；两份文件都不依赖用户机器上的主题或外部文件。
详见两份 reference 的「生成独立配置检查单」。

## 阶段 5：验证

逐级验证，做到哪级报告哪级；没做的如实写"未执行"并给原因，不得把语法验证说成视觉验证：

1. 解析/语法：
   - Ghostty：先 `ghostty +validate-config --help` 确认本版本支持的参数，再 `ghostty +validate-config --config-file=<绝对路径>`；动作参数与普通启动不同，无法执行就如实报告。
   - Kitty：用 `kitty +runpy` 调 `kitty.config.load_config` 做解析检查，片段见 `reference/kitty-colors.md` 的「验证」；内部 API 随版本可能变化，失败如实报告。
2. 生效值核对：对照阶段 3 的 24 色清单，逐项核对两份文件的 16 ANSI 与基础色写全、无误植。
3. GUI 预览：无 GUI 环境不声称视觉验证；不自动启动终端；未经同意不用 `kitten @ set-colors` 修改正在运行的实例。

完成标准：三级各有"通过 / 无法执行+原因"的结论。

## 阶段 6：交付报告

按此模板输出：

```markdown
## 心情配色：<一句话主题>

- 心情与思路：<用户心情 → 设计取向，一两句>
- 调色板：背景/前景/主点缀 hex，对比度摘要（如 fg/bg 10.9:1，必查对全部 ≥4.5:1）
- 文件（绝对路径）：
  - Kitty：<路径>
  - Ghostty：<路径>
- 验证：语法 <通过/未执行(原因)>；24 色核对 <通过/失败项>；GUI 预览 <未执行(原因)>
- 启动命令：
  - kitty -c <绝对路径>
  - ghostty --config-default-files=false --config-file=<绝对路径>
    （macOS：open -na Ghostty.app --args --config-default-files=false --config-file=<绝对路径>）
- 注意：<透明背景影响、未解决问题等>
```

启动命令参数以本机版本支持为准（`kitty -c` 会合并到内置默认值之上、不再加载用户配置）。不自动启动、不修改默认配置，除非用户另行要求。

## 关键陷阱速查

1. Ghostty `config-file` 嵌套文件在整个父配置之后加载——末尾追加颜色会被嵌套文件覆盖。
2. Kitty `current-theme.conf` 是 `kitten themes` 写入的 include；≥0.38 的 `*-theme.auto.conf` 会覆盖包括命令行在内的一切颜色设置。
3. Kitty 选区两个颜色都为 `none` 时使用反相行为，`none` 不是普通 RGB 值。
4. 浅色主题不是深色调色板的简单反转，须按新背景重新核算全部对比度。
5. 两终端可共用同一套颜色值，但语法与优先级规则不同，生成后分别验证。

## 参考资料

- `reference/kitty-colors.md`：完整配色模板、配置发现与加载优先级、验证片段、独立启动。
- `reference/ghostty-colors.md`：完整配色模板、加载顺序与嵌套规则、验证、独立启动。
- `reference/mood-and-color.md`：研究边界、心情族起手方向、对比度手算方法、16 色语义检查表。
- 资料检索日期 2026-10-28；配置项与命令随终端版本变化，以本机帮助和官方文档为准。
