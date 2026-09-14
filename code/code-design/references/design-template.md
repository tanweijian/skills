# Implementation design template

## Metadata

- **Status**：`Draft` 或 `ReadyForImplementation`
- **Source**：关联的 code spec、product PRD、code proposal、issue 或 conversation
- **Version**：用于识别 design 变更

## Goal and Constraints

概括本次 implementation design 要实现的目标、必须保持的行为和技术约束。引用上游来源，不重新定义产品需求。

## Current State

说明相关 module、interface、seam、dependency、external adapter、测试 prior art，以及阻碍本次变更的现有结构。

## Target Design

### Modules

描述目标 module、职责与 depth。指出新增、修改、合并或移除的 module。

### Interfaces and seams

描述 caller 需要了解的 interface、invariant、error mode 和 performance characteristic，以及测试观察行为的 seam。

### Dependencies and adapters

描述 dependency direction、可替换的 adapter 和 external system boundary。

### Data, API and migration

说明 schema、API contract、event、compatibility 或 migration 对 ticket sequencing 的影响。没有变化时明确写 `No change`。

## Requirement Coverage

| Requirement | Acceptance criteria | Ticket |
| --- | --- | --- |
| R1 |  | T01 |

## Ticket Graph

列出 blocking edges，并明确当前 frontier：

```text
T01 ──→ T03
T02 ──→ T03

Frontier: T01, T02
```

## Tickets

### T01: Ticket title

**What it delivers：** 从用户或 caller 视角描述完成后可工作的 end-to-end behavior。

**Blocked by：** ticket identifier，或 `None`。

**Design decisions：** 与该 slice 直接相关的 module、interface、seam 和约束。

**Acceptance criteria：**

- [ ] 可观察且可验证的 criterion

**Implementation input：** 交给 `code-tdd` 或 `code-implement` 时必须携带的 source requirement、design decision、assumption 和 open question。

## Wide Refactor Sequencing

仅在单个 mechanical change 的 blast radius 无法由独立 vertical slice 保持 green 时使用：

1. **Expand**：在旧形式旁引入新形式，保持兼容。
2. **Migrate**：按 package、directory 或其他 blast-radius 单位分批迁移；每批独立成为 ticket，并被 Expand 阻塞。
3. **Contract**：所有迁移完成后删除旧形式；该 ticket 被全部 Migrate tickets 阻塞。

若迁移批次本身无法独立保持 green，明确使用 integration branch，并增加最终的 integrate-and-verify ticket。

## Risks and Open Questions

列出 design risk、assumption 和 open question，并标记是否阻塞 `ReadyForImplementation`。
