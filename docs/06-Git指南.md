> 返回 [📖 文档索引](README.md)

# 06 · Git 指南

本指南面向 **Git 与开源贡献的初学者**。Git 是一个「版本控制系统」（Version Control System，简称 VCS）——它会记录你的项目文件每一次变化，让你可以回退、对比、协作。读完本篇你将理解 Git 的工作原理，并掌握日常最常用的命令。

> 阅读顺序建议：先看本篇，再看 [07-开源贡献与PR指南.md](07-开源贡献与PR指南.md)。

---

## 1. Git 心智模型：四个区域

要用好 Git，最重要的是理解你的代码可以处在 **四个区域**。几乎所有命令做的事情，本质上都是「把改动从一个区域搬到另一个区域」。

| 区域 | 英文 | 通俗解释 |
| --- | --- | --- |
| 工作区 | Working Directory | 你正在编辑的文件夹，所见即所得 |
| 暂存区 | Stage / Index | 「即将提交」的草稿箱，挑选哪些改动进入下一次提交 |
| 本地仓库 | Local Repository | 你电脑里的版本历史数据库（`.git` 目录） |
| 远程仓库 | Remote Repository | 托管在 GitHub/GitLab 等服务器上的仓库 |

### 改动如何在区域间流动

```
  工作区              暂存区             本地仓库            远程仓库
（你编辑文件）      （挑选改动）       （生成历史快照）    （GitHub 等）
     │                  │                   │                  │
     │  git add         │                   │                  │
     ├─────────────────►│                   │                  │
     │                  │  git commit       │                  │
     │                  ├──────────────────►│                  │
     │                  │                   │   git push       │
     │                  │                   ├─────────────────►│
     │                  │                   │                  │
     │◄─────────────────┴───────────────────┴──────────────────┤
     │           git pull / git fetch（把远程的改动拉回来）       │
```

用一句话串起来：

1. 你在 **工作区** 改文件。
2. `git add` 把选中的改动放进 **暂存区**。
3. `git commit` 把暂存区的内容打包成一个「快照」，存进 **本地仓库**。
4. `git push` 把本地仓库的新快照上传到 **远程仓库**。
5. `git pull`（= `fetch` + `merge`）把别人在远程的改动拉回到你的本地与工作区。

> 关键点：`add` 之后、`commit` 之前，改动只在暂存区，还没进历史；`commit` 之后才真正记入本地仓库；只有 `push` 之后，别人才能在远程看到。

---

## 2. 首次配置

安装好 Git 后，第一次使用前请设置你的身份。这些信息会写进你每一次提交的记录里。

```bash
# 终端
git config --global user.name "你的名字"
git config --global user.email "honglizeng1013@gmail.com"
```

- `--global` 表示「对当前电脑上所有仓库生效」。去掉它则只对当前仓库生效。
- 邮箱建议与你的 GitHub 账号邮箱一致，这样提交才能正确关联到你的头像与贡献记录。

### 换行符设置（Windows 重要）

Windows 的换行符是 `CRLF`，而 Linux/macOS 是 `LF`。不统一会导致「整个文件都显示被改动」的假象。Windows 用户建议：

```bash
# 终端
git config --global core.autocrlf true
```

- `true`：检出（checkout）时转成 CRLF，提交时自动转回 LF。**Windows 推荐**。
- `input`：提交时转成 LF，检出时不转。（macOS/Linux 常用）

查看当前所有配置：

```bash
# 终端
git config --list
```

预期输出（节选）：

```
user.name=你的名字
user.email=honglizeng1013@gmail.com
core.autocrlf=true
```

---

## 3. 常用命令速查

下面按用途分组。每条命令配一句话说明和一个典型示例。

### 3.1 创建 / 克隆

| 命令 | 一句话说明 |
| --- | --- |
| `git init` | 在当前目录创建一个新的本地仓库 |
| `git clone <url>` | 把远程仓库完整下载到本地 |

```bash
# 终端
git init                                          # 把现有文件夹变成 Git 仓库
git clone https://github.com/用户名/项目名.git    # 克隆远程仓库
```

