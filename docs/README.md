# Claude Code 与开源开发入门指南

这是一份面向**完全初学者**的中文实操指南，帮助你从零开始安装与配置 [Claude Code](https://docs.anthropic.com/en/docs/claude-code)（Anthropic 官方的命令行 AI 编码工具），并学会用它参与开源项目开发、提交 Pull Request。

如果你从未用过终端里的 AI 编码助手，也没关系——本指南会逐步讲解每一个名词、每一条命令，并给出「预期输出」示例，跟着做即可。

---

## Claude Code vs OpenCode 一句话区别

- **Claude Code**：Anthropic 官方出品的命令行（CLI）工具，深度适配 Anthropic 的 Claude 系列模型，开箱即用、体验完整。
- **OpenCode**：开源的终端 AI 编码工具，可以接入 75+ 模型提供商（如 Anthropic、OpenAI、本地模型等），灵活、不绑定单一厂商。

**简单选型建议**：

- 想要最省心、官方支持最好的体验 → 选 **Claude Code**。
- 想要自由切换不同模型、或必须使用非 Anthropic 模型 → 选 **OpenCode**。
- 二者**可以共存**，先用 Claude Code 入门，再按需尝试 OpenCode。

---

## 学习路径（建议阅读顺序）

按下面的顺序阅读，能让你从安装一路顺到参与开源贡献：

1. [01-安装与环境准备.md](01-安装与环境准备.md) — 安装 Node.js、Git、Claude Code 与 OpenCode，并验证可用。
2. [03-配置文件详解.md](03-配置文件详解.md) — 理解各类配置文件（如 `CLAUDE.md`、settings）的作用与位置。
3. [02-模型提供商配置.md](02-模型提供商配置.md) — 配置 API key、base_url、模型 ID，接入不同模型提供商。
4. [04-常用Skill与扩展.md](04-常用Skill与扩展.md) — 了解 Skill、子代理、MCP 等扩展能力。
5. [05-开源项目开发指南.md](05-开源项目开发指南.md) — 用 AI 辅助阅读与开发一个真实开源项目。
6. [06-Git指南.md](06-Git指南.md) — Git 基础与常用工作流。
7. [07-开源贡献与PR指南.md](07-开源贡献与PR指南.md) — Fork、分支、提交与发起 Pull Request 的完整流程。
8. [08-IDE集成指南.md](08-IDE集成指南.md) — 在 VS Code 等 IDE 中集成使用。
9. [09-最佳实践与排错.md](09-最佳实践与排错.md) — 高效使用技巧与常见问题排查。

---

## 关键名词速查

| 名词 | 一句话解释 |
| --- | --- |
| **Skill** | 一组可复用的能力/技能包，让 AI 工具具备特定领域的专门操作。 |
| **subagent（子代理）** | 由主 AI 派生出的、专门处理某个子任务的独立 AI 实例。 |
| **MCP** | Model Context Protocol（模型上下文协议），让 AI 工具连接外部数据源与工具的开放标准。 |
| **slash command（斜杠命令）** | 在工具中以 `/` 开头输入的快捷命令，如 `/help`、`/clear`。 |
| **provider（模型提供商）** | 提供大语言模型服务的厂商或平台，如 Anthropic、OpenAI。 |
| **base_url** | 模型 API 的服务地址（接口入口），决定请求发往哪个服务器。 |
| **token** | 模型处理文本的最小计费/计量单位，约等于一小段字词。 |
| **CLAUDE.md** | 放在项目里的说明文件，告诉 Claude Code 该项目的约定与上下文。 |

---

## 快速开始（三步）

1. **装好环境**：按 [01-安装与环境准备.md](01-安装与环境准备.md) 安装 Node.js、Git 和 Claude Code。
2. **配好模型**：按 [02-模型提供商配置.md](02-模型提供商配置.md) 完成登录或填好 `<API_KEY>`。
3. **跑第一个 prompt**：进入任意项目目录，运行 `claude`，输入你的第一句指令（例如「帮我总结这个项目的结构」），开始体验。

> ⚠️ 版本/字段可能变化，请以官方文档为准：[Claude Code 官方文档](https://docs.anthropic.com/en/docs/claude-code) ｜ [OpenCode 官方文档](https://opencode.ai/docs)
