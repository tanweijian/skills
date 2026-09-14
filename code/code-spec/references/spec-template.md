# Code spec template

## Metadata

- **Status**：`Draft` 或 `ReadyForDesign`
- **Source**：关联的 product PRD、issue、conversation 或其他 behavior contract
- **Version**：用于识别 implementation 期间的 requirement change

## Problem Statement

简要说明用户或系统当前遇到的问题，以及本次技术变更需要实现的目标。引用上游产品目标，不重新定义产品策略。

## Scope

### In scope

列出本次必须交付的 behavior 和 acceptance criteria。

### Out of scope

列出明确不包含的行为、平台、数据或后续工作。

## Current State

说明相关 module、interface、data flow、external dependency 和测试现状。只记录会影响本次 decision 的事实。

## Technical Constraints and Decisions

### Modules

说明已知会受影响的 module，以及 implementation design 必须满足的职责约束。目标结构由 `code-design` 确定。

### Interfaces and seams

说明 public behavior、invariant、调用约束和 error mode，并记录可用于观察行为的 seam candidate。最终 interface 与 seam 由 `code-design` 确定。

### Data and schema

说明 data model、schema、persistence、migration 和 backward compatibility 变化。无变化时明确写 `No change`。

### API and integrations

说明 API contract、event、external system boundary、timeout、retry 和 failure behavior。无变化时明确写 `No change`。

### State and error handling

说明关键 state transition、invalid state、partial failure、recovery 和 user-visible error。

### Non-functional constraints

记录与本次工作相关的 performance、security、privacy、reliability、accessibility 或 compatibility 要求。

## Requirement Traceability

对每条 requirement 建立映射：

| Requirement | Technical constraint | Verification requirement | Expected observation |
| --- | --- | --- | --- |
| R1 |  |  |  |

## Testing Decisions

说明需要的 unit、integration 和 end-to-end coverage，既有 prior art，以及必须 mock 或 fake 的 system boundary。测试聚焦 external behavior，不绑定内部 implementation detail。

## Migration, Rollout and Observability

说明 migration 顺序、feature flag、rollback、logging、metric 和 alert。均不需要时明确写 `Not required`。

## Open Questions and Assumptions

分别列出 open question 与 assumption，并标明是否阻塞 design。`ReadyForDesign` 不得包含未处理的 blocking question。

## Design Handoff

向 `code-design` 提供 source requirement、technical constraint、seam candidate、verification requirement 和仍需解决的 design question。不在此处提前拆分 ticket。
