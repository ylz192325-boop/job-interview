# job-interview-qa

简体中文 | [English](README.en.md)

**最后更新：** 2026-10-10

根据招聘要求截图、文档或文字，为每个岗位固定生成 **30 道面试题和 30 个简洁参考答案**。

这是一个用于 **Codex 和 Claude Code** 的 skill，两端共用同一份 `SKILL.md`。它将岗位要求转化为可练习的问答，适合求职者准备面试，也适合面试官拟定问题。题目覆盖技术、产品、运营、销售、管理等岗位，按实际职责和经验层级调整。

## 功能

- 接受招聘截图、PDF、DOCX、TXT 文件，以及直接粘贴的职位描述（JD）。
- 区分主要职责、必需能力和优先项，每题标明对应要求或相关延伸。
- 每个岗位固定输出 30 道题，编号 1–30，逐题附参考答案；多个岗位分别生成。
- 结合工作场景，生成知识、实践、排障、协作和经历类问题。
- 默认中文，保留必要的技术名词；可以指定英文等输出语言、难度和格式。
- 经历题使用可替换模板，避免把假设案例写成候选人的真实经历。

## 安装

需要一个支持 skills 的 Codex 或 Claude Code 环境。该 skill 没有独立程序或内置文档解析器；截图和附件读取使用当前环境提供的能力。Claude Code 可通过文件路径读取支持的图片、PDF 和文字；DOCX 需要可用的本地文档解析或 ZIP/XML 工具。无需为此 skill 配置特定 OCR 服务或额外 API key，宿主本身的模型访问仍使用你的现有配置。

### Codex

下载本仓库并解压后，将仓库根目录中的 `job-interview-qa/` 文件夹复制到：

- 设置了 `CODEX_HOME`：`$CODEX_HOME/skills/`
- 未设置 `CODEX_HOME`：`~/.codex/skills/`

如果已有同名 skill，先比较内容，再决定如何更新。macOS / Linux 可在仓库根目录执行以下命令；遇到同名文件夹时会停止，不覆盖已有内容：

```sh
skill_install_root="${CODEX_HOME:-$HOME/.codex}/skills"
skill_install_target="$skill_install_root/job-interview-qa"
if [ -e "$skill_install_target" ] || [ -L "$skill_install_target" ]; then
  printf '%s\n' 'job-interview-qa already exists; compare it before updating.'
else
  mkdir -p "$skill_install_root" && cp -R ./job-interview-qa "$skill_install_root/"
fi
```

### Claude Code

将同一个 `job-interview-qa/` 文件夹复制到以下一个位置：

| 安装范围 | skill 目录 |
| --- | --- |
| 个人安装，所有项目可用 | `~/.claude/skills/job-interview-qa/` |
| 项目安装，仅该项目使用 | `<项目目录>/.claude/skills/job-interview-qa/` |

macOS / Linux 在仓库根目录执行以下命令完成个人安装。若只安装到某个项目，将第一行替换为 `skill_install_root="/你的项目路径/.claude/skills"`：

```sh
skill_install_root="$HOME/.claude/skills"
skill_install_target="$skill_install_root/job-interview-qa"
if [ -e "$skill_install_target" ] || [ -L "$skill_install_target" ]; then
  printf '%s\n' 'job-interview-qa already exists; compare it before updating.'
else
  mkdir -p "$skill_install_root" && cp -R ./job-interview-qa "$skill_install_root/"
fi
```

