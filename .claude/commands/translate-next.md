---
description: 翻译 README checklist 中下一个待翻文件，提交并推送
allowed-tools: Bash, Read, Edit, Write, mcp__github__create_pull_request, mcp__github__list_pull_requests
---

# 翻译下一个待翻文件

你正在为 "100 Exercises to Learn Rust" 做中文翻译，严格遵循仓库根目录 `CLAUDE.md` 中的规范。

## 执行步骤

### 1. 找到下一个待翻文件

读取 `README.md`，找到**第一个** `- [ ] ⏸️` 开头的行。
- 如果找不到任何 `- [ ] ⏸️`，说明全部翻译完成。直接输出 `✅ 全部翻译完成，无可执行任务` 并退出，**不做任何修改、不创建分支、不 push**。
- 行格式形如 `- [ ] ⏸️ 02_basic_calculator/06_while.md`，提取相对路径 `02_basic_calculator/06_while.md`。

### 2. 检查英文源文件

英文源路径为 `book/src/<提取出的路径>`。
- 如果文件不存在或不是 `.md`，输出 `⚠️ 源文件 book/src/<path> 不存在，跳过` 并退出。

### 3. 切换到章节分支

从路径第一段提取章节目录（例如 `02_basic_calculator`），分支名为 `translate/<chapter_dir>`。
- 用 `git fetch origin main` 同步
- 如果当前已经在该分支，继续
- 否则：`git checkout -B translate/<chapter_dir> origin/main`（基于最新 main）

如果上一次 push 后远程已更新，先 `git pull origin translate/<chapter_dir>` 避免冲突。

### 4. 翻译文件

读取英文源文件 `book/src/<path>`，按以下规范翻译：

**强制规范**（参考 `CLAUDE.md` + 已翻译文件 `book/src/02_basic_calculator/06_while.md`、`book/src/02_basic_calculator/10_as_casting.md`）：

- 标题格式：
  - h1：`# 中文标题 (English Title)`，英文首字母大写
  - 多部分章节：`# 中文，第 N 部分 (Title, part N)`
  - h2/h3 中文术语后括号注释英文（首字母大写），例如 `## 进一步阅读` / `## 范围 (Ranges)`
- 专业术语首次出现时 `中文 (english term)`，括号内英文小写
- "Further reading" 章节统一译为 `## 进一步阅读`
- 代码块、错误信息、`println!` 字符串中的代码标识符**保持原样**；`println!` 字符串里的自然语言**翻译**
- 内部相对链接保持原样（如 `../05_ticket_v2/13_try_from.md`）
- footnote `[^name]` 标记保持原样
- 文件末尾追加：
  ```
  
  > 原文链接：[英文原文](https://github.com/mainmatter/100-exercises-to-learn-rust/blob/main/book/src/<path>)
  ```

用 Write 工具覆盖 `book/src/<path>`。

### 5. 更新 README.md checklist

把对应行的 `- [ ] ⏸️` 替换为 `- [x] ✅`，路径保持不变。

### 6. Commit & push

- `git add book/src/<path> README.md`
- 提交信息：
  ```
  翻译: <path>
  
  https://claude.ai/code/session_01QPswN6hGd5Te4NzqMVQHkA
  ```
- `git push -u origin translate/<chapter_dir>`，失败重试 4 次（2s/4s/8s/16s 退避）

### 7. 维护 draft PR

用 `mcp__github__list_pull_requests` 检查 `head=tangivis:translate/<chapter_dir>` 是否已有 open PR。
- 如果有：什么都不做（commit 已自动追加到 PR）
- 如果无：用 `mcp__github__create_pull_request` 创建 **draft** PR：
  - 标题：`翻译: 第 X 章（自动）`（X 是章节号，例如 03）
  - body 简述本章范围 + `自动翻译，请章节完成后人工审查再合并` 提示

### 8. 输出一行报告

格式：`✅ 已翻译 <path>（章 X：M/N），分支 translate/<chapter_dir>`
其中 M 是该章已完成数（README 中 `- [x] ✅ <chapter_dir>/` 计数），N 是该章总数。

## 重要约束

- **每次只翻译一个文件**，绝不批量
- **不要 merge PR**，永远保持 draft
- **不要切回 main 或其他分支**，章节分支独立工作
- 如果翻译质量自我评估存疑（术语首次出现却找不到合理中文等），在文件末尾翻译完成后追加一行 HTML 注释 `<!-- TODO: 待审 - 原因 -->`，便于人工抽检