`clone` 成功后会自动建立一个名为 `origin` 的远程引用，指向你克隆的地址。

### 3.2 查看状态与历史

| 命令 | 一句话说明 |
| --- | --- |
| `git status` | 查看哪些文件被改动、是否已暂存 |
| `git log --oneline --graph` | 用一行一个、带分支图的方式看提交历史 |
| `git diff` | 查看工作区与暂存区之间的具体改动 |

```bash
# 终端
git status
git log --oneline --graph --all       # --all 表示显示所有分支
git diff                              # 看还没 add 的改动
git diff --staged                    # 看已经 add、还没 commit 的改动
```

`git status` 的预期输出示例：

```
On branch main
Changes not staged for commit:
  modified:   README.md

Untracked files:
  notes.txt
```

`git log --oneline --graph` 的预期输出示例：

```
* a1b2c3d (HEAD -> main) docs: 补充安装说明
* 9f8e7d6 feat: 新增登录功能
* 1234567 init: 初始化项目
```

### 3.3 暂存与提交

| 命令 | 一句话说明 |
| --- | --- |
| `git add <文件>` | 把改动放入暂存区 |
| `git commit -m "信息"` | 把暂存区内容打包成一次提交 |
| `git restore <文件>` | 丢弃工作区里对该文件的改动（恢复到上次提交的样子） |
| `git reset` | 把改动从暂存区撤回到工作区（取消 add） |

```bash
# 终端
git add README.md            # 暂存单个文件
git add .                    # 暂存当前目录所有改动
git commit -m "docs: 更新说明"

git restore README.md        # 撤销对 README.md 的未暂存改动（危险：会丢失改动）
git reset README.md          # 把 README.md 移出暂存区，改动仍保留在工作区
```

> 区分：`restore` 默认动的是 **工作区**（丢改动）；`reset <文件>` 动的是 **暂存区**（取消 add，不丢改动）。

### 3.4 分支

分支（branch）让你在不影响主线的情况下并行开发。

| 命令 | 一句话说明 |
| --- | --- |
| `git branch` | 列出本地分支 |
| `git switch -c <名>` | 创建并切换到新分支（新写法，推荐） |
| `git checkout <名>` | 切换分支（旧写法，仍通用） |
| `git merge <名>` | 把指定分支合并到当前分支 |

```bash
# 终端
git branch                      # 查看分支，带 * 的是当前分支
git switch -c feature/login     # 新建并切到 feature/login 分支
git switch main                 # 切回 main 分支
git merge feature/login         # 在 main 上把 feature/login 合并进来
```

> `switch` 是较新的命令（Git 2.23+），语义更清晰，专门用于切换分支；`checkout` 功能更杂（既能切分支也能恢复文件），容易混淆。初学者优先用 `switch`。

### 3.5 远程协作

| 命令 | 一句话说明 |
| --- | --- |
| `git remote -v` | 查看已配置的远程仓库地址 |
| `git fetch` | 把远程的更新下载到本地，但不合并 |
| `git pull` | `fetch` + `merge`，下载并合并到当前分支 |
| `git push -u origin <分支>` | 把本地分支推送到远程，并建立跟踪关系 |

```bash
# 终端
git remote -v
git fetch origin
git pull
git push -u origin feature/login   # 首次推送新分支用 -u，之后直接 git push 即可
```

`git remote -v` 的预期输出示例：

```
origin  https://github.com/你的名字/项目名.git (fetch)
origin  https://github.com/你的名字/项目名.git (push)
```

> `-u` 是 `--set-upstream` 的简写：建立本地分支与远程分支的「跟踪关系」，之后只需 `git push` / `git pull` 不必再写分支名。

### 3.6 临时保存：stash

当你改到一半，又需要临时切到别的分支处理急事，但改动还不想提交时，用 `stash` 把改动暂时收起来。

| 命令 | 一句话说明 |
| --- | --- |
| `git stash` | 把当前未提交的改动暂存起来，工作区恢复干净 |
| `git stash pop` | 把最近一次 stash 的改动取回来并删除该记录 |