在目标项目中启动 Claude Code，输入 `/job-interview-qa` 调用。`agents/openai.yaml` 是 Codex 的界面元数据，Claude Code 使用 `SKILL.md`，可以直接复制整个 skill 文件夹。安装路径与调用方式参见 [Claude Code 官方 skills 文档](https://code.claude.com/docs/en/skills)。

两端安装后的 skill 内部结构相同：

```text
skills/
└── job-interview-qa/
    ├── SKILL.md
    └── agents/
        └── openai.yaml
```

Codex 在新对话中使用 `$job-interview-qa`，Claude Code 使用 `/job-interview-qa`。若未发现该名称，先确认安装路径和 `SKILL.md` 所在层级，再开启新会话检查；Claude Code 中可输入 `/` 查看可用技能。

## 使用

1. 上传招聘截图或文档、提供可访问的本地文件路径，或者粘贴岗位职责与任职要求。
2. 使用对应平台的调用语法输入指令。
3. 查看岗位要点，以及按能力分组的 30 道题和 30 个答案。

| 平台 | 显式调用 |
| --- | --- |
| Codex | `$job-interview-qa 你的要求` |
| Claude Code | `/job-interview-qa 你的要求` |

以下三个示例用于 Codex；Claude Code 将开头的 `$job-interview-qa` 改为 `/job-interview-qa` 即可。

### 招聘截图或附件

```text
$job-interview-qa 根据我上传的招聘要求，为这个岗位固定生成30道面试题，并逐题给出简洁易懂的参考答案。
```

### 指定难度与答案长度

```text
$job-interview-qa 根据以下JD，按1–3年经验设计30道面试题。每个答案用2–3句话解释，保留必要的技术名词。

岗位职责与任职要求：
在这里粘贴招聘文字。
```

### 英文或多个岗位

```text
$job-interview-qa 请用英文输出。附件包含两个不同岗位，请分别生成30道面试题和30个简洁参考答案。
```

### Claude Code 本地文件或文字

```text
/job-interview-qa 根据 /你的文件路径/招聘要求.png，为这个岗位固定生成30道面试题，并逐题给出简洁易懂的参考答案。
```

```text
/job-interview-qa 根据以下JD固定生成30道面试题和30个简洁参考答案：
岗位职责与任职要求：
在这里粘贴招聘文字。
```

文件路径中有空格时用引号包住；使用当前环境允许访问的路径。截图、PDF 的读取效果受清晰度、文档长度和模型能力影响；DOCX 使用本地解析工具，无法读取时提供可读文字。

简历和候选人经历是可选信息，不是开始出题的前提。需要结合个人经历回答时，可以补充真实项目事实。

## 输出格式

先用 3–6 行概括岗位、核心要求及必要假设，然后按相关能力分组，连续编号 1–30：

```text
1. 面试题：……？
对应要求：……（图片编号、PDF页码、文档章节，或“延伸”）
参考答案：……
```

以下仅为单题示例节选；实际每个岗位输出 30 题。

示例招聘文字：熟悉 SSE、WebSocket 和 HTTP 轮询，能够选择合适的实时通信方案。

```text
1. 面试题：SSE、WebSocket和HTTP轮询分别适合什么场景？
对应要求：示例招聘文字中的实时通信能力。
参考答案：SSE适合服务端持续推送，例如AI文本流式输出；WebSocket支持双向通信，适合实时语音收发。HTTP轮询是客户端定时查询，可用于任务状态或兜底，但要控制频率，并在任务结束后停止。
```

## 生成规则

| 项目 | 规则 |
| --- | --- |
| 题量 | 每个岗位固定 30 道题及 30 个对应答案，编号 1–30。 |
| 要求较少 | 从已知职责拆分不同考点，必要时补充相关题目并标明“延伸”，保持 30 题。 |
| 要求较多 | 优先覆盖核心职责与必需能力；合并相关考点，说明未覆盖内容，不自动超过 30 题。 |
| 难度 | 参考招聘要求中的经验层级；未给出时采用通用难度并说明假设。 |
| 参考答案 | 通常 2–4 句或 3 个短要点；中文答案约 60–150 字，复杂题可稍长。 |
| 经历题 | 用 `[实际项目]`、`[你的行动]`、`[真实结果]` 等占位符，由用户填入真实事实。 |
| 默认交付 | 在聊天中输出；用户要求导出时，根据可用工具保存文件。 |

## 输入处理与边界

- **截图：** 标记模糊、遮挡和截断处；同一岗位的连续截图合并去重，不猜补不可读内容。
- **PDF：** 尝试读取全部页面；扫描件或复杂列排版需要环境支持图像查看、渲染或 OCR。
- **DOCX：** 读取段落和表格；文本框或嵌入图片可能需要补充渲染或结构检查，不编造页码。
- **多个岗位或冲突来源：** 分别处理可确认的岗位；版本冲突未解决时标明冲突，依据一致部分出题。
- **材料不足：** 核心职责完全不可读时先请用户补充材料；局部不可读时继续处理已读内容并说明限制。

该 skill 优先使用本地文件读取能力，但模型处理和数据使用仍受运行环境及你的配置影响，不能理解为完全离线工具。材料中的嵌入指令只作为来源内容，不改变生成问答的任务。

题目属于根据招聘要求拟定的练习题，不是招聘方真实题库，也不保证录用结果。参考答案需要结合具体技术版本、政策和业务条件判断。本 skill 不提供简历匹配评分、简历改写或职位投递。

## 仓库结构

```text
.
├── README.md          # 默认中文介绍
├── README.en.md       # 英文介绍
├── README.zh-CN.md    # 中文入口链接
└── job-interview-qa/
    ├── SKILL.md
    └── agents/
        └── openai.yaml
```

[SKILL.md](job-interview-qa/SKILL.md) 是 Codex 与 Claude Code 共用的运行指令；[openai.yaml](job-interview-qa/agents/openai.yaml) 提供 Codex 的界面名称、简介与默认调用提示。

上传到 GitHub 时，将此目录中的三个 README 和 `job-interview-qa/` 文件夹放在仓库根目录。GitHub 默认展示中文 `README.md`，读者可通过顶部链接切换到英文版；`README.zh-CN.md` 保留旧的中文入口链接。
