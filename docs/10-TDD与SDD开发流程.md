> 返回 [📖 文档索引](README.md)

# 10 - TDD 与 SDD 开发流程及最佳实践

本篇介绍两套**业界主流**的开发方法，以及如何用 Claude Code / OpenCode 把它们落地：

- **TDD（Test-Driven Development，测试驱动开发）**：先写测试、再写实现的「微循环」开发法。源自 Kent Beck 的经典著作 *Test-Driven Development: By Example*（2002）。
- **SDD（Spec-Driven Development，规格驱动开发）**：先写「规格说明（spec）」、再让 AI 代理据此规划与实现的方法。当前业界主流落地工具是 GitHub 开源的 **Spec Kit**。

> 一句话关系：**SDD 管「先想清楚做什么」（外层），TDD 管「每一步代码怎么写对」（内层）**。两者互补，常常组合使用——SDD 把需求拆成任务，每个任务再用 TDD 实现。

本篇所有方法均为公开的、可考证的业界实践，文末附**权威来源链接**。

---

# 第一部分：TDD（测试驱动开发）

## 一、TDD 的核心：红-绿-重构（Red-Green-Refactor）

TDD 的标准循环只有三步，不断重复（来源：Kent Beck *TDD by Example*）：

| 阶段 | 做什么 | 通俗理解 |
| --- | --- | --- |
| 🔴 **Red（红）** | 写一个**会失败**的小测试（此时还没有实现，甚至编译不过） | 先定义「我要什么」 |
| 🟢 **Green（绿）** | 写**刚好能让测试通过**的最少实现代码（先求能跑，不求优雅） | 用最快方式满足它 |
| 🔧 **Refactor（重构）** | 在**不改变行为**的前提下整理代码、消除重复 | 让它变干净 |

配套的还有 Robert C. Martin（Uncle Bob）总结的 **TDD 三定律**，把上面的循环约束得更细：

1. 在写出一个**失败的**单元测试之前，不准写任何产品代码。
2. 只允许写「刚好导致失败」的测试（无法编译也算失败），不准写更多。
3. 只允许写「刚好让当前失败测试通过」的产品代码，不准写更多。

> 这两条「红绿重构 + 三定律」是 TDD 最被广泛引用的定义，几乎所有团队的 TDD 实践都建立在它之上。

## 二、为什么 TDD 特别适合 Claude Code / OpenCode

AI 代理在**有明确、可自动验证的目标**时表现最好。测试就是这样一个目标：

- 每一轮「红→绿」都给 AI **无歧义的反馈**——测试通过/失败是确定的信号。
- AI 可以自己「改代码→跑测试→看结果→再改」反复迭代，**无需人盯着**。
- Anthropic 官方把 TDD 称为「与智能体编码工具协作时最强的单一模式（the single strongest pattern）」。

> ⚠️ 一个重要前提：**AI 默认会先写实现、再补测试**（与 TDD 相反）。所以你必须**明确告诉它在做 TDD**，否则它不会先写测试。

## 三、Claude Code 官方推荐的 TDD 工作流

