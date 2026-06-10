# subagent-plan-decomposer

把已有方案改写成适合「主 agent + 子 agent」协作执行的委派方案。

这个 skill 不直接执行原方案，也不直接启动子 agent。它的职责是把一个已有方案转换成 `subagent-ready` 结构，让主 agent 明确知道哪些步骤应自己保留、哪些步骤应委派、哪些任务可以并行、在哪里汇合、如何合并结果、如何做最终验收。

## 适用场景

适合在以下情况下使用：

- 已有一份执行计划、研究计划、产品方案、运营方案、代码实现方案或 Codex 分步计划。
- 你希望把这个方案改写成多个子 agent 可执行的工作包。
- 方案中存在大量上下文阅读、重复批处理、多方向探索或独立审查需求。
- 你需要明确主 agent 的控制权、子 agent 的边界、并行关系和 join gate。
- 你不想让一个 agent 独自消化全部上下文，导致上下文污染、判断漂移或结果难以合并。

不适合在以下情况下使用：

- 你只是要直接执行一个很小的任务。
- 原始目标还不清楚，需要先澄清需求。
- 任务高度耦合，拆分后合并成本高于收益。
- 你需要的是完整开发工作流框架，而不是方案委派拆解。
- 你希望 skill 自动调用子 agent 并完成执行闭环。

## 核心能力

- 标准化原方案，把大步骤拆成原子步骤。
- 判断每一步是否应由主 agent 保留、必须委派、可选委派或拆成多个子 agent。
- 标注并行组、依赖关系、上下文隔离任务、审查 agent 和 join gate。
- 为每个子 agent 生成任务简报，包括目标、输入范围、禁止事项、输出格式、验收标准、依赖项和压缩等级。
- 定义主 agent 如何等待、合并、解决冲突并做最终验收。

## 关键标记

| 标记 | 含义 |
| --- | --- |
| `[[MAIN_AGENT_ONLY]]` | 必须由主 agent 完成，通常涉及目标、约束、关键决策、冲突合并或最终输出。 |
| `[[SUBAGENT_REQUIRED]]` | 必须委派给子 agent。 |
| `[[SUBAGENT_OPTIONAL]]` | 可以委派，但主 agent 也可直接处理。 |
| `[[SPLIT_TO_SUBAGENTS]]` | 一个步骤应拆成多个独立子任务。 |
| `[[PARALLEL_GROUP: <id>]]` | 同组任务可并行执行。 |
| `[[JOIN_GATE]]` | 主 agent 必须等待多个输出返回后再继续。 |
| `[[DEPENDENCY: <id>]]` | 当前步骤依赖某个前置输出或汇合点。 |
| `[[CONTEXT_DELEGATE]]` | 委派的主要原因是上下文隔离。 |
| `[[REVIEW_AGENT]]` | 需要独立审查、风险检查或 evaluator agent。 |

## 输出结构

skill 的最终输出固定包含 6 个部分：

1. `拆解总览`
2. `依赖与并行关系图`
3. `改写后的 Subagent-Ready 方案`
4. `子 Agent 任务简报`
5. `汇合 / 合并协议`
6. `主 Agent 检查清单`

这个结构的重点是让主 agent 可以直接进入下一步执行或调度，而不是只得到抽象建议。

## 使用方式

在 Codex 中直接引用 skill：

```text
$subagent-plan-decomposer

请把下面这份方案改写成主 agent + 子 agent 的委派执行方案：

<粘贴你的原始方案/原始方案文件路径>
```

也可以自然语言触发：

```text
把这个执行计划拆成主 agent 和多个子 agent 可以并行执行的版本，要求保留主 agent 的最终决策权。
```

如果你希望后续真正执行子 agent，需要在后续提示中明确要求 Codex spawn agents，例如：

```text
根据上面的 Subagent-Ready 方案，实际启动对应子 agent，等待所有 join gate 完成后汇总。
```

## 安装位置示例


- Windows：`C:\codex\skills\subagent-plan-decomposer\SKILL.md`
- Linux：`/home/user/.codex/skills/subagent-plan-decomposer/SKILL.md`

