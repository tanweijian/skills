---
name: code-design
description: 将 ReadyForDesign 的 code spec 转化为可执行的 implementation design，并拆分为带 blocking edges 的 tracer-bullet tickets。用于确定 module/interface/seam、vertical slice 和依赖编排；不用于编写上游 spec 或直接实现代码。
---

# Code Design

把 `ReadyForDesign` 的 code spec 转化为 implementation design：明确 module、interface 与 seam，并形成可以逐个交给 `code-tdd` 或 `code-implement` 的 tracer-bullet ticket graph。

## 输入与输出

优先使用：

1. `ReadyForDesign` 的 `code-spec`
2. `code-proposal` 的 route decision
3. 上游 `product-prd` 及 acceptance criteria
4. 用户明确提供的 design constraint 或 architecture decision

输出不是逐行实现方案，而是：

- module 与 interface 的目标形态
- 关键 seam 和 dependency direction
- tracer-bullet tickets
- tickets 之间的 blocking edges
- requirement coverage 与已知风险

若产品行为仍不明确，返回 `product-prd`；若 code spec 缺失、冲突或未达到 `ReadyForDesign`，返回 `code-spec`。`code-design` 在 contract 确定后安排如何安全落地。

## 1. 理解 codebase

检查相关代码、测试、配置和文档。存在时读取 `AGENTS.md`、`CONTEXT.md`、相关 ADR 与仓库规范，并沿用项目的 domain language。

确认现有：

- module 及其 interface
- behavior 被观察和测试的 seam
- dependency direction 与 external adapter
- 类似功能和测试的 prior art
- schema、API、migration 和 compatibility 约束

完成本步骤时，应能说明哪些现有结构可以复用、哪些结构会阻碍变更，以及拟议设计如何保持既有行为。

## 2. 设计 module 与 seam

优先设计 deep module：用较小的 interface 隐藏较多行为，把复杂度集中在清晰的 seam 后面。

- 优先复用已有 seam；只有实际行为需要变化位置时才新增 seam。
- Interface 应包含 caller 必须知道的 invariant、ordering constraint、error mode、configuration 和 performance characteristic，而不只是 type signature。
- 让 dependency 从稳定的 policy 指向可替换的 adapter。
- 测试通过与 caller 相同的 public interface 观察行为。
- 用 deletion test 检查浅层转发：删除某个 module 后，复杂度若没有重新出现在多个 caller 中，该 module 可能只是 Middle Man。

需要先调整结构才能安全实现时，创建独立的 prefactoring ticket，遵循“make the change easy, then make the easy change”。Prefactoring 必须保持现有 observable behavior。

## 3. 拆分 tracer-bullet tickets

每个 ticket 都应是一条窄而完整的 vertical slice：穿过实现该行为所需的相关层，并在完成后能够独立 demo 或 verification。

每个 ticket 必须满足：

- 交付一个 caller 或用户可观察的 behavior
- 具有可测试的 acceptance criteria
- 足以放入一个 fresh context window
- 不依赖未记录的隐含工作
- 说明使用或新增的 seam
- 只包含完成该 slice 所需的范围

避免按 schema、backend、frontend、tests 等技术层进行 horizontal slicing。某层与该行为无关时不必为了形式强行包含。

## 4. 建立 blocking graph

为每个 ticket 列出真正阻止其开始的其他 tickets。没有 blocker 的 ticket 构成当前 frontier，可以立即进入 `code-tdd` 或 `code-implement`。

检查 graph：

- blocking edges 必须表达真实前置条件，而不是偏好的执行顺序
- graph 不得出现 cycle
- blocker 应尽量少，使互不依赖的 tickets 可以并行
- 每条 product requirement 和 acceptance criterion 至少映射到一个 ticket

Wide refactor 是 vertical slicing 的例外。无法单独保持 green 的机械性广域变更，应使用 `expand → migrate → contract`：先并存新旧形式，再按 blast radius 分批迁移，最后删除旧形式。详细格式见 [references/design-template.md](references/design-template.md)。

## 5. 评审并保存 design

先向用户展示 ticket breakdown，至少包含 title、blocked by 和 what it delivers，并请用户确认 granularity、blocking edges 以及是否需要 merge 或 split。确认前标记为 `Draft`，确认后标记为 `ReadyForImplementation`。

使用 [references/design-template.md](references/design-template.md) 输出完整 design。遵循用户指定或仓库已有的存放约定；没有明确位置时，在回复中提供 design，并说明尚未持久化。

只有用户明确要求时才向 GitHub、Linear 或其他 issue tracker 发布。发布时一个 ticket 对应一个 issue，并使用平台原生 dependency relationship；平台不支持时，在 ticket 正文记录 `Blocked by`。不要修改或关闭 parent issue。

避免依赖易过期的 line number 和大段 code snippet。已有 file path 能明确 module ownership 时可以引用；prototype 中的 state machine、schema 或 type shape 比文字更精确时，可以保留最小的 decision-rich snippet。

## Handoff

按 dependency graph 的 frontier 推进：

```text
code-design ticket → code-tdd 或 code-implement → code-review
```

每次只把一个可开始的 ticket 及其 source requirement、acceptance criteria、design decision、seam 和 blocker 状态交给 `code-tdd` 或 `code-implement`。

## 完成标准

当目标 module/interface/seam 已明确，所有 in-scope requirement 均被 tickets 覆盖，每个 ticket 都是可独立验证且适合单个 context 的 vertical slice，blocking graph 无 cycle 且 frontier 可识别，wide refactor 已采用安全 sequencing，并且用户已确认 breakdown 时，design 才能标记为 `ReadyForImplementation`。
