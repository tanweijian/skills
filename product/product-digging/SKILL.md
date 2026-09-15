---
name: product-digging
description: 将模糊的产品想法或功能诉求挖掘为有证据的用户问题与机会判断。用于需求访谈、问题澄清、验证目标用户、场景、现有替代、痛点和价值，或下游因 Problem / Value 不明确而回退；目标问题与产品行为已经明确、只需制作原型或编写 PRD 时不使用。
---

# Product Digging

围绕待做的产品决策挖掘真实问题，而不是替一个已有方案寻找理由。交付物是可追溯的 opportunity brief：说明谁在什么情境下遇到什么问题、现有行为和影响是什么、证据支持到什么程度，以及下一步应该继续验证、做原型、进入 PRD 还是停止投入。

## 入口原则

可以从一句想法、功能请求、用户反馈、访谈材料、数据异常或下游退回的问题开始。先利用已有上下文；只有关键缺口会改变机会判断，且无法从材料中得到时，才向用户提问。

把用户给出的方案视为 solution hypothesis，保留其原意，但与 problem evidence 分开。用户、使用者、购买者和决策者可能不是同一角色；只在这种差异影响价值判断时区分他们。

用以下状态管理重要陈述：

- **Decision**：用户已明确作出的产品取舍。
- **Evidence**：可定位来源的观察、行为、数据或原话。
- **Inference**：由 evidence 推导的解释，并说明推导依据。
- **Hypothesis**：尚待验证但会影响决策的主张。
- **Unknown**：当前无法回答且不应猜测的事项。

证据缺失表示未知，不表示问题不存在；单个强烈反馈也不自动代表普遍需求。

## 1. 定义 discovery decision

先提取本次挖掘需要解锁的决策，例如是否继续投入、优先服务哪个 segment、哪个问题值得先验证，或某个功能诉求背后的问题是否真实存在。同时记录：

- 起始想法或触发信号，以及是谁提出的。
- 候选用户、发生场景和期望进展。
- 已知约束、为什么现在处理、已有证据来源。
- 一旦答案不同，会真正改变决策的未知项。

若没有明确决策，形成一个最小工作假设并标注为 hypothesis。完成本步骤时，应能用一句话说明“我们正在判断什么”，而不只是复述功能名称。

## 2. 建立 evidence ledger

围绕每个会改变决策的 claim，记录其状态、来源、置信度和影响。简单任务可直接在对话中维护；材料较多时使用紧凑表格：

| Claim | Status | Evidence / Source | Confidence | Decision impact |
| --- | --- | --- | --- | --- |
| 目标用户经常被某步骤阻塞 | Hypothesis | 待验证 | Low | 决定是否继续投入 |

优先寻找能描述实际行为的证据：产品使用或业务记录、最近一次具体事件、可核实的访谈材料。笼统偏好、未来意愿和无来源推测只能作为较弱信号。记录反证、没有采用现有方案的原因和样本偏差；不要只收集支持起始想法的材料。

## 3. 沿真实行为向下挖

每轮选择 decision impact 最大且置信度最低的 claim。已有材料足够时直接分析；需要用户补充时，优先一次提出一个高信息量问题，并沿回答中的具体线索继续，而不是机械执行问卷。

把抽象陈述落到最近一次真实经历，逐步确认：

- **Actor and context**：谁在什么触发条件下尝试完成什么进展。
- **Current behavior**：实际采取了哪些步骤，使用了什么替代方案或 workaround。
- **Friction and cause**：具体在哪里受阻，表面症状背后的原因是什么。
- **Impact**：造成了多少时间、金钱、风险、机会或情绪成本；频率和严重程度如何。
- **Alternatives and commitment**：为什么现有替代仍不够好，以及是否已经付出成本寻找或拼装解决办法。
- **Variation**：哪些人或场景不受影响，什么条件会让问题消失。

优先问“最近一次发生时你做了什么”“然后发生了什么”“现在怎么解决”。对于“你会不会用”“你想要哪些功能”之类的回答，把它们保留为偏好信号，再追问可观察行为。不要向受访者推销方案或把引导性问题当作验证。

