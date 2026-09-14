---
name: code-implement
description: 根据 code spec、ticket 或明确的用户要求实现代码变更，并持续验证 acceptance criteria。用于普通 implementation、feature 开发和已明确方案的 bug fix；用户要求 test-first、TDD 或先写 regression test 时使用 code-tdd。
---

# Code Implement

实现一个边界明确、可独立验证的工作项，并保持变更忠实于 behavior contract。优先消费 `ReadyForImplementation` 的 `code-spec`；小型且无歧义的任务可以直接以 ticket 或用户要求作为 contract。

## 输入与路由

开始前确认当前工作项的：

- 目标 behavior 与 acceptance criteria
- in-scope 和 out-of-scope
- 已确认的 module、interface、data/API change 与 testing seam
- blocker、dependency、migration 和 compatibility 要求

输入缺少会改变产品行为的信息时返回 `product-prd`；技术方向或 ticket 划分未确定时返回 `code-design`；implementation detail 尚不足以安全执行时返回 `code-spec`。

用户明确要求 TDD、test-first，或要求 bug fix 前先建立 regression test 时，使用 `code-tdd`。`code-implement` 仍负责补充和运行测试，但不强制 red-before-green。

## 1. 建立工作基线

检查相关代码、测试、配置和文档。存在时读取 `AGENTS.md`、`CONTEXT.md`、相关 ADR 与仓库规范，并沿用项目的 domain language 和既有 implementation pattern。

执行 read-only 检查确认：

- 当前 branch、working tree 和已有 diff
- 目标行为当前如何实现和测试
- 用户已有或未提交的改动是否与本任务重叠
- 项目可用的 build、typecheck、lint 和 test command

保留用户和其他任务的现有改动。仅修改完成当前工作项所必需的文件；无法绕开重叠修改时，先说明冲突再继续。

## 2. 按 vertical slice 实现

一次推进一个可观察的 behavior slice。每个 slice 应包含使该行为真正工作的必要实现和验证，而不是批量修改某一技术层后再补其他层。

实现时：

- 遵循 code spec 中确定的 module、interface 和 seam
- 优先复用现有抽象、dependency 和 adapter
- 只实现当前 acceptance criteria 要求的能力
- 同步处理必要的 error path、state transition 和 compatibility behavior
- schema 或 data change 必须包含相应 migration 与 rollback/compatibility 考量
- 避免 speculative generality、无关 cleanup 和超出 contract 的行为变化

实现过程中发现 spec 不可行、互相矛盾或需要改变 public behavior 时停止该分支，记录证据，并返回 `code-spec`、`code-design` 或 `product-prd`。不要用未经确认的实现决定覆盖上游 contract。

## 3. 持续验证

验证频率与变更风险匹配：

1. 完成一个小的 logical change 后，运行最窄的相关测试或检查。
2. 修改 typed interface、schema 或跨 module contract 后，及时运行 typecheck 或 compile check。
3. 完成一个 behavior slice 后，运行该 slice 的测试及相邻 regression suite。
4. 全部实现完成后，至少运行一次完整的相关 test suite，并按项目约定运行 build、typecheck 和 lint。

测试应通过 public interface 验证 observable behavior，并覆盖本次 acceptance criteria、关键 failure path 和 regression。新增 dependency、generated artifact 或 migration 时，验证其真实集成路径，而不只验证孤立实现。

若验证失败，先确定失败是否由本次变更引入。修复范围内失败；对确认无关的既有失败保留证据并在 handoff 中说明，不顺带扩大修改范围。

## 4. 自检 diff

完成实现后检查整个 diff：

- 每处修改都能映射到当前 requirement 或必要验证
- 没有遗留 debug code、临时开关、无效注释或 accidental formatting change
- 新增 public interface、dependency、config 和 migration 均已在 spec 中体现
- documentation、example 或 generated file 在 behavior 改变时已按仓库约定同步
- 未覆盖的 acceptance criteria 和剩余风险已明确

除非用户明确要求，否则不要 commit、push、创建 PR、发布 package 或修改外部 tracker。

## Handoff

向 `code-review` 交付：

- 已实现的 behavior slice 和对应 acceptance criteria
- 关键 module/interface/data/API change
- 执行过的 command 及结果
- 未运行的验证及原因
- spec deviation、已知限制和暂缓的 refactoring
- review 使用的 fixed point 或当前 working tree scope

结构问题只有在当前 behavior 正确且 verification 通过后，才交给 `code-refactor` 处理。

## 完成标准

当所有 in-scope acceptance criteria 已实现并具有相应验证，相关 build、typecheck、lint 和 test 通过，diff 只包含完成本工作项所需的改动，不存在未说明的 spec deviation，且 handoff 信息足以让 `code-review` 独立复核时，implementation 才算完成。
