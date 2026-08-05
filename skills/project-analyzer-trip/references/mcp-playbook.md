# MCP Playbook 与链接收录

有有效 `app.id` 时按本文件执行。无 AppId 时不要调用应用侧 MCP。

## 调用顺序

### P0（有 AppId 时尽量完成）

| 步骤 | MCP | 工具 | 用途 |
|------|-----|------|------|
| 1 | Captain | `get_application_info` | 中文名、Owner、admins、importance_level、组织/产线/产品、`ci_project_id`、`ci_repo` |
| 2 | — | 本地 `git remote -v` | GitLab 链接优先来源 |

`get_application_info` **成功**后才可写 Captain 应用链接，并采用返回的元数据填 1.1。

### P1（增强，失败可跳过）

| MCP | 工具 | 用途 |
|-----|------|------|
| Captain | `get_groups`（env: fat/uat/prod） | 部署分组概要 → 第 9 章 |
| QConfig | `list_envs` → `list_config_files` | 配置文件名清单 → 6.5（勿贴敏感值） |
| BAT | 先 `bat-global-helper`，再 `bat-dependency-daily` | 上下游依赖 → 6.4；限制深度与时间窗 |
| MOM | `get_project_list(term=appId 或 serviceCode)` | 契约线索；有明确 URL 才写 MOM 链接 |
| TestRun | `tc_query_job_by_app_id` | 自动化 Job → 第 10 章 |

### P2（按需）

| MCP | 工具 | 用途 |
|-----|------|------|
| MOM | `get_operation_list` / `get_operation_summary` | 契约 Operation 列表 → 6.6 |
| Framework | `framework-search` | 不明依赖/组件的官方语义 |
| Captain | `get_releases` | 仅用户关心近期发布时 |

### 禁止

- `deploy`、`changeqconfig`、`apply_data_auth` 及一切写/变更类工具
- 权限不足时反复重试同一失败调用
- 用猜测填 Owner、组织、链接

## 链接校验表（写入 1.2 前逐条过）

| 平台 | URL | 允许写入的条件 |
|------|-----|----------------|
| GitLab | `https://git.dev.sh.ctripcorp.com/{group}/{repo}` | ① `git remote` 为该 host 的 http(s)；或 ② Captain `ci_repo` 为 `git@git.dev.sh.ctripcorp.com:group/repo.git` 并按此转换。SSH 原文不得当作网页链接 |
| Captain 应用 | `https://captain.release.ctripcorp.com/app/{appId}` | `get_application_info` 成功且 id 一致 |
| Captain CI | `https://captain.release.ctripcorp.com/ci/project/{ci_project_id}/pipeline` | 返回了有效 `ci_project_id` |
| BAT | `http://bat.fx.ctripcorp.com/application/{appId}` | AppId 已由本地 `app.id` 确认，且 Captain 成功或用户确认该应用存在 |
| QConfig | （默认不写） | **仅当**已验证该应用深链格式，且 MCP 能访问该 appId 时；否则只在正文列文件名，不写链接行 |
| MOM | （仅明确 URL） | 接口/页面返回可打开的项目 URL，或用户提供且可解析；禁止瞎拼 |

门户级入口（如 QConfig 首页、SOA Portal 首页）**不要**当作「本项目链接」默认写入。

## GitLab 从 ci_repo 转换

```text
git@git.dev.sh.ctripcorp.com:framework/db-cluster.git
→ https://git.dev.sh.ctripcorp.com/framework/db-cluster
```

若 `ci_repo` host/路径无法识别，宁可不写 GitLab 行（或仅用已验证的 `git remote`）。

## 降级文案（附录 A 可用）

- 「Captain 查询失败/无权限，应用元数据与 Captain 链接已省略」
- 「无 AppId，已按二方库分析，跳过应用侧 MCP」
- 「QConfig 无权限，仅根据代码中的注解/本地配置描述」
