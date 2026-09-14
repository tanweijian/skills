# Code Skills

`code/` 是代码工作流的分类目录，不是可调用技能。目录中的每个 `code-xxx` 都是独立技能；`code-proposal` 是所有 code request 的统一路由入口。

## Workflow

产品需求通过 `product-prd` 进入代码流程：

```text
product-prd
    ↓
code-proposal
    ├─ 简单、明确、低风险
    │    → code-tdd 或 code-implement
    │    → code-review
    │
    ├─ 复杂、跨 module 或存在设计风险
    │    → code-spec
    │    → code-design
    │    → code-tdd 或 code-implement
    │    → code-review
    │
    ├─ Review request
    │    → code-review
    │
    └─ Behavior-preserving refactor
         → code-refactor
         → code-review
```

复杂流程固定为 `spec → design → implementation → review`：

- `code-spec` 固定 behavior contract、technical constraint 和 verification requirement，产出 `ReadyForDesign`。
- `code-design` 确定 module、interface、seam、vertical slice 和 blocking graph，产出 `ReadyForImplementation`。
- `code-tdd` 或 `code-implement` 完成实现和 verification。
- `code-review` 分别从 Standards 与 Spec 两个 axis 检查最终 diff。

## Routing rules

所有未明确指定子技能的 code request 先由 [`code-proposal`](code-proposal/SKILL.md) 判断 route。用户显式调用子技能时仍可独立使用；若其前置条件不成立，应返回 `code-proposal` 重新路由。

只有以下条件全部成立时才允许直接 implementation：

- behavior 与 acceptance criteria 明确
- 影响范围局部且验证路径清晰
- 不改变 public interface、API contract 或跨 module protocol
- 不涉及复杂 schema migration 或不可逆 data change
- 不引入新的 security、privacy 或高风险 reliability decision
- 不存在需要比较的 architecture 方案
- 可以在一个小型 vertical slice 中完成

任一条件不成立时，进入 `code-spec → code-design`。

## Skills

| Skill | Responsibility | Primary output |
| --- | --- | --- |
| [`code-proposal`](code-proposal/SKILL.md) | 分类所有 code request，执行 complexity gate 并选择后续流程 | Route decision |
| [`code-spec`](code-spec/SKILL.md) | 将 product PRD、issue 或用户要求转为 technical contract | `ReadyForDesign` code spec |
| [`code-design`](code-design/SKILL.md) | 设计 module/interface/seam，并拆分 tracer-bullet tickets | `ReadyForImplementation` design 与 ticket graph |
| [`code-implement`](code-implement/SKILL.md) | 根据明确 contract 完成普通 implementation 和持续 verification | Verified implementation |
| [`code-tdd`](code-tdd/SKILL.md) | 使用 test-first 和 red-green loop 驱动 implementation | Green implementation 与 behavior tests |
| [`code-review`](code-review/SKILL.md) | 独立执行 Standards 与 Spec review | Prioritized findings |
| [`code-refactor`](code-refactor/SKILL.md) | 在保持 observable behavior 不变的条件下改善 architecture | Behavior-preserving refactor |

## Handoff artifacts

技能之间通过明确 artifact 交接，不依赖隐含 conversation state：

```text
Product PRD
  → Route decision
  → Code spec
  → Implementation design / Ticket graph
  → Implementation diff / Verification evidence
  → Review findings
```

每次 handoff 至少携带：

- source requirement 与 acceptance criteria
- 当前 scope、out-of-scope 和 blocker
- 已确认的 decision、assumption 与 open question
- 下游需要遵守的 constraint
- 已完成及尚未完成的 verification

发生 product behavior、scope 或 acceptance criteria 变化时返回 `product-prd`；发生 route、spec 或 design 层级变化时返回 `code-proposal`，只重新评估受影响部分。

## Invariants

- Product 决定 why、who、what；Code 决定如何安全实现和验证。
- `code-spec` 先于 `code-design`，复杂 implementation 不跳过两者。
- TDD 请求使用 `code-tdd`；普通 implementation 使用 `code-implement`。
- Refactor 保持 observable behavior；改变 behavior 的工作重新进入正常 feature 流程。
- 所有代码修改最终进入 `code-review`。
- 外部发布、commit、push 和 PR creation 只在用户明确要求时执行。
