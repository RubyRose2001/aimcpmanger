# Project: 【企业mcp集成管理平台】

## 技术栈

### 后台端
- 前端：Next.js + TypeScript + Tailwind CSS
- 后端：Next.js API Routes
- 数据库：PostgreSQL + Prisma ORM
- 部署：私有话平台
- 包管理：pnpm

## 代码规范

### 后台端
- TypeScript 严格模式，禁用 any
- 函数式组件 + React Hooks
- 文件名 kebab-case（如 user-profile.tsx）
- 组件 PascalCase，工具函数 camelCase
- 每个文件 ≤ 200 行，超过则拆分
- 不在组件里直接 fetch，统一走封装的 API 层

## Git 提交规范
- 每次改动后必须创建 commit
- 格式：<type>(<scope>): <描述>
- type: feat / fix / refactor / docs / test / chore
- scope: 模块名（如 auth, payment, ui, schedule, member）
- 描述用中文，简洁明了
- 示例：feat(auth): 添加微信登录功能
- 一次提交只含一个功能；两端改动分开提交

## 测试要求
- 每次改动后编写或更新相关测试
- 后台端：Vitest + React Testing Library，覆盖率 ≥ 80%
- 小程序端：测试方案每次改动后自动生成
- 交付前确保所有测试通过

## 约束条件
- 不直接操作生产数据库
- 不在代码中硬编码 API Key 或密钥
- 不引入未经评估的新依赖，不擅自改依赖版本号
- 不改已有功能的逻辑，除非 SPEC 明确要求
- 不改已有文件的命名和路径
- 数据层沿用现有集合与云函数；需新增字段必须先输出计划确认，
  不允许新建集合或云函数
- 每次改动前先输出改动计划，确认后再执行

## 安全红线
- 用户输入必须做 XSS 过滤
- SQL 查询必须参数化（Prisma 默认满足）
- 不在前端暴露敏感信息
- 文件上传必须校验类型和大小

## UI 规则
做任何 UI / 样式相关改动前，先读项目根目录的 UI_RULES.md，
逐条照它执行。数值一律以 UI_RULES.md 为准，
AGENTS.md 不另立一套数值。

## 验收标准
1. 逐条自查约束，列出每条对应的改动位置
2. 用 Playwright 打开真实页面，桌面端与移动端各按同一尺寸截图，
   与改前逐项比对，不一致继续迭代直到视觉一致
3. 输出：改动文件清单 + 引用变量清单 + 未解决缺失 token 说明
