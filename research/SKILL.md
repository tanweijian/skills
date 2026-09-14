---
name: research
description: 调查技术、产品或领域问题，优先使用 high-trust primary sources，并将可追溯的 findings 保存为仓库内的 Markdown research note。用于查阅官方 docs、source code、spec、standards、paper、first-party API，或把资料阅读工作委托给 background agent。
---

# Research

围绕一个明确问题开展可追溯研究，并把结论沉淀为仓库内可复用的 Markdown 文档。Research 提供 evidence 和判断依据，不替代后续的 product decision 或 code decision。

## 明确研究任务

开始前确认：

- 要回答的核心问题，以及答案将支持什么决策。
- 研究范围、时间范围、技术版本和适用环境。
- 已知约束、现有材料与期望交付位置。
- 停止条件：达到什么证据强度即可形成结论。

如果缺失信息不会实质改变研究方向，采用合理假设继续，并在文档中记录假设。只有关键歧义会导致完全不同的研究路径时，才向用户确认。

## 选择执行方式

当环境支持 background agent，且任务包含较多独立的检索或阅读工作时，将研究委托给 background agent，使主流程可以继续推进。委托内容必须包含研究问题、范围、source 标准、产物路径和完成条件。

以下情况可直接研究：

- 问题范围较小，少量 source 即可回答。
- 当前环境不支持 background agent。
- 需要紧密结合当前仓库代码、上下文或即时判断。

无论由谁执行，最终都要检查 source 质量、claim 与 citation 的对应关系，以及产物是否真正回答研究问题。

## 使用可信来源

按以下优先级选择 source：

1. 官方 docs、正式 spec、standards、first-party API、维护方 source code 和 repository。
2. Peer-reviewed paper、权威 dataset、监管机构或标准组织发布的材料。
3. Secondary source 仅用于发现线索、补充背景或理解争议；关键 claim 必须继续追溯到 primary source。

不要把搜索结果摘要、聚合页面或未经验证的转述作为最终证据。涉及可能变化的信息时，检查发布日期、更新时间、版本和当前适用性。

如果 primary sources 相互冲突：

- 分别记录各自的 claim、适用版本和上下文。
- 优先采用更新、约束更明确或更接近实际实现的 source。
- 无法消解时保留分歧，不制造确定结论。

## 执行研究

1. 将核心问题拆成少量可验证的 research questions。
2. 优先检查仓库内已有 docs、source code、ADR 和 research notes，再查外部 source。
3. 阅读 claim 所在的完整上下文，记录 URL 或文件路径、版本、发布日期或 commit 等定位信息。
4. 对影响决策的关键 claim，在合理情况下使用另一份独立 primary source 交叉验证；如果只有一个权威来源，明确说明。
5. 区分事实、推断和未知项。推断必须说明依据，未知项不得包装成事实。
6. 综合回答问题，提炼影响与取舍，不把检索记录简单堆叠成结论。

## 编写交付物

将 findings 写入一个 Markdown 文件。优先遵循仓库已有 research 文档约定；如果没有约定，使用 `docs/research/<topic-slug>.md`，并在交付时说明路径。

文档至少包含：

```markdown
# <研究主题>

## 问题与范围

## 结论摘要

## Findings

## 对决策的影响

## 未知项与证据冲突

## Sources
```

根据任务复杂度删减空章节或增加必要章节，但保持一个文件能够独立阅读。

## Citation 标准

- 让 citation 紧邻其支持的 claim，而不是只在文末罗列链接。
- 外部 source 使用可直接访问的原始页面链接，不引用搜索结果页。
- 仓库内证据尽量标注文件路径、行号或 commit。
- 精确措辞只有在确有必要时才短量引用，其余内容使用准确转述。
- 一个 citation 不应被用于支持 source 中没有明确表达的结论。

## 完成标准

交付前确认：

- 核心研究问题已得到回答，或无法回答的原因和证据缺口已明确。
- 重要 claim 均有 primary source 支撑，事实、推断和未知项清晰分离。
- 时效性信息已核对日期、版本和适用范围。
- 冲突证据、限制条件和不确定性没有被隐藏。
- Findings 已保存为一个符合仓库约定的 Markdown 文件，并向用户报告其位置和关键结论。
