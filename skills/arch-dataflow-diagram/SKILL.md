---
name: arch-dataflow-diagram
description: |
  为项目绘制架构数据流图（Architecture Data Flow），默认 Gane-Sarson 语义，分层 Level 0（上下文）+ Level 1（主加工），复杂度高时再下钻。
  输出：本地 Markdown（Mermaid）和/或飞书云文档（Mermaid 自动转为画板）。飞书默认新建文档；用户指定已有文档 URL 时追加。
  触发：数据流图、架构数据流、DFD、画数据流、系统数据流、写入飞书画板、输出数据流文档。
  输入：用户口述，或用户指定的项目文档（本地路径 / 飞书文档 URL）。
---

# 架构数据流图

用 Gane-Sarson 语义绘制**架构数据流**（系统/服务/存储/外部系统之间的数据移动），不是经典结构化分析的纯业务 DFD，也不是时序图或业务流程图。

## 工作流

1. **确认参数**（缺则简短询问，一次问清）：
   - 系统/范围名称
   - 输入来源：口述 | 指定文档路径/URL
   - 输出目标：`local` | `feishu` | `both`（未说明则询问；口述后默认 `both`）
   - 飞书：未给已有文档 URL → **新建**；给了 URL/doc_id → **追加**到该文档
   - 层级：默认 Level 0 + Level 1；用户要求或加工 >7 / 单加工过复杂时再 Level 2
2. **采集信息** → 见下方「信息采集」
3. **建模** → 四类元素 + 分层；读 `references/dfd-rules.md`
4. **校验** → 跑校验清单（同文件）
5. **渲染 Mermaid** → 读 `references/mermaid-patterns.md`（中文标签必须双引号）
6. **写文档** → 读 `references/doc-template.md`，按目标输出

## 信息采集

从口述或指定文档中抽取（未知则标注「待确认」，不要编造）：

| 维度 | 问什么 |
|------|--------|
| 边界 | 本图系统是什么？边界外有哪些调用方/被调方？ |
| 入口数据 | 谁发起？带什么数据？ |
| 加工 | 主要处理步骤（服务/模块/作业）？ |
| 存储 | DB / Cache / MQ / 文件 / 配置中心？ |
| 出口 | 写回谁、回调谁、同步到哪？ |
| 异步 | 有无消息、定时、批量？ |

指定飞书文档时：用 `user-feishu-doc-mcp` 的 `fetch-doc`（或 `feishu-doc-read` skill）读取后再建模。

## 符号约定（Gane-Sarson 语义 → Mermaid）

| 元素 | 含义 | Mermaid |
|------|------|---------|
| 外部实体 | 边界外系统/用户/渠道 | `E1["用户/渠道"]` |
| 加工 | 本系统内处理（服务/模块/作业） | `P1(["1.0 处理订单"])` |
| 数据存储 | 持久或准持久存放 | `D1[("D1 订单库")]` |
| 数据流 | 移动的数据（名词），非控制流 | `A -->|"订单请求"| B` |

Level 0：单一加工表示整个系统 + 外部实体 + 跨边界数据流（通常不出现内部存储）。
Level 1：3-7 个主加工 + 相关存储 + 内外部数据流。

## 输出路由

### 本地 Markdown

- 路径：用户指定，或默认 `docs/dataflow/<系统短名>-数据流.md`
- 内容：按 `references/doc-template.md`，内嵌 Mermaid 代码块

### 飞书（画板）

使用 MCP **`user-feishu-doc-mcp`**（优先于 `user-feishu2md`）：

| 场景 | 工具 | 要点 |
|------|------|------|
| 新建（默认） | `create-doc` | `title` + `markdown`；正文**不要**重复一级标题 |
| 追加到指定文档 | `update-doc` | `mode: append`，`doc_id` 为用户给的 URL/token |
| 长文 | `create-doc` 后 `update-doc` append | 分段写入 |

**画板规则（硬约束）：**

- 用 mermaid 围栏代码块；飞书自动转为画板，返回 `board_tokens`
- **禁止**写入态使用读取态 whiteboard token 语法
- 空白 whiteboard blank 仅占位，本 skill **不用**来画数据流
- 中文/非 ASCII 标签一律双引号包裹

完成后向用户返回：本地路径和/或飞书 `doc_url`。

## 与相近能力的边界

- `method-flow`：Java 方法调用链 → 可作输入素材，本 skill 不替代
- `feishu-doc-read`：读飞书 → 本 skill 负责建模与写出
- 时序图 / 纯业务流程图：不在本 skill 范围；用户要那些时改用对应写法，勿硬套本规范

## 参考文件（按需加载）

- `references/dfd-rules.md` - 规则、分层、校验清单、架构语义说明
- `references/mermaid-patterns.md` - Mermaid 形状、示例、飞书注意事项
- `references/doc-template.md` - 本地/飞书文档章节模板