以下步骤来自 [Anthropic 官方《Claude Code Best Practices》](https://www.anthropic.com/engineering/claude-code-best-practices)，是目前最权威的 AI + TDD 流程：

1. **让 Claude 先写测试**：根据「输入 → 期望输出」写测试。**明确说明你在做 TDD**，让它**不要写任何实现、也不要写桩/假实现（mock）**。
2. **运行测试，确认全部失败**：让 Claude 跑测试并确认是红的；再次强调此时**不要写实现代码**。
3. **提交这些失败的测试**（commit）：作为一个检查点（checkpoint）。
4. **让 Claude 写实现让测试变绿**：要求它**不准修改测试**，反复「写代码→跑测试→调整」，直到全部通过。
5. **满意后提交实现代码**。

**为什么第 3 步「先提交测试」很关键**：AI 有时会偷懒——**改测试去迁就实现**，而不是改实现。先把测试提交，就有了安全网，事后 `git diff` 一眼能看出测试是否被偷偷改过。还可以让一个**子代理（subagent）独立审查**实现是否只是「过拟合」了测试。

### 一个可直接用的提示词模板

```text
我们用 TDD 开发「<功能描述>」。请严格按下面顺序，分步进行，每步等我确认：

第 1 步：只写测试，覆盖这些输入→输出：<列出用例>。
  - 这是 TDD，不要写任何实现代码，也不要写 mock/桩实现。
第 2 步：运行测试，确认它们全部失败（红）。先不要写实现。
（我确认后）第 3 步：写最少的实现让测试通过；过程中不要修改测试文件，
  反复跑测试直到全绿，再做必要的重构。
```

> 在 OpenCode 中流程完全一样，把上面的提示词发给它即可——TDD 是方法论，不依赖具体工具。

## 四、各主流语言的测试配置（让 AI 知道「怎么跑测试」）

**关键前提**：把「测试怎么跑」写进项目根目录的 `CLAUDE.md`（见 [03-配置文件详解.md](03-配置文件详解.md)），AI 才能自动运行测试、读取红/绿结果。

### Python — pytest（最主流）

```bash
# 终端
pip install pytest
# 约定：测试文件名 test_*.py 或 *_test.py，测试函数以 test_ 开头
pytest -q            # 安静模式跑全部测试
pytest -k 加法 -x    # 只跑名字含「加法」的，遇到第一个失败即停
```

`test_calculator.py` 示例（红阶段先写它，此时 `calculator.py` 还没实现）：

```python
from calculator import add

def test_add_two_positives():
    assert add(2, 3) == 5

def test_add_negative():
    assert add(-1, 1) == 0
```

`CLAUDE.md` 里写一行：`运行测试：pytest -q`。

### Java — JUnit 5（业界标准）

```bash
# 终端
mvn test            # Maven 项目
./gradlew test      # Gradle 项目
```

JUnit 5 测试示例：

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {
    @Test
    void addTwoPositives() {
        assertEquals(5, new Calculator().add(2, 3));
    }
}
```

常配套 **AssertJ**（更流畅的断言）与 **Mockito**（mock 依赖）。`CLAUDE.md`：`运行测试：mvn -q test`。

### JavaScript / TypeScript — Jest 或 Vitest

```bash
# 终端
npm test            # 通常映射到 jest 或 vitest
npx vitest run      # 跑一次（非 watch 模式）
```

```javascript
import { add } from './calculator';
test('adds two positives', () => {
  expect(add(2, 3)).toBe(5);
});
```

### Go — 内置 testing

```bash
# 终端
go test ./...       # 跑当前模块全部测试
```

```go
func TestAdd(t *testing.T) {
    if got := Add(2, 3); got != 5 {
        t.Errorf("Add(2,3) = %d; want 5", got)
    }
}
```

> 不论哪种语言，思路一致：**先有失败的测试 → 让 AI 写实现到全绿 → 重构**。AI 负责重复劳动，你负责审阅测试是否表达了真实需求。

## 五、TDD 最佳实践清单

- **测试要小、要快、要独立**：一个测试只验证一件事；整套测试秒级跑完，AI 才能高频迭代。
- **先提交测试再写实现**：给 AI「不许改测试」立规矩，并留 `git diff` 安全网。
- **盯紧「AI 改测试骗过」**：每轮检查测试文件是否被改动；必要时让子代理独立复核实现。
- **从「输入→输出」用例出发**：把需求写成具体例子交给 AI 写测试，比抽象描述更可靠。
- **重构阶段也要跑测试**：重构后必须保持全绿，证明行为没变。
- **小步提交**：每个红-绿-重构循环结束就提交一次（见 [06-Git指南.md](06-Git指南.md)）。
- **别追求 100% 覆盖率而写无意义测试**：覆盖关键逻辑与边界条件即可。

---

# 第二部分：SDD（规格驱动开发）

## 六、什么是 SDD，为什么需要它

「先写 spec、再写代码」并不新鲜（设计文档由来已久）。**AI 时代的 SDD** 把这个思路工具化：你先用结构化的「规格说明」描述要做什么，再让 AI 代理据此**规划 → 拆任务 → 实现**，避免「凭感觉一句话让 AI 乱写」（即所谓 *vibe coding*）。

当前业界主流落地工具是 GitHub 开源的 **[Spec Kit](https://github.com/github/spec-kit)**（含一个 `specify` CLI）。它支持 **Claude Code、GitHub Copilot、Gemini、Cursor、Codex** 等多种 AI 编码代理。

> 另一个常被提到的 SDD 工具是 Amazon 的 **Kiro** IDE。本篇以开源、跨工具的 Spec Kit 为主。

## 七、Spec Kit 的四个核心阶段

SDD 的核心是**每个阶段产出一个 Markdown 工件，喂给下一个阶段**，给 AI 结构化上下文而不是零散提示。Spec Kit 用斜杠命令驱动（在 Claude Code 等代理里输入）：

| 顺序 | 命令 | 做什么 | 产出工件 |
| --- | --- | --- | --- |
| （前置） | `/constitution` | 确立项目「宪法」：不可妥协的原则与规则（如代码规范、技术约束） | `constitution.md` |
| 1 | `/specify` | 描述**做什么、为什么**（需求、用户故事），**先不谈技术栈** | `spec.md` |
| 2 | `/clarify` | 在规划前，澄清规格中模糊/缺失的地方（AI 反问你） | 更新 `spec.md` |
| 3 | `/plan` | 给出**技术栈、架构、实现方案** | `plan.md` |
| 4 | `/tasks` | 把规格与方案拆成**可执行的任务清单** | `tasks.md` |
| （校验） | `/analyze` | 跨工件一致性分析（spec/plan/tasks 是否自洽） | 分析报告 |
| 5 | `/implement` | 让 AI 代理按任务清单逐项实现 | 代码 |

> 「宪法（constitution）」是 Spec Kit 的特色：一份**长期有效的规则文件**，后续每个命令都会参考它，确保 AI 始终遵守项目底线。

## 八、上手 Spec Kit（安装与流程）

Spec Kit 用 Python 的 `uv` 工具运行 `specify` CLI（⚠️ 命令以[官方仓库](https://github.com/github/spec-kit) README 为准）：

```bash
# 终端：在新项目里初始化（需先安装 uv）
uvx --from git+https://github.com/github/spec-kit.git specify init <项目名>
# 选择你的 AI 代理（如 Claude Code），它会生成 .specify/ 模板与斜杠命令
```

随后在 Claude Code（或 OpenCode）里，按 `/constitution → /specify → /clarify → /plan → /tasks → /analyze → /implement` 的顺序逐步推进，每步审阅生成的 Markdown 工件后再进入下一步。

> OpenCode 用户提示：若 Spec Kit 暂未内置某代理的适配，你也可以**手动套用同一套工件**——在仓库里建 `spec.md` / `plan.md` / `tasks.md`，把它们作为上下文交给 OpenCode，并用其[自定义命令](https://opencode.ai/docs)固化每个阶段。方法论不绑定工具。⚠️ 官方支持的代理列表请以 Spec Kit README 为准。

## 九、SDD 最佳实践清单

- **规格只讲「什么」与「为什么」，把「怎么做」留给 `/plan`**：`/specify` 阶段不要陷入技术细节。
- **善用 `/clarify` 先消除歧义**：让 AI 在动手前把模糊点问清楚，比事后返工便宜得多。
- **先立「宪法」**：把团队不可妥协的规范（语言、风格、安全、测试要求）写进 `constitution.md`，让所有阶段都遵守。
- **逐阶段人工审阅工件**：spec / plan / tasks 都是 Markdown，提交前像 review 代码一样 review 它们（见 [07-开源贡献与PR指南.md](07-开源贡献与PR指南.md)）。
- **把工件纳入版本管理**：`spec.md`、`plan.md`、`tasks.md` 一并提交，作为「需求的真相来源」。
- **任务粒度要小**：`/tasks` 拆出的任务越小越可验证，越适合交给 AI 一项项做。

---

# 第三部分：把 TDD 装进 SDD（推荐组合）

两者天然互补，推荐的组合流程：

1. **SDD 定方向**：`/specify → /clarify → /plan → /tasks` 把需求变成一份**任务清单**。
2. **TDD 实现每个任务**：在 `/implement` 阶段，对每个任务执行红-绿-重构——先让 AI 写失败测试、提交、再写实现到全绿。
3. **持续校验**：每完成一个任务就提交（小步提交），用 `/code-review`、`verify` 自查（见 [04-常用Skill与扩展.md](04-常用Skill与扩展.md)）。

这样既保证「做对的事」（SDD），又保证「把事做对」（TDD），是当前 AI 辅助开发较为稳健的主流范式。

---

## 权威来源

- TDD 经典定义：Kent Beck, *Test-Driven Development: By Example*（Addison-Wesley, 2002）；Martin Fowler, [bliki: Test Driven Development](https://martinfowler.com/bliki/TestDrivenDevelopment.html)。
- TDD 三定律：Robert C. Martin（Uncle Bob），[The Cycles of TDD](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html)。
- AI + TDD 工作流：[Anthropic《Claude Code Best Practices》](https://www.anthropic.com/engineering/claude-code-best-practices)。
- SDD 工具与流程：[GitHub Spec Kit 仓库](https://github.com/github/spec-kit) 与 [GitHub 官方博客：Spec-driven development with AI](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)。

> ⚠️ 工具命令、阶段名称可能随版本更新，请以上述官方来源的最新文档为准。
