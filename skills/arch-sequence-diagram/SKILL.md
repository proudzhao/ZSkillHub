---
name: arch-sequence-diagram
description: |
  为项目绘制架构/组件级时序图（UML Sequence 语义，Mermaid sequenceDiagram）。默认只出主成功路径；用户指定或关键类/方法时可细化到类/方法级。
  输出：本地 Markdown（Mermaid）和/或飞书云文档（Mermaid 自动转为画板）。飞书默认新建文档；用户指定已有文档 URL 时追加。
  触发：时序图、sequence diagram、交互时序、调用时序、画时序、场景时序、写入飞书画板时序。
  输入：用户口述，或用户指定的项目文档（本地路径 / 飞书文档 URL）。
---

# 架构时序图

用 UML Sequence 语义绘制**架构/组件级时序**（参与者之间按时间顺序的消息交互）。默认粒度到服务/模块/外部系统；仅在用户指定或某类/方法极为关键时下钻到类/方法。

默认**只出主成功路径**；异常、超时、补偿等仅在用户明确要求时另出图。

## 工作流

1. **确认参数**（缺则简短询问，一次问清）：
   - 场景/用例名称（时序围绕「一个场景」）
   - 输入来源：口述 | 指定文档路径/URL
   - 输出目标：`local` | `feishu` | `both`（未说明则询问；口述后默认 `both`）
   - 飞书：未给已有文档 URL → **新建**；给了 URL/doc_id → **追加**到该文档
   - 粒度：默认架构/组件；用户点名类/方法或声明「展开 XXX」时再细化
   - 场景：默认仅主成功；用户要求异常/异步分支时再加图
2. **采集信息** → 见下方「信息采集」
3. **建模** → 参与者 + 有序消息；读 `references/seq-rules.md`
4. **校验** → 跑校验清单（同文件）
5. **渲染 Mermaid** → 读 `references/mermaid-sequence.md`（中文标签必须双引号）
6. **写文档** → 读 `references/doc-template.md`，按目标输出

## 信息采集

从口述或指定文档中抽取（未知则标注「待确认」，不要编造）：

| 维度 | 问什么 |
|------|--------|
| 场景 | 主成功路径从触发到结束是什么？ |
| 参与者 | 谁发起？经过哪些服务/网关/存储/外部系统？ |
| 消息顺序 | 每一步谁调用谁？同步还是异步？返回什么？ |
| 关键数据 | 消息上需标注的关键参数/结果（保持短） |
| 细化点 | 是否有必须展开的类/方法？ |

指定飞书文档时：用 `user-feishu-doc-mcp` 的 `fetch-doc`（或 `feishu-doc-read` skill）读取后再建模。

## 符号约定（UML → Mermaid）

| 元素 | 含义 | Mermaid |
|------|------|---------|
| 参与者 | 组件/服务/系统 | `participant S as "订单服务"` |
| Actor | 外部用户/渠道 | `actor U as "用户"` |
| 同步调用 | 实心箭头 | `A->>B: "下单"` |
| 返回 | 虚线箭头 | `B-->>A: "订单号"` |
| 异步 | 开放箭头 | `A-)B: "异步: 已创建事件"` |
| 可选/分支 | 组合片段 | `opt` / `alt`（主成功图尽量少用 alt） |

## 输出路由

### 本地 Markdown

- 路径：用户指定，或默认 `docs/sequence/<场景短名>-时序.md`
- 内容：按 `references/doc-template.md`，内嵌 Mermaid `sequenceDiagram`

### 飞书（画板）

使用 MCP **`user-feishu-doc-mcp`**（优先于 `user-feishu2md`）：

| 场景 | 工具 | 要点 |
|------|------|------|
| 新建（默认） | `create-doc` | `title` + `markdown`；正文**不要**重复一级标题 |
| 追加到指定文档 | `update-doc` | `mode: append`，`doc_id` 为用户给的 URL/token |
| 长文 | `create-doc` 后 `update-doc` append | 分段写入 |

**画板规则（硬约束）：**

- 用 mermaid 围栏代码块（`sequenceDiagram`）；飞书自动转为画板，返回 `board_tokens`
- **禁止**写入态使用读取态 whiteboard token 语法
- 空白 whiteboard blank 仅占位，本 skill **不用**来画时序
- 中文/非 ASCII 标签一律双引号包裹

完成后向用户返回：本地路径和/或飞书 `doc_url`。

## 与相近能力的边界

- `arch-dataflow-diagram`：数据落点与流动 → 互补；本 skill 管时间顺序交互
- `method-flow`：Java 方法调用链深挖 → 可作细化输入，不替代；默认架构时序不要展开成完整调用树
- `feishu-doc-read`：读飞书 → 本 skill 负责建模与写出
- 数据流图 / 纯业务流程图：勿用本 skill 硬套

## 参考文件（按需加载）

- `references/seq-rules.md` - UML 语义、粒度、场景策略、校验清单
- `references/mermaid-sequence.md` - Mermaid 语法、示例、飞书注意
- `references/doc-template.md` - 本地/飞书文档章节模板
