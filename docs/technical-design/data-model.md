[返回技术设计](../../technical-design.md) · [下一章：API 设计](api-design.md)

## 1. 数据模型

### 1.1 通用约定

数据库使用关系表，从零设计。数据库字段采用 `snake_case`，API 字段采用 `camelCase`。

| 项目 | 类型及约束 | 默认值 |
|---|---|---|
| 主键 `id` | UUID，服务端生成，不使用姓名或 OA 登录名作为主键 | 新 UUID |
| 公司归属 `company_id` | UUID，关联公司配置，由服务端确定 | 当前部署的公司 |
| 创建时间 `created_at` | `timestamptz`，统一保存 UTC | 当前时间 |
| 更新时间 `updated_at` | 可编辑记录使用 `timestamptz` | 当前时间，修改时更新 |
| 编辑版本 `revision` | 可编辑主数据、草稿使用正整数 | `1` |
| 可选字段 | 以下标记为 `?` 的字段允许为空 | `NULL` |
| 未注明默认值的必填字段 | 创建或发布时必须提供 | 无默认值 |

公司配置只允许一条。其他业务记录仍保存公司归属，并使用外键、唯一约束及服务端检查防止错误关联。

员工、系统、工具、后台账号采用停用方式保留历史关联。业务删除或写入只能通过获授权的业务系统 API 完成。

秘密字段采用加密封装，包含密文、密钥版本、随机 nonce 和认证标签；数据库不保存其明文。

### 1.2 公司、后台账号与员工

以下各表除特别说明外，均包含适用的通用字段。

| 表名 | 字段列表及类型 | 约束与默认值 |
|---|---|---|
| `company_settings` | `name varchar(128)`；`log_retention_days int`；`person_masking_rules jsonb`；`runtime_limits jsonb` | 单条公司配置；保留天数默认 `30`、必须大于零；人员脱敏规则默认 `{}`；运行参数见第 5 节 |
| `admin_accounts` | `username varchar(64)`；`password_hash text`；`role enum`；`status enum`；`must_change_password boolean` | 规范化用户名在公司内唯一；角色为 `ADMIN` 或 `OPERATOR`；状态默认 `ACTIVE`；管理员代建账号默认要求首次改密 |
| `admin_sessions` | `account_id uuid`；`token_hash bytea`；`last_seen_at timestamptz`；`absolute_expires_at timestamptz`；`revoked_at timestamptz?` | 会话摘要唯一；仅保存摘要；空闲 30 分钟或创建满 8 小时失效 |
| `employees` | `employee_no varchar(64)`；`name varchar(128)`；`department varchar(128)?`；`email varchar(254)?`；`status enum`；`grants_revision int` | 员工编号在公司内唯一，姓名可重复；状态默认 `ACTIVE`；授权集合编辑版本默认 `1` |
| `mcp_credentials` | `employee_id uuid`；`token_hash bytea`；`display_prefix varchar(16)`；`issued_by uuid`；`revoked_at timestamptz?` | 凭据摘要唯一；首版不自动过期；原文仅签发响应展示一次 |

补充规则：

- 普通员工记录没有后台密码。
- 管理员需要使用 MCP 时，另建员工记录并单独授权。
- 员工编号用于人工管理，内部 UUID 用于关联；修改姓名或编号不改变历史归属。
- 员工停用后，其全部 MCP 凭据立即失去调用资格。
- MCP 凭据吊销影响该凭据；员工停用影响该员工的所有凭据。

### 1.3 业务系统、人员绑定与接口配置

| 表名 | 字段列表及类型 | 约束与默认值 |
|---|---|---|
| `business_systems` | `name varchar(128)`；`status enum`；`current_version_id uuid?`；`credentials_encrypted jsonb?`；`credentials_revision int` | 默认 `DISABLED`；凭据修订号默认 `1`；当前版本必须属于本系统且已发布 |
| `system_versions` | `system_id uuid`；`version_no int`；`state enum`；`base_url text`；`employee_fields_schema jsonb`；`auth_definition jsonb`；`published_by uuid?`；`published_at timestamptz?` | 系统与版本号组合唯一；状态为 `DRAFT/PUBLISHED`；每系统最多一个草稿；发布后不可修改 |
| `employee_system_bindings` | `employee_id uuid`；`system_id uuid`；`field_values jsonb`；`secrets_encrypted jsonb?`；`validated_system_version_id uuid?`；`status enum` | 员工与系统组合唯一；普通字段默认 `{}`；状态为 `ACTIVE/NEEDS_REVIEW/DISABLED`；未经校验不能调用 |
| `tool_categories` | `name varchar(64)`；`sort_order int`；`status enum` | 公司内分类名称唯一；排序默认 `0`；初始化提供默认分类 |
| `tools` | `system_id uuid`；`category_id uuid`；`name varchar(128)`；`status enum`；`current_version_id uuid?` | 工具名称在公司内唯一，发布后保持稳定；默认 `DISABLED` |
| `tool_versions` | `tool_id uuid`；`version_no int`；`state enum`；`description text`；`http_method enum`；`relative_path text`；`input_schema jsonb`；`output_schema jsonb`；`request_mapping jsonb`；`response_mapping jsonb`；`error_mapping jsonb`；`operation_type enum`；`timeout_ms int`；发布人及时间 | 工具与版本号组合唯一；每工具最多一个草稿；操作性质默认 `NON_QUERY`；超时默认 `20000` 毫秒；发布后不可修改 |

