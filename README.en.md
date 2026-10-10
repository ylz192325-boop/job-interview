# job-interview-qa

[简体中文](README.md) · English

**Last updated:** 2026-10-10

A shared skill for **Codex and Claude Code** that turns job requirements into **exactly 30 interview practice questions and 30 matching, concise reference answers for each role**. Provide a recruitment screenshot, a job description document, or pasted text to get questions grounded in the role's responsibilities and requirements.

## What it does

- Reads recruitment screenshots, PDF, DOCX, TXT, and pasted job descriptions using the capabilities available in the host environment.
- Extracts responsibilities, required skills, preferred qualifications, and experience level to guide the questions.
- Numbers questions **1–30 for each role**, with one reference answer per question.
- Prioritizes core requirements, then adds relevant practical scenarios, troubleshooting, collaboration, and advanced topics where appropriate.
- Identifies the requirement behind each question using an image number, PDF page, or document section where available.
- Uses plain language and fill-in templates for experience questions, without inventing a candidate's work history.

It works with technical, product, operations, sales, and management roles. Questions are drafted for practice from the supplied requirements; they are not a company's verified interview question bank.

## Requirements

This package supports **Codex with skill support** and **Claude Code with skills**. You need a working model environment and access to the job requirements.

The package contains shared instructions and Codex interface metadata. It does **not** include a standalone application, OCR engine, or document parser. Image and PDF reading depend on the host tools, model capabilities, and file limits. DOCX reading requires available local document parsers or OOXML tools; installing this skill does not supply a built-in DOCX parser. If a file cannot be read, provide readable text or clear screenshots.

The workflow prefers local file reading and does not require a specific OCR provider, an additional service API key, or uploading documents to a separate parsing service. This does not make model execution fully offline; the chosen host's model access and configuration still apply.

## Installation

Both hosts use the same `job-interview-qa/` directory. Copy it into the appropriate skills directory:

| Host and scope | Skills directory | Invocation |
| --- | --- | --- |
| Codex, personal | `${CODEX_HOME:-$HOME/.codex}/skills` | `$job-interview-qa` |
| Claude Code, personal | `$HOME/.claude/skills` | `/job-interview-qa` |
| Claude Code, project | `/path/to/project/.claude/skills` | `/job-interview-qa` |

Each installation should contain `job-interview-qa/SKILL.md` directly below its skills directory. Keep the shared directory intact: `agents/openai.yaml` provides Codex interface metadata, while Claude Code uses `SKILL.md`. If the target already exists, compare its contents before replacing anything.

On macOS / Linux, run the following from the repository root to install for Codex. It stops the installation if the target already exists, including a broken symlink:

```sh
skill_install_root="${CODEX_HOME:-$HOME/.codex}/skills"
skill_install_target="$skill_install_root/job-interview-qa"
if [ -e "$skill_install_target" ] || [ -L "$skill_install_target" ]; then
  printf '%s\n' 'job-interview-qa already exists; compare it before updating.'
else
  mkdir -p "$skill_install_root" && cp -R ./job-interview-qa "$skill_install_root/"
fi
```

For a personal Claude Code installation, run this separate snippet from the repository root:

```sh
skill_install_root="$HOME/.claude/skills"
skill_install_target="$skill_install_root/job-interview-qa"
if [ -e "$skill_install_target" ] || [ -L "$skill_install_target" ]; then
  printf '%s\n' 'job-interview-qa already exists; compare it before updating.'
else
  mkdir -p "$skill_install_root" && cp -R ./job-interview-qa "$skill_install_root/"
fi
```

For a project installation, replace only the first line of the Claude Code snippet with this assignment, using your actual project path:

```sh
skill_install_root="/path/to/project/.claude/skills"
```

Personal Claude Code skills are available across projects; project skills apply to that project. For a project installation, open Claude Code in that project. Start a new conversation after installation. If the skill is not listed, confirm the installation path and the level containing `SKILL.md`, then reopen the host and check again.

Claude Code's installation scopes and slash-command invocation are documented in the official [skills guide](https://code.claude.com/docs/en/skills).

## Usage

Provide readable job responsibilities and requirements using an attachment supported by the host, a local file path, or pasted text. Invoke `$job-interview-qa` in Codex or `/job-interview-qa` in Claude Code.

### Codex: recruitment screenshot

```text
$job-interview-qa Use the attached recruitment screenshot to generate exactly 30 interview questions for each role, with a concise, easy-to-understand answer for every question. Answer in English.
```