用户无法提供一手信息时，检查请求范围内已有的 issue、support ticket、访谈、analytics、销售记录、操作日志和研究资料。外部市场或竞品信息可以解释背景，但不能单独证明用户问题成立；需要专门查证领域事实时，把明确 research question 交给 `research`。

## 4. 综合问题与机会

当关键 claim 已有足够材料，写出可被证据反驳的问题陈述：

```text
对于 [segment]，当 [context / trigger] 时，他们需要 [desired progress]，
但由于 [barrier / cause] 只能采用 [current behavior]，从而产生 [measurable impact]。
现有证据是 [source]；仍待验证的是 [highest-risk claim]。
```

区分根因、症状和方案请求。若多个 segment 的场景、替代方式或价值明显不同，分别陈述，不把它们平均成一个虚构用户。比较候选机会时，依据现有证据讨论 reach、frequency、severity、strategic fit 和 confidence；缺少数据的维度保持 unknown，不伪造评分。

主动寻找最强的替代解释和反证，例如问题只发生在错误配置、某个渠道偏差或一次性迁移中。说明这些因素会如何改变机会判断。

## 5. 作出阶段性判断

依据本次 discovery decision 给出以下一种建议，并明确依据：

- **继续 digging**：关键 actor、场景、现有行为、影响或因果解释仍缺失，或证据相互冲突。指出下一条最高风险 hypothesis 和获取证据的最小行动。
- **进入 product-prototype**：目标用户、问题和价值已有可信证据，但核心流程、交互或行为方案仍需观察和比较。给出要验证的 hypothesis、场景和可观察结果。
- **进入 product-prd**：问题、价值、范围和核心产品行为都已明确，剩余未知项不会阻塞需求契约。交接已确认 decision、约束、scope 和 acceptance 目标。
- **停止或降级投入**：证据显示问题影响有限、已有替代足够、目标 segment 不匹配，或在约定的验证成本内仍没有足够信号。说明这是当前证据下的判断及可使其重开的新信号。

不以固定访谈数量宣称“已验证”。证据是否足够取决于待做决策的风险、来源质量、信号是否收敛，以及反证是否得到解释。用户只要求探索或访谈结论时，在 opportunity brief 完成后结束，不自动创建原型、PRD、ticket 或实现。

## 6. 交付 opportunity brief

信息简单时用短段落；复杂任务使用以下结构并删除空项：

```markdown
# Opportunity Brief: <主题>

## Discovery decision
- 本次要判断的决策
- 建议与 confidence

## Target segment and context
- 用户 / stakeholder、触发场景、期望进展

## Problem and current behavior
- 问题陈述
- 当前替代与 workaround
- 频率、严重程度与影响

## Evidence
- 支持证据及来源
- 反证、替代解释与样本限制

## Decisions, hypotheses, and unknowns
- 已确认 decision
- 待验证 hypothesis
- 关键 unknown

## Recommendation and handoff
- 继续 digging / product-prototype / product-prd / 停止投入
- 下一步最小行动、进入条件和所需输入
```

已有 artifact 使用路径或链接引用。没有来源的数字、用户原话和市场事实不得写成 evidence；必要推断必须保持 inference 标签。

## 重新打开 digging

原型观察推翻问题假设、PRD 发现目标用户或价值没有依据、工程反馈暴露出新的用户取舍，或用户改变 segment、场景和成功标准时，只重新挖掘受影响的 claim。保留仍然有效的 decision 与 evidence，并说明新信息为何改变原判断。

## 完成标准

当 discovery decision 已被回答，关键 problem / value claim 都有可定位证据或明确标为 hypothesis / unknown，最强反证与样本限制已呈现，并且建议、confidence、下一条最高风险 hypothesis 和阶段交接条件均清楚时，本次 digging 完成。问题仍未知也可以是有效结论，但不能以未经验证的方案代替问题证据。
