---
name: code-proposal
description: 所有 code request 的统一路由入口。用于接收 feature、bug、test、refactor、review、spec 或 design 请求，判断应进入 code-spec → code-design 的完整流程，还是可以直接交给 code-implement、code-tdd、code-refactor 或 code-review。
---

# Code Proposal

先判断工作性质和风险，再把请求交给最合适的 code skill。该技能只负责 route decision、必要的上下文整理和 handoff，不替代下游技能执行实际工作。

## 入口原则

所有未明确指定子技能的 code request 先经过本技能。用户显式调用某个 code skill 时仍尊重该选择；若发现输入不满足该技能的前置条件，说明原因并返回本路由重新判断。

优先使用用户请求、上游 `product-prd`、issue、已有 code spec/design 和当前 codebase 作为 contract。只读取足以做出 route decision 的上下文，不在路由阶段提前完成完整设计或实现。

## 1. 分类请求

先识别 request type：

- **Review**：审查 PR、branch、diff 或 WIP changes
- **Refactor**：保持 observable behavior 不变的结构调整或 architecture scan
- **Specification**：整理实现约束、acceptance criteria 和 technical contract
- **Design**：设计 module/interface/seam，拆分 tracer-bullet tickets 和 blocking graph
- **Implementation**：新增或修改 behavior、修复 bug、补充测试或完成 ticket

需求本身仍不清楚、需要确认用户问题或产品行为时，退出 code workflow，返回 `product-digging` 或 `product-prd`。Code skill 不替产品流程补写产品决策。

## 2. 专用请求直接路由

- Review 请求直接进入 `code-review`。
- 明确保持 behavior 不变的 refactor 进入 `code-refactor`；若实际需要改变 public contract，则重新路由到完整流程。
- 用户只要求编写 code spec 时进入 `code-spec`，完成于 `ReadyForDesign`。
- 用户只要求 implementation design 且已有 `ReadyForDesign` spec 时进入 `code-design`。
- 用户明确要求 TDD、test-first 或先写 regression test 时，在通过 complexity gate 后选择 `code-tdd` 作为 implementation skill。

## 3. Complexity gate

只有以下条件全部成立时，implementation request 才能直接执行：

- 目标 behavior 与 acceptance criteria 明确
- 变更范围局部，相关 module 和验证路径容易确定
- 不新增或实质性修改 public interface、API contract 或跨 module protocol
- 不涉及 schema migration、不可逆 data change 或复杂 compatibility
- 不涉及新的 authorization、安全、隐私或高风险 reliability decision
- 不存在需要比较的 architecture 方案或不确定 dependency direction
- 失败影响和 rollback 简单明确
- 能在一个小的 vertical slice 内完成并验证

全部通过时：

```text
code-proposal
  → code-tdd（要求 test-first）或 code-implement
  → code-review
```

任一条件不成立、证据不足或影响范围无法快速确定时：

```text
code-proposal
  → code-spec
  → code-design
  → code-tdd 或 code-implement
  → code-review
```

不要为了节省流程把不确定性解释成简单。也不要让低风险、局部且契约明确的修改承担不必要的 spec/design 成本。

## 4. 输出 route decision

执行下游技能前，给出紧凑的 route decision：

```markdown
**Request type:** Implementation
**Route:** code-spec → code-design → code-tdd → code-review
**Reason:** 涉及新的 public interface 和 schema migration
**Contract:** 使用的 product PRD、issue 或用户要求
**Assumptions:** 当前用于路由的非阻塞假设
**Next input:** 交给下一个技能的目标、约束和证据
```

路由清晰且风险低时直接说明并继续，不要求用户重复确认。只有不同 route 会造成明显成本、行为或风险差异，且现有信息无法安全判断时，才提出一个最小阻塞问题。

## 5. 重新路由

下游工作发现以下情况时返回本技能：

- spec、design 或 implementation 与 product PRD 冲突
- 原本局部的修改扩展为跨 module change
- 出现新的 schema、security、compatibility 或 migration 风险
- 用户改变范围或 acceptance criteria
- review finding 表明问题属于另一个工作阶段

保留已经确认的 contract 和证据，只重新评估受变化影响的 route，不从头重复整个流程。

## 完成标准

当 request type、选定 route、route reason、使用的 contract、关键 assumption 和下一个技能所需输入都已明确，并且没有未说明的 blocker 时，路由才算完成。
