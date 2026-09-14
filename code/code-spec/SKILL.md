---
name: code-spec
description: 将 product PRD、issue、code proposal 或当前讨论整理为 ReadyForDesign 的 technical spec。用于明确 behavior contract、技术约束、data/API change 和 verification requirement；不用于需求挖掘、implementation design 或直接实现代码。
---

# Code Spec

将上游 behavior contract 整理成由 `code-design` 消费的 technical spec。Spec 负责精确表达“代码必须满足什么以及受哪些约束”，不得重新定义产品目标，也不提前完成 implementation design。

## 输入与职责边界

优先使用当前最可靠的输入：

1. 用户明确指定的要求和约束
2. 上游 `product-prd` 及其 acceptance criteria
3. `code-proposal` 的 route decision
4. issue、ticket 或当前 conversation

`product-prd` 决定 why、who、what 和验收目标；`code-spec` 固定 behavior contract、technical constraint、data/API change 和 verification requirement；`code-design` 再决定具体 module、interface、seam 与 ticket sequencing。输入之间发生冲突时，明确记录冲突，不自行改变产品行为或掩盖技术限制。

优先综合已有上下文，不重新进行开放式访谈。只有缺失信息会改变 public behavior、关键 architecture 或不可逆 data change，且无法从现有证据安全推断时，才提出阻塞性问题。

## 1. 理解现状

检查与需求相关的代码、测试、配置和文档。存在时读取 `AGENTS.md`、`CONTEXT.md`、相关 ADR 和仓库规范，并使用项目既有 domain language。

确认：

- 当前行为与目标行为的差异
- 将被修改的 module 及其现有 interface
- 可复用的 implementation pattern 和相似测试
- external dependency、兼容性与 migration 约束

完成本步骤时，应能解释变更落在何处、为什么落在那里，以及哪些既有行为必须保持不变。

## 2. 固定技术决策

将产品要求转化为 design 必须满足的 technical decisions 和 constraints，包括：

- 需要新增、修改或移除的 module
- module interface 及其 invariant、error mode 和调用约束
- data model、schema、API contract 或 event contract 的变化
- state transition、failure path 和 recovery behavior
- backward compatibility、migration、rollout 和 observability 要求

识别可能复用的 module 和 seam，但把具体目标结构交给 `code-design`。存在多个 architecture 方案时，记录每个方案必须满足的约束和仍待解决的 design question，不把未决定的方案写成既定实现。

使用稳定的 module 和 interface 名称。只有 file path 能实质性消除歧义时才写入已有路径；避免 line number 和大段 code snippet。prototype 中的 state machine、schema 或 type shape 比文字更精确时，可以保留最小的 decision-rich snippet，并注明来源。

## 3. 建立 traceability

为每条 in-scope requirement 建立完整映射：

```text
Requirement → Technical decision → Test seam → Expected observation
```

记录能够观察用户或 caller 行为的最高稳定 public interface，以及可能复用的 testing seam。最终 seam 由 `code-design` 固定；只有候选 seam 会改变 public behavior 或测试范围时，才请求用户确认。

测试决策应说明：

- 哪些行为需要 unit、integration 或 end-to-end coverage
- 每类行为通过哪个 seam 验证
- 可参考的既有测试
- 哪些 external system boundary 需要 mock 或 fake
- migration、failure path 和 regression 如何验证

## 4. 编写并保存 spec

读取 [references/spec-template.md](references/spec-template.md)，使用其中结构生成 spec。遵循用户指定或仓库已有的 spec 存放约定；没有明确位置时，在回复中提供完整 spec，并说明尚未持久化。发布到 issue tracker、创建 ticket 或修改外部系统前，需要用户明确要求。

Spec 应区分：

- 已确认的 decision
- 有证据支持的 assumption
- 尚未解决的 open question

不要把实现步骤写成逐行编码指令。描述足以约束实现和 review 的行为、seam 与 decision，同时给执行者保留局部实现空间。

## Handoff

Spec 达到 `ReadyForDesign` 后交给 `code-design`，由其确定 module/interface/seam、拆分 tracer-bullet tickets 并建立 blocking graph。Design 完成后再根据 route decision 进入 `code-tdd` 或 `code-implement`，最终由 `code-review` 将 diff 同时对照本 spec 和仓库 Standards。

后续 design 或 implementation 若发现 spec 冲突、缺失或不可行，应更新 spec 状态并返回 `code-proposal` 或上游 `product-prd`，而不是静默偏离。

## 完成标准

当所有 in-scope requirement 都映射到明确的 technical constraint、expected observation 和 verification requirement，data/API 变化已说明，关键 failure、migration 与 compatibility 风险已覆盖，out-of-scope 清晰，且剩余 open question 不阻塞 design 时，spec 才能标记为 `ReadyForDesign`。