```bash
# 终端
git stash            # 收起当前改动
git switch main      # 去处理别的事
# ……处理完后切回原分支……
git stash pop        # 把改动恢复回来
```

---

## 4. 提交信息规范（Conventional Commits）

好的提交信息能让别人（和未来的你）一眼看懂这次改了什么。社区广泛使用 **Conventional Commits** 约定，格式为：

```
<类型>: <简短描述>
```

常用类型：

| 类型 | 含义 |
| --- | --- |
| `feat` | 新增功能 |
| `fix` | 修复 bug |
| `docs` | 仅文档改动 |
| `refactor` | 重构（不改变功能行为） |
| `test` | 增删或修改测试 |
| `chore` | 杂项（构建、依赖、配置等） |

### 好的示例

```
feat: 支持邮箱登录
fix: 修复购物车数量为负数的问题
docs: 补充 API 鉴权说明
refactor: 抽取日期格式化工具函数
```

### 不好的示例

```
更新                    # 看不出改了什么
fix bug                 # 哪个 bug？
asdfgh                  # 无意义
修改了好多东西还有测试    # 一次提交做太多事，描述含糊
```

> 经验法则：一次提交只做一件事；描述用「动词 + 对象」，让人不看代码也大致知道发生了什么。

---

## 5. 撤销与后悔药

Git 提供了多种「后悔药」，但请务必看清适用场景——有的会**永久丢失改动**。

### 5.1 改错了某个文件，想丢弃改动

```bash
# 终端
git restore 文件名          # 丢弃该文件「未暂存」的改动（不可恢复，请谨慎）
```

### 5.2 已经 add 了，想取消暂存（但保留改动）

```bash
# 终端
git restore --staged 文件名    # 等价于 git reset 文件名
```

### 5.3 提交信息写错了（且还没 push）

```bash
# 终端
git commit --amend -m "fix: 正确的提交信息"
```

`--amend` 会「修改最近一次提交」。注意：如果这次提交已经 push 到远程并被别人拉取，请**不要** amend，否则会造成历史冲突。

### 5.4 本地回退到某次提交（谨慎使用 reset）

```bash
# 终端
git reset --soft  HEAD~1    # 撤销最近 1 次提交，改动留在暂存区
git reset --mixed HEAD~1    # 撤销最近 1 次提交，改动留在工作区（默认）
git reset --hard  HEAD~1    # 撤销最近 1 次提交，并丢弃改动（危险！不可恢复）
```

> `HEAD~1` 表示「当前提交的前一个」。`--hard` 会直接删除改动，使用前请再三确认。已经 push 的提交不要用 `reset` 改写历史；改用 `git revert <提交ID>`，它会生成一个「反向提交」来抵消，更安全。

---

## 6. 用 Claude Code 辅助 Git

Claude Code（下文简称 CC）可以大幅降低 Git 的上手难度，但它的行为是「克制」的：

- **CC 默认不会替你自动提交或推送。** 涉及 `commit`、`push` 等会改变历史或外发的操作，CC 会在你明确确认后才执行。
- 你可以让 CC：
  - **解释 diff**：「帮我看看这次改动做了什么、有没有风险。」
  - **生成 commit message**：「根据当前暂存的改动，按 Conventional Commits 写一条提交信息。」
  - **梳理冲突解决思路**：合并冲突时，让 CC 解释冲突双方的含义、给出合并建议。
- **审阅后再执行**：CC 生成的命令和信息，请你过目确认无误后再运行。它是助手，不是替你做决定的人。

更多与编辑器/IDE 的协作方式见 [08-IDE集成指南.md](08-IDE集成指南.md)；遇到报错时的排查思路见 [09-最佳实践与排错.md](09-最佳实践与排错.md)。

---

## 下一步

掌握了 Git 基础后，就可以进入真正的开源协作了：

- [05-开源项目开发指南.md](05-开源项目开发指南.md)：如何搭建本地开发环境。
- [07-开源贡献与PR指南.md](07-开源贡献与PR指南.md)：如何 Fork、提交 Pull Request、回应 Code Review。

> 返回 [📖 文档索引](README.md)
