# {项目名} — AI Coding 工程化入口

> 本文件为 AI 编码工具在本项目中工作时的总入口。
> 所有 AI 在生成、修改代码前 **必须先阅读本文件**，再按"文档路由"进入对应规则。

---

## 一、项目定位

- **项目名**：{项目名}
- **业务域**：{一句话描述项目所在的业务领域}
- **职责**：{项目负责什么}
- **不负责**：{项目不负责什么，边界在哪}

## 二、技术栈速览

{根据 Phase 0 扫描结果填写，例如：}
- {语言}、{框架}、{中间件}
- {数据库} + {分片/集群策略}
- {其他关键技术}

## 三、AI 修改代码前必读路由

| 任务类型 | 必读文档 |
|---------|---------|
| {模块A} 相关代码 | `docs/ai-context/04-business-rules/{module-a}-rules.md` |
| {模块B} 相关代码 | `docs/ai-context/04-business-rules/{module-b}-rules.md` |
| 新增/修改 Service | `docs/ai-context/08-code-style/service-layer-guide.md` |
| 查术语/概念 | `docs/ai-context/GLOSSARY.md` |
| 了解项目全貌 | `docs/ai-context/01-project-overview/README.md` |
| 查数据库表 | `docs/ai-context/06-database/README.md` |
| 提交前自检 | `docs/ai-context/11-review-checklists/ai-generated-code-checklist.md` |

## 四、全局禁止事项（任何模块都不可违反）

{根据 Phase 2-3 扫描结果提取全局性禁止事项，例如：}
1. **禁止虚构**：禁止生成项目中不存在的类、方法、字段、枚举值。修改前必须先确认。
2. **禁止跨租户**：{如有多租户，必须带租户条件}
3. **禁止引入新依赖**：不在依赖文件中新增依赖（除非用户明确要求）。
4. **禁止顺手重构**：只改本次需求相关的代码。

## 五、代码分层结构（速查）

{根据 Phase 0 识别的分层模式填写}

| 层 | 路径 | 核心规则 |
|----|------|---------|
| Controller | `{路径}` | {规则} |
| Service | `{路径}` | {规则} |
| Repository | `{路径}` | {规则} |
| Model | `{路径}` | {规则} |
