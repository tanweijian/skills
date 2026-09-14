---
name: code-tdd
description: 使用 test-first 和 red-green loop 实现代码变更。用于用户明确要求 TDD、要求在修复 bug 前先编写 regression test，或希望由 integration test 驱动实现；非 test-first 的实现任务使用 code-implement。
---

# Code TDD

使用 TDD，将已确认的行为逐个转化为可工作的代码，每次只推进一个可观察的 vertical slice。

## 输入

从当前最可靠的 behavior contract 开始：产品 PRD 中的 acceptance criteria、已批准的 code spec、issue 或用户的明确要求。保持其中的术语和范围。若契约中仍存在会改变 public behavior 的未决事项，先明确该事项，再进入实现。

如果仓库中存在 `AGENTS.md`、`CONTEXT.md` 或相关 ADR，先读取并遵循。使用项目现有的 test command、测试约定和测试目录。

## 确定测试 seam

识别用于触发和观察目标行为的 public interface。优先使用 code spec 或现有代码中的 seam。在编写第一个测试前，明确说明选择的 seam；仅当这个选择会创建或实质性改变 public interface 时，才请求用户确认。

测试应当能够承受内部 refactoring。选择测试形式或检查 assertion 是否聚焦行为时，读取 [references/tests.md](references/tests.md)；引入 mock 或 fake 前，读取 [references/mocking.md](references/mocking.md)。

## Red-green loop

按 vertical slice 推进，每次只处理一个行为，并重复以下循环：

1. **Red：** 编写一个测试，表达下一个可观察行为。运行范围最小且相关的 test command，确认失败原因确实是目标行为尚未实现，而不是 setup、语法或无关缺陷。
2. **Green：** 只编写足以通过该测试的实现。运行同一测试直至通过，再运行相关 test suite 检查 regression。
3. **Advance：** 根据刚完成的 slice 所揭示的信息选择下一个行为。避免为想象中的实现细节批量编写测试。

将 refactoring 放在本循环之外。目标行为进入 green 后，记录值得进行的结构调整，并交给 `code-review` 或 `code-refactor`。

## 测试质量

- 通过 public interface 验证行为。
- 从 behavior contract 或其他独立的 source of truth 得到 expected result。
- 每个测试只聚焦一个行为；描述同一结果时可以包含多个 assertion。
- 只 mock system boundary；在可行时使用真实的内部 module 和实际的 integration path。
- 修复已报告的缺陷前，先添加能够在该缺陷上失败的 regression coverage。

## Handoff

报告已完成的 behavior slice、已测试的 seam、执行过的 command，以及尚未覆盖的 acceptance criteria。将 green implementation 和暂缓处理的结构问题交给 `code-review`。

## 完成标准

当所有范围内行为都在已确认的 seam 上具备可观察的 coverage、每个新测试都曾在实现前因预期原因失败、所有相关测试均通过，并且未解决的产品或技术决策已被明确记录时，工作才算完成。
