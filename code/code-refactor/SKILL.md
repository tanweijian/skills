---
name: code-refactor
description: 在保持 observable behavior 不变的前提下重构代码，或扫描 codebase 识别 deepening opportunities。用于处理 code-review 后的结构问题、shallow module、leaky seam、重复知识和 testability friction；不用于新增功能或改变产品行为。
---

# Code Refactor

通过收缩 interface、集中知识和加深 module，提高 leverage、locality、testability 与 AI-navigability。Refactoring 必须保持既有 observable behavior；需要改变产品行为、acceptance criteria 或 public contract 时，返回 `product-prd`、`code-design` 或 `code-spec`。

## 模式与输入

根据请求选择最窄模式：

- **Targeted refactor**：用户、`code-review` finding 或 `code-spec` 已指出具体 module、seam 或结构问题。直接围绕该目标分析和实施。
- **Architecture scan**：用户要求广泛检查或寻找改进机会。先识别 candidates，由用户选择后再修改代码。

优先使用用户指定的 scope。没有 scope 的 architecture scan 应查看足够的 git history，找出反复变化的 hot spot；只有变化分散且没有明显 hot spot 时才扩大范围。YAGNI：优先改善会继续承受变化的结构。

## 1. 建立 behavior baseline

开始前读取作用域内的 `AGENTS.md`、`CONTEXT.md`、相关 ADR、仓库规范、code spec 和测试。检查 working tree 与已有 diff，保护用户和其他任务的改动。

明确本次必须保持的：

- public interface 与 observable behavior
- acceptance criteria、invariant 与 error behavior
- data、API、event 和 persistence contract
- performance、compatibility 与 ordering constraint
- 当前通过这些 seam 验证行为的测试

先运行范围最小且相关的 baseline test。缺少能够保护目标行为的测试时，先通过现有 public interface 添加 characterization test；测试通过当前行为后，才能开始结构修改。

## 2. 诊断 architecture friction

使用以下 vocabulary：module、interface、implementation、depth、seam、adapter、leverage、locality。

寻找具有证据的 deepening opportunity：

- 理解一个 domain concept 需要在许多小 module 之间跳转
- module 的 interface 与 implementation 几乎同样复杂，形成 shallow module
- domain knowledge、validation 或 error handling 分散在多个 caller
- dependency 泄漏穿过 seam，caller 被迫了解内部细节
- 为了测试而暴露 internal function，但真实风险仍存在于调用组合中
- 一个行为需要跨多个位置修改，缺少 locality
- 当前 interface 难以通过 caller 使用的路径进行测试

对疑似 shallow module 使用 deletion test：删除它后，复杂度是消失，还是重新散落到多个 caller？只有后者说明它正在提供真实 depth。

一个 adapter 时，seam 通常只是推测；存在两个真实 adapter 时，seam 才得到变化证据。不要为了未来可能性增加 abstraction。

Architecture scan 时读取 [references/architecture-scan.md](references/architecture-scan.md)，输出 candidates、证据和推荐强度。候选超过一个且关系复杂时，可使用 before/after diagram 或 visual report；呈现完成后让用户选择，不直接实施未经选择的 candidate。

## 3. 固定 refactor contract

在修改前记录：

- **Problem**：当前 friction 的具体证据
- **Invariant**：必须保持不变的 observable behavior
- **Before**：当前 module、interface、seam 与 dependency
- **After**：目标 module、收缩后的 interface、隐藏的 implementation 与 adapter
- **Verification**：每一步使用的 test seam 和 command
- **Rollback**：失败时如何恢复到最近一次 green state

目标应当形成 deep module：较小的 interface 提供更多行为，把 caller 不需要知道的复杂度留在 implementation 内，并让测试与 caller 穿过同一个 seam。

若 After 需要改变 public behavior、引入新的产品能力或重写 acceptance criteria，这已经不是纯 refactor；先交给 `code-design` 和 `code-spec`。

## 4. 小步实施

将 refactor 拆成能够独立保持 green 的 reversible steps。每一步只做一种结构变化，例如 rename、move、extract、inline、collapse 或 dependency inversion，并立即运行最窄的相关测试。

实施时：

- 先使现有调用点继续工作，再迁移 caller
- 优先替换旧 seam，而不是在旧结构外继续叠加新层
- 将重复的 domain knowledge 集中到一个 module
- 删除已经失去职责的 Middle Man 和旧 adapter
- 保持 feature work 与 behavior change 在 scope 之外
- 每次失败都回到最近的 green state，定位后再继续

Wide refactor 无法由单步修改保持 green 时，使用 `expand → migrate → contract`：先让新旧形式并存，再按 blast radius 分批迁移，最后在所有 caller 完成迁移后删除旧形式。

发现 ADR 冲突时先说明。只有真实 friction 足以重新评估原 decision 时才提出修改 ADR；不要静默绕过。Domain term 在 refactor 中被重新命名或澄清时，同步更新 `CONTEXT.md`，但不要把 implementation detail 写入 domain glossary。

## 5. Verification

每个 step 后运行对应的 narrow test；修改 typed interface、schema 或 dependency contract 后及时运行 typecheck 或 compile check。全部步骤完成后运行：

- 与目标 seam 相关的完整 regression suite
- 项目要求的 build、typecheck 和 lint
- 能验证 compatibility、migration 或 performance invariant 的检查

审查最终 diff，确认所有修改都服务于选定 refactor，且不存在 accidental behavior change、无关 cleanup、临时兼容代码或未删除的旧路径。

除非用户明确要求，否则不要 commit、push、创建 PR、删除外部资源或修改 issue tracker。

## Handoff

将结果交给 `code-review`，包含：

- refactor scope 与 fixed point
- Before/After 的 module、interface 和 seam
- 保持不变的 behavior 与 invariant
- 执行过的 verification command 及结果
- 删除或保留的 compatibility path
- 未解决的 risk、ADR conflict 和后续 candidate

`code-review` 的 Spec axis 应确认 behavior contract 未偏离，Standards axis 应确认 deepening 没有引入新的 smell。

## 完成标准

当选定 architecture friction 已消除或显著集中，目标 module 的 interface 更小且 leverage/locality 更高，所有既有 observable behavior 与 contract 保持不变，每个 step 和最终 regression suite 均为 green，旧路径和临时兼容结构已按计划处理，且结果可由 `code-review` 独立复核时，refactor 才算完成。
