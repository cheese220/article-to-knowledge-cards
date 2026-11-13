# make-knowledge-cards

`make-knowledge-cards` 是一个用于 Codex 的知识卡片 Skill。它把较长的文章压缩成少量、可复习的知识卡片，帮助读者抓住核心内容，而不是重新浏览全文。

## 项目解决什么问题

长文章里常有重复观点、铺垫和不重要的细节，读者很难快速判断哪些内容真正值得记住。直接让模型总结又容易遗漏限定条件，或用原文没有的信息补齐内容。

本项目通过固定的提炼流程解决这些问题：

- 优先保留定义、因果关系、判断规则、关键步骤及其边界等核心知识。
- 每张卡片只讲一个知识点，合并意思重复的内容。
- 只使用输入文章中的事实，保留原文的条件、范围和不确定性。
- 有效知识点不足时不强行凑数，目标数量是 5–8 张，也可以少于 5 张。

## 主要功能

- 接收用户直接粘贴的文章，或读取用户提供的本地 `.md`、`.txt` 文件。
- 生成带编号的知识卡片，每张包含：
  - 标题
  - 核心知识
  - 简明解释
  - 忠实于原文的例子，或可依据原文回答的自测题
- 自动过滤重复、次要和解释性铺垫内容。
- 默认使用输入文章的主要语言输出。
- 默认在对话中返回 Markdown；用户明确指定文件时，可保存到指定的 `.md` 文件。
- 原文信息不足时减少卡片数量，不会通过添加外部知识或虚构案例补足。

暂不支持网页抓取、PDF、Anki 制卡和图形界面。

## 安装方法

将整个 `skills/make-knowledge-cards` 文件夹复制到 Codex 的 Skills 目录。若设置了 `CODEX_HOME`，目标位置是 `$CODEX_HOME/skills`；否则使用 `~/.codex/skills`。

在 macOS 或 Linux 中，可在项目根目录执行：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R skills/make-knowledge-cards "${CODEX_HOME:-$HOME/.codex}/skills/"
```

在 Windows PowerShell 中，可在项目根目录执行：

```powershell
$skillsDir = if ($env:CODEX_HOME) {
    Join-Path $env:CODEX_HOME 'skills'
} else {
    Join-Path $env:USERPROFILE '.codex\skills'
}
New-Item -ItemType Directory -Force -Path $skillsDir | Out-Null
Copy-Item -Path '.\skills\make-knowledge-cards' -Destination $skillsDir -Recurse -Force
```

安装完成后，确认目录中包含 `SKILL.md` 和 `agents/openai.yaml`。本项目没有运行脚本或第三方依赖。

## 使用方式

在 Codex 中显式调用 `$make-knowledge-cards`，然后粘贴文章或给出本地文件路径。

处理粘贴的文章：

```text
请使用 $make-knowledge-cards 把下面这篇文章整理成知识卡片：

[在这里粘贴文章]
```

处理本地 Markdown 或 TXT 文件：

```text
请使用 $make-knowledge-cards 读取 ./notes/article.md，并生成知识卡片。
```

默认输出 Markdown 卡片。若需要保存结果，可以明确指定目标文件：

```text
请使用 $make-knowledge-cards 读取 ./notes/article.txt，
并将知识卡片保存到 ./notes/article-cards.md。
```

## 输入输出示例

输入：

```markdown
# 间隔练习

间隔练习把练习分散到多天，而不是一次集中完成。每轮重新回忆知识，
会让后续提取更容易。把同一篇文章分几天连续重读，不一定能产生这种效果。
```

输出示例（原文只支持两个独立知识点，因此不凑满 5 张）：

```markdown
原文有效知识点有限，以下为 2 张卡片。

### 1. 间隔练习分散练习时间

**核心知识**：间隔练习要把练习分散到多天，而不是一次集中完成。

**简明解释**：它的关键区别在于练习之间留出时间，让每轮知识提取发生在不同时间。

**例子或自测**：自测：把所有练习安排在一天内完成，符合文中的间隔练习吗？参考：不符合。

### 2. 重新回忆会强化后续提取

**核心知识**：间隔后的重新回忆能让后续知识提取变得更容易。

**简明解释**：仅仅分几天重读同一篇文章，不一定能产生重新回忆带来的效果。

**例子或自测**：自测：分几天重读但从不尝试回忆，是否一定有间隔练习的效果？参考：不一定，原文没有支持这一结论。
```

Skill 定义位于 [`skills/make-knowledge-cards/SKILL.md`](skills/make-knowledge-cards/SKILL.md)，界面元数据位于 [`skills/make-knowledge-cards/agents/openai.yaml`](skills/make-knowledge-cards/agents/openai.yaml)。
