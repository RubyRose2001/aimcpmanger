# Technical Design: 企业 MCP 集成管理平台

**版本：v1.0，设计确认版。**  
依据：[SPEC v1.1](spec-v1.1.md)及本次确认结果。

首版采用**一家公司独立部署一套产品**，面向约 200–500 名员工，由我方实施人员部署、维护。普通员工通过 WorkBuddy 使用工具，不提供个人后台；企业管理员与实施／运维人员使用管理后台。

技术栈为 Next.js、TypeScript、Tailwind CSS、Next.js API Routes、PostgreSQL、Prisma，使用 pnpm 管理依赖。

**非查询操作采用带前置条件的设计：WorkBuddy 的可信交互、签名来源和密钥登记机制验证通过前，保持禁止执行。** 本文不将尚未验证的客户端能力记为已支持。

## 文档目录

1. [数据模型](docs/technical-design/data-model.md)
2. [API 设计](docs/technical-design/api-design.md)
3. [组件树](docs/technical-design/component-tree.md)
4. [边界与异常处理](docs/technical-design/edge-cases.md)
5. [安全与性能](docs/technical-design/security-performance.md)
6. [实现顺序](docs/technical-design/implementation-order.md)

正文按项目“每个文件不超过 200 行”的要求分章保存，六章合计构成完整技术设计。

[文档交付检查](technical-design-review.md)记录本次文件清单、完整性检查及未解决事项。
