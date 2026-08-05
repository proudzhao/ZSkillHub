# {项目名称} 项目介绍文档

> 生成说明（写入正式文档时可删除本提示块）：
> - `{...}` 为占位符，分析后替换为真实内容。
> - 标注「可选」的章节：无对应事实则整节省略，不要留空标题。
> - 标注「仅可发布应用」的章节：无 AppId / 非发布型 JAR 库时省略。
> - 相关链接：仅写入已校验为正确的 URL（见文末《链接收录规则》）；未校验则不写该项。

---

## 1. 项目概述

| 项 | 内容 |
|----|------|
| 项目名称 | {artifactId / 仓库名 / Captain 中文名} |
| 项目类型 | {可发布应用 / 二方库(JAR) / 多模块混合（含应用+库）} |
| 一句话定位 | {业务职责简述} |
| 所属组织 / 产线 / 产品 | {有 Captain 元数据则填；否则填代码/文档可推断信息，或写「未知」} |
| 包路径 / 坐标 | `{groupId}:{artifactId}`（多模块可列主模块） |
| 打包方式 | {jar / war / pom 聚合} |
| Java 版本 | {x.y} |
| 框架基线 | {Framework BOM / Super POM / Spring Boot 版本，能确定则写} |

### 1.1 AppId 与应用元数据（可选 · 仅可发布应用）

> 无 `META-INF/app.properties` 的 `app.id`、且非可发布应用时：**整节删除**。  
> 多模块多 AppId 时：用表格逐行列出，或拆成多个 1.1.x 小节。

| 项 | 内容 |
|----|------|
| AppId | {app.id} |
| 应用英文名 | {Captain name} |
| 应用中文名 | {Captain chinese_name} |
| Owner | {owner} |
| 管理员 | {admins，可截断} |
| 重要级别 | {L1–L5 / importance} |
| 容器类型 | {container，如有} |
| CI Project Id | {ci_project_id，如有} |

### 1.2 相关链接（可选）

> **只列已校验通过的链接**；校验失败或不适用则不要出现该行。  
> 二方库通常仅有 GitLab（及可选 CI）；Captain / BAT / QConfig 等依赖有效 AppId。

