> 返回 [📖 文档索引](README.md)

# 08 · IDE 集成指南

本篇讲解如何把 **Claude Code**（以及 **OpenCode**）和你常用的 **IDE**（集成开发环境，Integrated Development Environment，即带代码编辑、调试、运行功能的开发软件，如 VS Code、PyCharm）结合起来使用，让 AI 编程助手和图形界面工具各司其职、配合顺畅。

全程在 **Windows** 上演示。代码块开头会用注释标明运行环境（如 `# PowerShell` 或 `# Git Bash`），请在对应终端里执行。

---

## 1. 总览：把 Claude Code 当成「终端里的结对程序员」

> **结对程序员（pair programmer）** 是软件开发里的一个概念：两个人一起写代码，一个人主写、一个人在旁边审阅与提建议。把 Claude Code 想象成坐在你旁边、住在终端里的那位搭档——你说需求，它写代码、跑命令、改文件。

**IDE 和 Claude Code 各擅长什么？**

| 工具 | 擅长做的事 |
| --- | --- |
| **IDE**（VS Code / PyCharm 等） | 浏览代码、跳转定义、设断点调试、点按钮运行程序、用图形化 Git 面板查看改动 |
| **Claude Code**（终端里的 AI） | 理解需求、批量生成与修改代码、自动跑测试、解释陌生代码、执行多步骤任务 |

**什么时候用 IDE、什么时候切到 Claude Code？**

- **用 IDE**：当你想**手动**精读某个文件、单步**调试**一个 bug、用鼠标点击运行/停止程序、或在 Git 面板里逐行审阅改动时。
- **切到 Claude Code**：当你想**用一句话描述需求**让 AI 去实现、让它**跨多个文件**改动、让它**自动跑测试并根据结果修复**、或让它**解释**一段你看不懂的代码时。

二者并不冲突。最常见的高效姿势是：**在 IDE 里编辑和浏览，在同一个 IDE 内置的终端里运行 `claude`**，让两者共享同一个项目、同一个窗口。下文会具体演示。

---

## 2. VS Code 集成