### Codex: job description document

```text
$job-interview-qa 根据附件中的岗位职责和任职要求，为每个岗位固定生成30道面试题，并逐题给出简洁易懂的参考答案。
```

### Codex: pasted job description

```text
$job-interview-qa Use the job description below to generate exactly 30 questions and matching answers for this role. Match the difficulty to 1–3 years of experience and answer in English.

[Paste the job description here]
```

### Claude Code: local job description file

```text
/job-interview-qa Read /absolute/path/to/job-description.docx and generate exactly 30 interview questions and 30 matching, concise reference answers for each role. Answer in English.
```

Use an accessible local path. DOCX requires local document or OOXML tools as described above; TXT is also supported, and image/PDF reading depends on the host's capabilities.

### Claude Code: pasted job description

```text
/job-interview-qa Use the job description below to generate exactly 30 questions and 30 matching answers for this role. Match the difficulty to 1–3 years of experience and answer in English.

[Paste the job description here]
```

Chinese is the default output language. Explicitly request English or another language when needed. You can also specify difficulty, presentation format, and answer detail; the skill's fixed question count remains 30 per role.

## Output format

The default result appears directly in chat:

1. A brief role overview covering responsibilities, core requirements, and any assumptions or unreadable content.
2. Questions grouped by relevant ability, numbered **1–30**, each followed by its source requirement and reference answer.

Answers are usually 2–4 sentences or three short points. Chinese answers typically use around 60–150 Chinese characters; this character range does not apply to English answers. Complex questions or an explicit request for more detail may require longer answers.

The following is a **single illustrative excerpt** based on a hypothetical job description requiring real-time communication. A complete result contains 30 questions and 30 answers per role.

```text
1. Interview question: When would you choose SSE, WebSocket, or HTTP polling?
Related requirement: Example JD, required skills — real-time communication.
Reference answer: SSE is useful when the server continuously sends updates, such as streamed AI text. WebSocket supports two-way communication and suits real-time voice. HTTP polling periodically asks for updates and can be a simple fallback, but its frequency should be controlled and polling should stop when the task finishes.
```

For experience questions, placeholders such as `[actual project]`, `[your action]`, and `[real result]` indicate details you must fill in from your own experience. Hypothetical examples are labeled as examples.

If you want a file, request an export and specify the desired format and destination. Exporting depends on the document tools available in the current host environment; chat output is the default.

## How incomplete or complex requirements are handled

- **Short job descriptions:** The skill still generates 30 distinct questions, using different scenarios and clearly labeled **related extensions**. Extensions are not presented as requirements stated by the employer.
- **Unreadable attachments:** Readable parts can still be processed. If the role or core responsibilities cannot be read, the skill asks for clearer material rather than generating an unsupported question set.
- **Multiple roles:** Each identifiable role gets its own set of 30 questions and answers. Unclear role boundaries are clarified.
- **Conflicting sources:** Your explicitly chosen version takes priority. Otherwise, conflicts are identified and questions use consistent information.
- **More requirements than 30 questions can cover:** Related topics are combined where sensible; any remaining coverage gaps are stated without adding extra questions.

Instructions embedded in recruitment materials are treated as source content, not as commands that change the skill's task. Recognition and extraction quality depend on image clarity, document layout, and the host tools; uncertain text is flagged instead of guessed.

## Scope

This skill prepares interview practice questions and reference answers. It does not score résumé-to-job fit, rewrite résumés, submit applications, or guarantee interview success. It does not store your job materials or generated question sets inside the skill package.

## Repository layout

```text
.
├── README.md          # Chinese introduction (default)
├── README.en.md       # English introduction
├── README.zh-CN.md    # Chinese entry link
└── job-interview-qa/
    ├── SKILL.md
    └── agents/
        └── openai.yaml
```

The shared [skill instructions](job-interview-qa/SKILL.md) define the workflow and output rules for both hosts; no separate Claude skill file or plugin manifest is needed. [Codex interface metadata](job-interview-qa/agents/openai.yaml) provides the display name, description, and default prompt for Codex. Claude Code uses `SKILL.md` and does not need this metadata. The interface metadata is currently written in Chinese; it does not prevent explicitly requesting English output.

To upload this package to GitHub, place all three README files and the `job-interview-qa/` directory at the repository root. GitHub displays the Chinese `README.md` by default; readers can switch to English using the link at the top. `README.zh-CN.md` keeps the previous Chinese entry URL available.