| 平台 | 链接 | 校验依据 |
|------|------|----------|
| GitLab | {https://git.dev.sh.ctripcorp.com/{group}/{repo}} | 本地 `git remote` 或 Captain `ci_repo` 转换且仓库路径可信 |
| Captain 应用 | {https://captain.release.ctripcorp.com/app/{appId}} | `get_application_info(appId)` 成功返回 |
| Captain CI | {https://captain.release.ctripcorp.com/ci/project/{ci_project_id}/pipeline} | Captain 返回有效 `ci_project_id` |
| BAT | {http://bat.fx.ctripcorp.com/application/{appId}} | AppId 已由 Captain 或本地 `app.id` 确认存在 |
| QConfig | {仅当确认该应用页深链正确时填写；否则省略本行} | MCP 能列出该 appId 配置，且 URL 模式已验证 |
| MOM | {仅当 MOM 返回/可打开的项目页 URL 正确时填写} | `get_project_list` 等命中且 URL 可解析 |
| 其他 | {用户提供或文档中的已验证链接} | 人工/页面确认 |

---

## 2. 技术栈

### 2.1 后端技术

| 技术 | 版本 | 用途 / 说明 |
|------|------|-------------|
| Java | | |
| Spring / Spring Boot | | 如有 |
| Framework BOM | | 如有 |
| ORM / DAL | | MyBatis / JPA / DAL 等 |
| RPC | | Baiji SOA / CDubbo / Feign / HTTP 等 |
| 配置中心 | | QConfig 等 |
| 消息 | | QMQ / Kafka 等 |
| 缓存 | | CRedis / Redis 等 |
| 其他中间件 | | CAT / QSchedule / Hickwall 等按实际 |

### 2.2 前端技术（可选）

| 技术 | 版本 | 用途 / 说明 |
|------|------|-------------|
| | | |

---

## 3. 项目结构

### 3.1 模块说明（多模块时）

| 模块 | 类型（应用/库） | AppId（如有） | 职责 |
|------|-----------------|---------------|------|
| | | | |

### 3.2 目录树

```text
{展示关键目录，忽略 .git / target / node_modules 等}
```

### 3.3 代码规模（可选）

| 维度 | 数量 |
|------|------|
| Java 源文件 | |
| Controller | |
| Service | |
| Mapper/DAO | |
| 前端页面/组件 | |
| … | |

---

## 4. 业务模块详解

> 二方库若无 HTTP/业务入口：可改为「对外 API / 核心能力」按包或类聚类，不必强行用 Controller 路由。

### 4.1 {业务域 / 能力域 1}

- **职责：** …
- **入口：** {路由 / SOA Operation / 对外 API}
- **子能力：**

| 子模块 / 接口 | 路径或方法 | 说明 |
|---------------|------------|------|
| | | |

- **核心流程：** …
- **依赖服务 / 组件：** …

### 4.2 {业务域 / 能力域 2}

…

---

## 5. 核心架构设计

### 5.1 整体架构

```text
{ASCII / 文本架构图}
```

### 5.2 分层与包结构（可选）

| 层级 | 包路径关键词 | 说明 |
|------|--------------|------|
| | | |

### 5.3 分库分表 / 数据访问（可选）

- DalCluster / 数据源：…
- 分片策略：…

### 5.4 多区域 / 多环境（可选）

…

### 5.5 权限与安全（可选）

…

---

## 6. 外部依赖

### 6.1 服务依赖（RPC / HTTP）

| 依赖方 | 接口 / ServiceCode | 用途 | 来源（代码 / BAT / MOM） |
|--------|-------------------|------|-------------------------|
| | | | |

### 6.2 中间件与基础设施

| 类型 | 名称 / Topic / Cluster | 用途 |
|------|------------------------|------|
| | | |

### 6.3 Maven 二方依赖（可选 · 二方库或关键 SDK）

| groupId | artifactId | version | 说明 |
|---------|------------|---------|------|
| | | | |

### 6.4 运行时调用拓扑（可选 · 仅可发布应用）

> 来自 BAT 等；采样数据仅作依赖关系参考。

| 方向 | AppId / 服务 | 说明 |
|------|--------------|------|
| 上游（调用我） | | |
| 下游（我调用） | | |

### 6.5 配置中心文件（可选 · 仅可发布应用）

| 环境 | 文件名 | 说明（勿粘贴敏感值） |
|------|--------|----------------------|
| | | |

### 6.6 SOA 契约（可选）

| ServiceCode / 服务名 | 版本 | 主要 Operation | MOM 链接（仅已校验） |
|----------------------|------|----------------|----------------------|
| | | | |

---

## 7. 前端架构（可选）

### 7.1 目录结构

### 7.2 路由与权限

---

## 8. 数据库与持久层（可选）

### 8.1 实体 / 表映射

| 实体类 | 表名 | 说明 |
|--------|------|------|
| | | |

### 8.2 Mapper / DAO

| 接口 | 说明 |
|------|------|
| | |

---

## 9. 部署与运行（可选 · 仅可发布应用）

| 项 | 内容 |
|----|------|
| 部署形态 | {容器 / 分组概要，来自 Captain groups} |
| 环境 | {fat / uat / prod 等存在情况} |
| 健康检查 | {如 /vi/health，代码或配置中可见时} |

---

## 10. 测试与质量（可选）

| 项 | 内容 |
|----|------|
| 单测 / 模块测 | |
| 自动化 Job（TestRun 等） | {有 AppId 且查询命中时} |

---

## 11. 总结

- **项目定位：** …
- **技术特点：** …
- **边界与注意点：** …（如：本仓库为二方库、无独立 AppId；或某模块才是发布单元）

---

## 附录 A：分析范围与局限（可选）

- 分析仓库路径：…
- 是否含 AppId：{是 / 否}；若否，已跳过 Captain/BAT/QConfig 等应用侧 enrichment
- MCP 调用情况：{成功项 / 权限不足降级项}
- 未覆盖内容：…

---

## 附录 B：链接收录规则（Skill 执行时遵守；正式文档可删）

**总原则：宁缺毋滥。无法证明 URL 正确，就不写入「相关链接」。**

### B.1 项目类型与链接预期

| 项目类型 | 典型特征 | 通常可有的链接 |
|----------|----------|----------------|
| 可发布应用 | 存在 `resources/META-INF/app.properties` 且含有效 `app.id` | GitLab、Captain、CI、BAT；QConfig/MOM 需额外校验 |
| 二方库 JAR | 无 `app.id`，packaging=jar，供他方依赖 | GitLab（remote）；一般无 Captain/BAT/QConfig 应用页 |
| 多模块混合 | 部分子模块有 `app.id`，部分为 library | 按子模块分别收录；库模块不写应用链接 |

### B.2 已验证可模板化的 URL（有 AppId / 元数据时）

| 平台 | URL 模板 | 前置条件 |
|------|----------|----------|
| Captain 应用 | `https://captain.release.ctripcorp.com/app/{appId}` | Captain `get_application_info` 成功 |
| Captain CI | `https://captain.release.ctripcorp.com/ci/project/{ci_project_id}/pipeline` | 返回了 `ci_project_id` |
| GitLab（自 ci_repo） | `git@git.dev.sh.ctripcorp.com:{group}/{repo}.git` → `https://git.dev.sh.ctripcorp.com/{group}/{repo}` | 优先用本地 `git remote -v` 的 http(s)；否则用 Captain `ci_repo` 按此规则转换 |
| BAT 应用 | `http://bat.fx.ctripcorp.com/application/{appId}` | AppId 已确认（文档案例路径为 `/application/{appId}/…`） |

### B.3 暂不默认拼接的链接

| 平台 | 原因 | 处理方式 |
|------|------|----------|
| QConfig 应用深链 | 门户入口已知（`http://qconfig.ctripcorp.com/webapp/page/index.html#/qconfig`），**按 appId 的稳定深链未在文档中充分证实** | 有权限且 MCP 读成功时，可在正文写「配置文件清单」；**相关链接表默认不写 QConfig**，除非用户确认深链或后续规则更新 |
| MOM 项目页 | MCP 可按 term 查项目，但统一门户深链模式未固化 | **仅当接口/页面返回明确 URL，或 `get_url_project` 能解析该 URL 时**才写入 |
| SOA Portal | `http://gov.soa.fx.ctripcorp.com/` 为门户级入口，非项目专属 | 不作为「本项目链接」默认项；有 serviceCode 专属页且验证后再加 |

### B.4 禁止行为

- 凭猜测拼接未经验证的 hash 路由或 query
- 将 SSH `git@…` 原文当作可点击网页链接（应转换为 https 浏览地址）
- AppId 无效、Captain 查无、或明确无发布属性时，仍输出 Captain/BAT/QConfig 链接
- 在文档中粘贴 QConfig/密钥类敏感配置全文