> **VS Code**（Visual Studio Code）是微软出品的免费、轻量、跨平台代码编辑器，是目前最流行的 IDE 之一。如果你还没安装，可从 [https://code.visualstudio.com/](https://code.visualstudio.com/) 下载，或用 winget 安装：
>
> ```powershell
> # PowerShell
> winget install Microsoft.VisualStudioCode
> ```

### 2.1 安装 Claude Code 扩展

> **扩展（extension）**，也叫「插件」，是给 IDE 增加额外功能的小程序。VS Code 有一个**扩展市场（Marketplace）**，可以在里面搜索并一键安装。

**方式一：从扩展市场安装**

1. 打开 VS Code，点击左侧活动栏的**扩展**图标（四个方块组成的图标），或按快捷键 `Ctrl+Shift+X`。
2. 在搜索框输入 **Claude Code**。
3. 找到由 Anthropic 发布的官方扩展，点击 **Install（安装）**。

**方式二：在集成终端里直接运行 `claude` 自动检测 IDE**

VS Code 自带一个**集成终端（integrated terminal）**——就是嵌在编辑器底部的命令行窗口。打开方式：菜单 **Terminal（终端）→ New Terminal（新建终端）**，或按 `` Ctrl+` ``（数字键 1 左边的反引号键）。

在这个集成终端里直接运行：

```powershell
# PowerShell
claude
```

Claude Code 会**自动检测**到自己正运行在 VS Code 里，并在需要时提示你安装/启用配套扩展。按提示同意即可。

> ⚠️ 扩展的名称、发布者和安装入口可能随版本变化，请以官方为准：[Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code)。

### 2.2 在 VS Code 里使用 Claude Code 的三个核心能力

**能力一：内联 diff 审阅**

> **diff**（差异）指代码改动前后的对比；**内联（inline）** 指直接显示在编辑器里。

当 Claude Code 修改文件时，VS Code 扩展会把改动以**内联 diff** 的形式展示出来：删除的行标红、新增的行标绿，直接显示在你正在看的文件中。你可以在动手合并前清楚看到「它到底改了什么」，然后选择**接受（Accept）** 或 **拒绝（Reject）**。这一步非常重要——永远先看清 diff 再接受（见 [09-最佳实践与排错.md](09-最佳实践与排错.md)）。

**能力二：在集成终端中使用**

把 `claude` 跑在 VS Code 内置终端里，好处是：AI 改完文件后，你**立刻在左边的编辑器里看到变化**，无需切换窗口；它跑的命令、报的错也都在同一个界面里，便于对照。

**能力三：选中代码作为上下文**

在编辑器里**用鼠标选中**一段代码后再向 Claude Code 提问，扩展会把这段选中的代码作为**上下文（context，即 AI 回答时参考的背景信息）** 一起发给 AI。例如选中一个函数后问「这个函数有什么 bug？」，AI 就知道你指的是哪段代码，回答更精准。

### 2.3 与 OpenCode 协同

OpenCode 是另一个终端 AI 编码工具（见 [01-安装与环境准备.md](01-安装与环境准备.md)）。它同样可以跑在 VS Code 的集成终端里：

```powershell
# PowerShell
opencode
```

由于 VS Code 的终端支持多标签，你完全可以**开两个终端标签页**，一个跑 `claude`、一个跑 `opencode`，按需在两者之间切换对照。

### 2.4 推荐配套插件

下面这些插件能让你和 AI 协作时更顺手（搜索名称、点击 Install 即可）：

| 插件 | 作用 |
| --- | --- |
| **GitLens** | 增强 Git 功能，能逐行查看每段代码是谁、什么时候、为什么改的（配合 [06-Git指南.md](06-Git指南.md)）。 |
| **Python + Pylance** | 写 Python 时提供智能补全、类型检查、错误提示（Pylance 是语言分析引擎）。 |
| **ESLint** | 检查 JavaScript/TypeScript 代码中的潜在问题与风格错误。 |
| **Prettier** | 自动格式化代码（统一缩进、引号、换行风格），保存时一键美化。 |

> 小贴士：装了 ESLint/Prettier 后，可以在 `CLAUDE.md` 里告诉 Claude Code「本项目用 Prettier 格式化、用 ESLint 检查」，它就会遵守项目风格（`CLAUDE.md` 的写法见 [03-配置文件详解.md](03-配置文件详解.md)）。

---

## 3. PyCharm / JetBrains 集成

> **JetBrains** 是一家做开发工具的公司，旗下有一系列 IDE：**PyCharm**（Python）、**IntelliJ IDEA**（Java）、**WebStorm**（前端）、**GoLand**（Go）等。它们界面相似、插件体系相通，下面以 **PyCharm** 为例，其他 JetBrains 系产品步骤一致。

### 3.1 安装 Claude Code 插件

**方式一：从 JetBrains Marketplace 安装插件**

> **JetBrains Marketplace** 是 JetBrains 系 IDE 的官方插件市场。

1. 打开 PyCharm，进入菜单 **File（文件）→ Settings（设置）**（macOS 上是 **Preferences**）。
2. 在左侧选择 **Plugins（插件）**。
3. 切到 **Marketplace** 标签，搜索 **Claude Code**。
4. 找到官方插件，点击 **Install（安装）**，安装后按提示 **Restart（重启）** IDE。

**方式二：直接用内置 Terminal 运行 `claude`**

PyCharm 也自带终端。打开方式：菜单 **View（视图）→ Tool Windows（工具窗口）→ Terminal**，或点击 IDE 左下角的 **Terminal** 图标。在里面运行：

```powershell
# PowerShell
claude
```

和 VS Code 一样，Claude Code 会自动检测到 JetBrains IDE 环境。

> ⚠️ 插件名称与安装入口可能随版本变化，请以官方为准：[Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code)。

### 3.2 与虚拟环境 / 解释器配合

> **虚拟环境（virtual environment，简称 venv）** 是 Python 里一种「项目专属的隔离安装空间」，让不同项目各自安装各自版本的依赖库，互不污染。**解释器（interpreter）** 指实际运行 Python 代码的那个 `python.exe`。

这是 PyCharm 里最关键的一点：**你要让 Claude Code 在正确的虚拟环境里跑测试和命令**，否则它可能找不到依赖库、报「ModuleNotFoundError」之类的错。

做法：

1. 先在 PyCharm 里把项目的解释器设为目标 venv（菜单 **File → Settings → Project → Python Interpreter**，选中或新建对应的 venv）。
2. **从 PyCharm 内置 Terminal 启动 `claude`**。PyCharm 的内置终端通常会**自动激活**当前项目选定的 venv（你会在终端提示符前看到形如 `(venv)` 的前缀）。
3. 这样 Claude Code 后续跑 `pytest`、`pip install` 等命令时，就在正确的环境里执行了。

如果终端没有自动激活，也可以手动激活后再启动：

```powershell
# PowerShell
.\.venv\Scripts\Activate.ps1
claude
```

> 看到提示符前出现 `(.venv)` 字样，说明虚拟环境已激活，此时再让 Claude Code 跑测试就会用对环境。

---

## 4. 常见 IDE 使用技巧

下面是一套把 IDE 与 Claude Code 揉在一起的高效工作流，适合日常开发：

1. **在 IDE 里编辑、在 Claude Code 里生成与审查**：手动调小改动用编辑器，大段生成/重构交给 AI；AI 改完后用内联 diff 审阅，确认无误再接受。
2. **保存后让 Claude Code 跑测试**：在编辑器里改完并保存文件，回到 Claude Code 输入「跑一下测试」，让它执行测试命令（如 `pytest`、`npm test`）并根据失败信息自动修复。
3. **用 IDE 的 Git 面板配合命令行流程**：用 VS Code/PyCharm 的图形化 **Git 面板**直观地查看改动、暂存（stage）、撰写提交信息、查看历史，再结合 [06-Git指南.md](06-Git指南.md) 的命令行流程与 [07-开源贡献与PR指南.md](07-开源贡献与PR指南.md) 的 Pull Request 流程完成贡献。
4. **一个窗口、两件工具**：始终把 `claude` 跑在 IDE 的内置终端里，让编辑器与 AI 共享同一个项目目录，改动实时可见，省去来回切窗口。
5. **善用选中即上下文**：遇到看不懂的函数，先在编辑器里选中它，再问 Claude Code「解释这段代码」或「这里有没有性能问题」，回答会更贴合你指的位置。

---

## 下一步

- 学习高效使用技巧与排查常见问题：前往 [09-最佳实践与排错.md](09-最佳实践与排错.md)
- 复习 Git 与 PR 流程：[06-Git指南.md](06-Git指南.md)、[07-开源贡献与PR指南.md](07-开源贡献与PR指南.md)

> ⚠️ IDE 扩展/插件的功能与入口更新较快，遇到与本文描述不符之处，请以官方文档为准：[Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code) ｜ [OpenCode 官方文档](https://opencode.ai/docs)
