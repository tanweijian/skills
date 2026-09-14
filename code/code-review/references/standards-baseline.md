# Standards baseline

本 baseline 用于补充仓库已记录的 coding standards。仓库规则始终优先；以下 code smell 均为 heuristic，报告时使用“可能存在”而不是 hard violation。跳过 formatter、linter 或其他 tooling 已可靠覆盖的问题。

检查 diff 时关注：

- **Mysterious Name**：function、variable 或 type 的名称无法揭示其职责或内容。优先重命名；若无法找到准确名称，通常说明设计仍不清晰。
- **Duplicated Code**：多个 hunk 或 file 中出现相同的逻辑形态。提取共享行为，并让各调用点复用。
- **Feature Envy**：method 操作其他 object 的数据明显多于自身数据。考虑将行为移动到它真正依赖的数据附近。
- **Data Clumps**：相同的一组 field 或 parameter 反复共同出现。考虑将其建模为一个明确的 type。
- **Primitive Obsession**：primitive 或 string 承担了重要 domain concept。考虑用小型 domain type 表达约束和语义。
- **Repeated Switches**：多个位置对同一种 type 重复使用 `switch` 或 `if` cascade。考虑使用 polymorphism 或共享映射集中变化。
- **Shotgun Surgery**：一个逻辑变化要求在许多 file 中进行分散修改。考虑将共同变化的知识集中到一个 module。
- **Divergent Change**：一个 file 或 module 因多个无关原因被同时修改。考虑按变化原因拆分职责。
- **Speculative Generality**：为当前 spec 并不需要的场景增加 abstraction、parameter 或 extension hook。删除未被实际需求支撑的泛化。
- **Message Chains**：调用者依赖较长的 `a.b().c().d()` navigation。考虑由靠近链起点的 object 隐藏内部导航。
- **Middle Man**：class 或 function 的主要作用只是原样转发。考虑直接使用真正承担行为的 module。
- **Refused Bequest**：subclass 或 implementer 忽略、绕过或覆盖大部分继承内容。考虑取消 inheritance，改用 composition。

报告 smell 时必须引用具体 hunk，并说明它为何会增加本次变更的风险。仅仅匹配名称不足以形成 finding。
