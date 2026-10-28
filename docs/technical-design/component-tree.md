[返回技术设计](../../technical-design.md) · [上一章：API 设计](api-design.md) · [下一章：边界与异常处理](edge-cases.md)

## 3. 组件树

### 3.1 页面结构与导航

| 页面 | 路径建议 | 主要跳转 |
|---|---|---|
| 登录 | `/login` | 登录后进入有权限的默认页面 |
| 员工与授权 | `/employees` | 员工详情、系统绑定、工具授权、凭据操作 |
| 业务系统 | `/systems` | 系统详情、鉴权配置、配置版本 |
| 接口与工具 | `/tools` | 草稿编辑、调试、版本比较、发布 |
| 调用日志 | `/logs` | 日志详情、关联确认记录 |
| 平台设置 | `/settings` | 后台账号、日志保留、人员脱敏及运行设置 |

详情页支持通过资源 ID 直接访问，但必须重新验证权限。普通员工没有后台页面。

### 3.2 组件层级

```text
AdminLayout
├── Header
│   └── AccountMenu
├── RoleNavigation
└── PageContent
    ├── EmployeesPage
    │   ├── EmployeeFilters
    │   ├── EmployeeTable
    │   └── EmployeeDetail
    │       ├── EmployeeProfileForm
    │       ├── SystemBindingEditor
    │       ├── ToolGrantEditor
    │       └── CredentialPanel
    ├── SystemsPage
    │   ├── SystemTable
    │   └── SystemDetail
    │       ├── SystemConfigEditor
    │       ├── AuthMappingEditor
    │       └── VersionHistory
    ├── ToolsPage
    │   ├── ToolFilters
    │   ├── ToolTable
    │   └── ToolDetail
    │       ├── InterfaceConfigEditor
    │       ├── ParameterSchemaEditor
    │       ├── ResponseMappingEditor
    │       ├── DebugPanel
    │       └── PublishReview
    ├── LogsPage
    │   ├── LogFilters
    │   ├── LogTable
    │   └── LogDetail
    └── SettingsPage
        ├── AdminAccountPanel
        ├── RetentionSettings
        ├── MaskingSettings
        └── RuntimeSettings
```

共用组件包括加载、空状态、字段错误、分页、冲突提示、版本差异和权限提示。

具体颜色、字体、尺寸、间距及移动端布局，待根目录 `UI_RULES.md` 补齐后确定。

### 3.3 状态管理方案

| 状态 | 存放位置 |
|---|---|
| 当前账号、角色、最小权限摘要 | React Context |
| 非敏感筛选、排序、分页 | URL 查询参数 |
| 表单内容、校验错误、未保存修改 | 页面局部状态，复杂表单使用 `useReducer` |
| 当前弹窗、选中行、调试展示状态 | 局部状态 |
| 密钥输入、首次展示的个人凭据 | 当前组件临时内存，关闭后清空 |
| 正式授权、配置版本、执行状态 | 服务端数据库 |

首版优先使用 React、Next.js 自身能力，不额外引入全局状态库或请求缓存库。

秘密不得进入 URL、全局状态、浏览器持久缓存或错误报告。

### 3.4 数据流方向

后台数据流：

**页面加载器／业务 Hook → 封装 API 层 → API Route → 服务层 → Prisma／业务系统调用器 → 安全 DTO → 页面组件。**

MCP 数据流：

**WorkBuddy → MCP 协议层 → 身份与权限检查 → 配置及映射层 → 系统鉴权 → 业务请求 → 结果过滤 → MCP 响应。**

组件不直接 `fetch`。角色导航只负责页面体验，服务端始终独立校验权限。

保存成功后使用服务器返回的新版本刷新页面；不能仅凭前端乐观状态认定授权或配置已经生效。