配置结构遵循以下规则：

- 公司私钥、应用密钥等放入对应业务系统的秘密配置。
- OA 登录名等普通人员字段放入员工与系统绑定。
- 创建员工时可同时填写已有系统要求的人员字段，后续由实施／运维人员维护。
- 人员字段定义包含名称、类型、必填性、校验规则及是否属于秘密。
- 请求映射只允许引用公司配置、当前员工绑定、已校验的业务参数及受控鉴权结果。
- 支持路径、查询参数、请求头、JSON 请求体、嵌套对象与数组。
- 禁止任意脚本、动态执行代码，以及由 Agent 覆盖基础地址、身份字段或鉴权头。
- 操作性质根据真实副作用判定，不能根据 GET、POST 判定；不确定时按非查询处理。

鉴权定义描述受控鉴权请求、字段映射、结果提取和业务错误映射。**OA 的实际鉴权路径、Token 生命周期及身份语义必须依据真实文档和联调结果配置，本文不预设其具体结构。**

系统发布新版本时，必须检查现有工具映射及员工绑定是否兼容。身份字段含义或必填结构发生不兼容变化时，相关绑定标记为 `NEEDS_REVIEW`，完成维护和验证后才能恢复调用。

### 1.4 授权、确认与审计

| 表名 | 字段列表及类型 | 约束与默认值 |
|---|---|---|
| `employee_tool_grants` | `employee_id uuid`；`tool_id uuid`；`granted_by uuid`；`granted_at timestamptz` | 员工与工具组合唯一；无岗位、无隐式继承授权 |
| `debug_identity_grants` | `account_id uuid`；`employee_id uuid`；`tool_id uuid`；`approved_revision int`；`config_hash bytea`；`expires_at timestamptz`；`status enum` | 调试权限绑定获准人员及具体配置范围；配置发生执行相关变化后重新核验 |
| `operation_confirmations` | 员工、系统、工具、绑定及配置版本引用；`grant_id_snapshot uuid`；`parameters_encrypted jsonb`；`parameters_hash bytea`；`code_hmac bytea?`；`attempt_count int`；`expires_at timestamptz`；`status enum`；领取、完成、核查及保留截止时间 | 默认 `PENDING`；错误次数默认 `0`；有效期 5 分钟；执行参数只能取自服务端快照 |
| `audit_events` | `event_type enum`；`admin_id uuid?`；`employee_id uuid?`；`system_id uuid?`；`tool_id uuid?`；`request_id uuid`；`confirmation_id uuid?`；`status enum`；`duration_ms int?`；`error_code text?`；`sanitized_payload jsonb?` | 服务端生成；覆盖调用、拒绝、管理、登录和确认事件；不保存秘密明文 |
| `management_requests` | `actor_id uuid`；`action text`；`idempotency_key text`；`request_hash bytea`；`status enum`；`resource_id uuid?` | 操作者、操作类型和去重键组合唯一；保存结果引用，不保存可回读的凭据原文 |

确认记录还包含：

- 当前业务系统凭据修订号、人员绑定修订号。
- 当前工具授权记录标识。
- 来源为 MCP 调用还是后台调试。
- 结果摘要、核查人、核查时间、证据引用及核查结论。
- 可信确认凭证的校验摘要、一次性标识；不记录验证码明文。

`grants_revision` 只用于后台授权编辑冲突检查。更新授权集合时计算增删差异，保留未变化授权的记录标识，避免撤销一个工具影响其他工具。

### 1.5 关联关系

| 关系 | 类型 |
|---|---|
| 后台账号 → 登录会话 | 一对多 |
| 员工 → MCP 凭据 | 一对多 |
| 员工 ↔ 业务系统 | 通过人员绑定构成多对多 |
| 业务系统 → 系统配置版本 | 一对多 |
| 业务系统 → 工具 | 一对多 |
| 分类 → 工具 | 一对多 |
| 工具 → 工具配置版本 | 一对多 |
| 员工 ↔ 工具 | 通过逐人工具授权构成多对多 |
| 员工／工具 → 确认记录 | 一对多 |
| 确认记录 → 审计事件 | 一对多 |

当前版本引用、人员绑定和授权必须指向同公司下的正确资源。必要的复合外键在数据库层约束，不能只靠页面选择器限制。

### 1.6 索引设计

| 索引 | 用途 |
|---|---|
| 公司内用户名、员工编号、工具名、分类名称唯一索引 | 防止重复创建 |
| 员工与系统、员工与工具组合唯一索引 | 防止重复绑定、重复授权 |
| 系统／工具与版本号组合唯一索引 | 保证版本唯一 |
| 每个系统／工具仅一个草稿的部分唯一索引 | 避免出现并行草稿 |
| 会话摘要、MCP 凭据摘要唯一索引 | 快速鉴权和查重 |
| 授权表 `(tool_id, employee_id)` | 查询工具被哪些员工使用 |
| 日志 `(company_id, created_at, id)` | 时间范围查询和游标分页 |
| 日志 `request_id`、`confirmation_id` | 追踪调用链及确认流程 |
| 确认记录 `(employee_id, status, created_at)` | 查询员工的待确认和未知结果 |
| 确认记录 `(status, expires_at)`、`retention_until` | 到期处理和清理 |
| 管理请求的去重组合索引 | 阻止重复创建和签发 |

JSON 字段默认不建立整体索引；只有实际查询证明需要时，才增加特定字段索引。
