---
name: code-review
description: 从固定基点或 working tree 审查代码变更，分别评估 Standards 与 Spec，并输出可定位、可执行的 findings。用于 review branch、PR、WIP changes 或“review since X”；不用于直接修复 findings。
---

# Code Review

对同一份 diff 进行两个相互独立的 review axis：

- **Standards**：变更是否符合仓库规范，并保持合理的 code quality。
- **Spec**：变更是否忠实实现 product PRD、code spec、issue 或用户要求。

两个轴分别形成 conclusions，避免一个轴的通过掩盖另一个轴的问题。Review 默认是 read-only；用户明确要求修复后，再进入实现或 refactoring。

## 1. 固定 review scope

优先使用用户指定的 commit、branch、tag、merge-base 或 PR base。

- Review branch 或“since X”时，验证 fixed point 可解析，并使用 merge-base comparison，例如 `git diff <fixed-point>...HEAD`。
- Review WIP changes 时，覆盖 unstaged、staged 和 untracked files。
- 用户未指定 branch base 时，优先从 upstream 或 default branch 推断并说明假设；存在多个会显著改变 diff 的候选项时，再请求用户确认。

记录实际使用的 diff scope 和 commit list。若 fixed point 无效或 diff 为空，在进入正式 review 前说明，不对不存在的变更生成 findings。

## 2. 收集 review contract

### Spec sources

按相关性寻找并读取：

1. 用户明确提供的 spec 或 acceptance criteria
2. 与变更对应的 `code-spec`
3. 上游 `product-prd`
4. issue、ticket 或任务描述
5. branch、commit message 以及仓库中的相关设计文档

`product-prd` 决定产品行为和验收目标，`code-spec` 决定在该范围内的技术实现约束。两者冲突时，将冲突本身作为 finding，不自行选择一方改写需求。找不到任何 spec 时仍执行 Standards axis，并在 Spec 结果中明确标记 `No spec available`。

### Standards sources

读取作用域内的 `AGENTS.md`、`CONTRIBUTING.md`、coding standards、`CONTEXT.md`、ADR 和其他仓库约定。仓库明确记录的规则优先于通用判断。

此外，使用 [references/standards-baseline.md](references/standards-baseline.md) 作为 code smell baseline。Baseline 只提供 heuristic，不构成 hard violation；工具已经稳定检查的格式问题不重复报告。

## 3. 独立执行两个 review axis

环境支持 independent subagent 时，并行执行两个 axis；否则进行两次独立 review pass，并在完成各自 conclusions 前避免用另一轴的判断替代它。

### Standards axis

检查每个相关 file 和 hunk：

- 违反仓库已记录规范的地方，并引用规则来源
- baseline 中出现的 code smell，并标记为 judgement call
- 由变更引入且能够具体说明影响路径的 maintainability risk

### Spec axis

对照每条相关 requirement 和 acceptance criteria，检查：

- 缺失或只实现了一部分的要求
- 实现结果与要求不一致的行为
- 未被要求的行为或 scope creep
- 表面已实现，但在具体输入、状态或错误路径下不成立的要求

## 4. Findings 标准

只报告作者在知道后很可能会修复、且由本次变更引入或暴露的问题。每个 finding 必须包含：

- `P0` 至 `P3` priority
- 精确到最小必要范围的 file 和 line
- 能触发问题的条件或证据
- 对用户、系统或后续维护的影响
- 简洁、可执行的修正方向

Priority 含义：

- **P0**：会造成广泛中断、数据损坏或严重安全后果，必须立即阻断。
- **P1**：常见路径上的明确错误或 release blocker，应优先修复。
- **P2**：在特定条件下发生的真实缺陷或显著维护风险。
- **P3**：低风险但值得处理的问题，不影响当前主要交付。

避免把个人偏好、纯粹的 style nit、没有实际触发路径的猜测或旧代码问题写成 finding。

## 5. 输出

先给 findings，再给简短结论。保持两个 axis 分离：

```markdown
## Standards

### [P2] Finding title
位置、证据、影响和修正方向。

## Spec

### [P1] Finding title
位置、spec 依据、影响和修正方向。

## Summary

Standards: N findings；Spec: N findings；未覆盖的 review scope 或验证限制。
```

某个 axis 没有 finding 时明确写 `No findings`。不要将两个 axis 合并排序，也不要用一个总体 pass/fail 掩盖其中任一结果。

## 完成标准

Review 在以下条件全部满足时完成：实际 diff scope 已明确，每个 changed file 都已纳入检查，Standards 与 Spec sources 已识别或明确缺失，两个 axis 均已独立完成，所有 findings 都有可定位证据，并说明未运行的验证或剩余不确定性。
