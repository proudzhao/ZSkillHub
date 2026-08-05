---
name: project-analyzer-trip
description: 深度分析携程内部 Java 项目代码库，结合 AppId（如有）与公司 MCP（Captain/QConfig/BAT/MOM 等）生成中文项目介绍文档。支持可发布应用与无 AppId 的二方库 JAR。触发：分析项目、项目介绍、项目文档、代码分析、生成项目文档、AppId、Captain 应用画像等。
---

# 项目分析 Skill（携程定制）

分析工作区内的携程内部项目，按固定模板生成中文 Markdown 介绍文档。

**必读参考（按需加载）：**

| 文件                                                         | 何时读取                                  |
| ---------------------------------------------------------- | ------------------------------------- |
| [references/doc-template.md](references/doc-template.md)   | **生成文档前必读**；章节与可选规则以它为准               |
| [references/mcp-playbook.md](references/mcp-playbook.md)   | 有 AppId、需调 MCP / 拼链接时                 |
| [references/ctrip-signals.md](references/ctrip-signals.md) | 识别 Framework BOM、DAL、SOA、QConfig 等指纹时 |

## 项目类型判定

| 类型        | 判定                                            | 后续差异                                       |
| --------- | --------------------------------------------- | ------------------------------------------ |
| 可发布应用     | 存在 `**/META-INF/app.properties` 且含有效 `app.id` | 做平台 enrichment；可写 1.1 / 部署 / BAT 等节        |
| 二方库 (JAR) | 无有效 `app.id`；packaging 多为 jar，供他方依赖           | **跳过** Captain/BAT/QConfig 等应用侧 MCP；不写应用链接 |
| 多模块混合     | 部分子模块有 `app.id`，部分为 library                   | 按模块列表；仅对有 AppId 的模块做 enrichment            |

无 AppId **不是错误**：按二方库路径继续静态分析即可。

## 执行流程

### 阶段 0：准备

1. 读取 [references/doc-template.md](references/doc-template.md)，后续输出必须贴合该模板。
2. 明确用户指定的仓库/模块；未指定且工作区多仓库时先询问。

### 阶段 1：项目发现

```bash
# 构建系统与模块
find <root> -maxdepth 2 \( -name pom.xml -o -name build.gradle -o -name build.gradle.kts -o -name package.json \)
# AppId（可能 0 个或多个）
find <root> -path "*/META-INF/app.properties"
# Git 远程（用于 GitLab 链接）
git -C <root> remote -v
```

产出：构建类型、模块列表、每个模块的 `app.id`（如有）、`git remote`。

多 AppId：表格列出模块↔AppId；用户未指定时优先分析可部署模块，或询问。

### 阶段 2：AppId 与平台 enrichment（条件执行）

- **无 AppId**：跳过本阶段，进入阶段 3；文档中删除 1.1 及「仅可发布应用」章节。
- **有 AppId**：按 [references/mcp-playbook.md](references/mcp-playbook.md) 调用 MCP。

最低要求（P0，有 AppId 时尽量完成）：

1. Captain `get_application_info(application_id)` → 元数据；成功才可写 Captain 应用链接。
2. 若有 `ci_project_id` / `ci_repo` → CI 链接、GitLab 链接候选。
3. 可选 P1：`get_groups`；QConfig `list_config_files`；BAT 依赖（先 helper）；MOM `get_project_list`。

规则：

- MCP 失败（权限/空结果）→ **降级继续**，附录注明；禁止臆造元数据。
- **禁止**调用写操作（deploy、改配置、改权限等）。
- **链接**：仅按 playbook / 模板附录 B 收录；宁缺毋滥。

### 阶段 3：技术栈解析

从 pom / gradle 提取 Java、Spring、BOM、依赖。携程指纹见 [references/ctrip-signals.md](references/ctrip-signals.md)。

识别要素：Framework BOM / Super POM、DAL、Baiji SOA、CDubbo、QConfig、QMQ、CRedis、CAT、QSchedule 等（有则写，无则省略）。

### 阶段 4：代码结构扫描

```bash
find <src> -type d ...   # 分层目录
find ... -name "*Controller.java" | wc -l
# 配置清单（含携程特有）
find <resources> \( -name "app.properties" -o -name "Dal.config" -o -name "*.yml" -o -name "*.xml" -o -name "*.properties" \)
```

分层关键词：controller/api、service/biz、dao/mapper、entity/po、dto/vo、config、job、client/proxy 等。

### 阶段 5：业务域深度分析

- **有 Controller / HTTP API**：按路由聚类业务域（优先）。
- **SOA 服务端**：按 Service / Operation 聚类。
- **纯二方库**：按对外 API / 核心包能力聚类，不强行套路由表。

并行读关键入口类；大项目 Controller>50 时先抓头部重要模块，其余按前缀归类。

### 阶段 6：架构与依赖

按实际存在项梳理（无则跳过）：

| 维度   | 线索                                                  |
| ---- | --------------------------------------------------- |
| RPC  | `@DubboReference` / Baiji Client / Feign；MOM/BAT 可补 |
| 数据   | `Dal.config`、`*_dalcluster`、数据源 yml                 |
| MQ   | QMQ / Kafka 注解与配置                                   |
| 配置中心 | `@QConfig` / `@QMapConfig`；QConfig MCP 文件列表         |
| 安全   | SSO / IAM / 权限注解                                    |

### 阶段 7：文档生成

1. 再次对照 [references/doc-template.md](references/doc-template.md)。
2. 填充有事实支撑的章节；**可选章无内容则整节删除**，不留空标题。
3. 相关链接表：只保留已校验行。
4. 输出规范：
   - 语言：中文
   - 文件名：`{项目名称}-介绍文档.md`
   - 位置：`<workspace>/{项目名称}-介绍文档.md`（或用户指定路径）
   - 架构图：ASCII；表格优先；数字来自实际统计
   - 删除模板中的「生成说明」提示块与附录 B（附录 B 仅约束执行，不写入用户文档；附录 A 可选保留）

## 执行注意事项

### 并行

阶段 1 的文件发现可并行；有 AppId 时 P0 MCP 与阶段 3–4 静态扫描可并行；业务深度分析依赖结构扫描结果。

### 信息优先级

1. AppId + Captain 元数据（若有）
2. 业务入口（Controller / SOA / 对外 API）
3. 构建配置与携程中间件指纹
4. Dal.config / RPC / MQ / QConfig
5. 工具类、枚举、测试细节

### 常见陷阱

- 无 AppId ≠ 分析失败；按二方库写文档。
- 多模块可能多个 `app.properties`，勿只读一个就当全仓唯一应用。
- WAR：`WEB-INF/classes/META-INF/app.properties`。
- `Dal.config` 文件名 D 大写；也可能在 QConfig 而非仓库内。
- 勿把 `.git` / `target` 计入规模统计。
- 勿在文档粘贴密钥、账号、完整敏感配置。

### 大型项目

- Controller>50：详写约 20 个核心，其余前缀归类。
- Service 过多：跳过纯 CRUD 转发。
- 大文件先读头部（如 `head` / 限行读取）取关键注解与签名。
