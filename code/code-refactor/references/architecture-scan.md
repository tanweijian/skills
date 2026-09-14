# Architecture scan

仅在用户要求广泛寻找 refactor candidates 时读取。Targeted refactor 不需要生成完整 scan report。

## Candidate selection

从用户指定范围或近期 hot spot 开始，记录实际遇到的 friction。不要按机械 metric 扫描整个仓库，也不要为了展示数量制造 speculative candidates。

每个 candidate 应包含：

- **Name**：用 domain language 命名要加深的 module
- **Files/modules**：涉及的现有代码位置
- **Evidence**：重复变化、测试困难、知识分散或 seam leakage 的具体证据
- **Problem**：当前 architecture 为什么缺少 depth 或 locality
- **Before**：现有 module、interface、seam 与 dependency 关系
- **After**：建议收缩的 interface，以及将被隐藏到 implementation 内的复杂度
- **Benefits**：对 leverage、locality 和 testability 的直接改善
- **Risk**：migration、compatibility、blast radius 和 ADR conflict
- **Recommendation**：`Strong`、`Worth exploring` 或 `Speculative`

最后给出一个 Top recommendation，说明它为何比其他 candidates 更值得优先处理。不要在用户选择前设计完整 interface 或修改代码。

## Visual report

当 candidates 超过一个，且 dependency、call flow 或 seam leakage 难以用短文本比较时，使用 before/after visual：

- dependency 或 call flow 使用 graph
- 多层 shallow forwarding 使用 cross-section
- interface 相对 implementation 过宽使用 mass diagram
- 多个 internal call 可被一个 deep module 隐藏时使用 call-graph collapse

Visual 应突出 module、seam、leakage 和 deepening，不做装饰性 dashboard。每个 candidate 的文字保持简短，让 diagram 承担关系说明。

用户明确要求 HTML report 时，将 self-contained HTML 写入 OS temp directory，使用唯一文件名，并向用户提供 absolute path。联网 CDN 不可用时使用本地 CSS/SVG 或 Mermaid-capable visualization，不因此阻塞 architecture scan。
