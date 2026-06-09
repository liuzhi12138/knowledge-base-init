---
name: knowledge-base-init
description: Generate a complete AI Coding knowledge base for any project. Scans source code, extracts business rules, creates structured documentation with built-in quality verification. Use when the user wants to create, bootstrap, or initialize an AI context knowledge base for a project, or mentions "知识库", "AI context", "knowledge base".
---

# Knowledge Base Init

为任意项目生成结构化的 AI Coding 知识库。通过源码扫描 + 五层质疑机制，产出可供 AI 编码助手使用的全套上下文文档。

## 核心原则

1. **证据驱动**：每条事实必须有源码引用，禁止凭空推断
2. **增量写入**：边扫边写，不等不攒，防止中断丢失
3. **主动质疑**：每个关键节点自动执行质疑步骤（Q1-Q5）
4. **人工兜底**：业务规则必须经用户确认，AI 只提候选项
5. **断点续传**：通过 manifest.json 跟踪进度，支持跨 session 恢复
6. **低侵入性**：知识库不占用项目的 AGENTS.md 和 docs/ 目录，独立入口、独立存储、一行引用即可拆卸

## 异常场景处理

在 Phase 0 扫描时，如果遇到以下情况，按对应策略处理：

| 场景 | 识别方式 | 处理策略 |
|------|---------|----------|
| **空项目/脚手架项目** | 源码文件 < 5 个 | 告知用户："项目源码不足，建议有业务代码后再生成知识库"，停止执行 |
| **无源码目录** | 找不到 src/、app/、lib/ 等常见源码目录 | 列出找到的所有目录，请用户指定源码位置 |
| **纯前端项目** | 只有 HTML/CSS/JS，无后端逻辑 | 正常执行，但跳过 Phase 3 的 Service/Repository 层扫描，聚焦组件/路由/状态管理 |
| **Monorepo 多包** | 根目录含 packages/、apps/ 等子项目目录 | 请用户选择要生成知识库的子项目，每个子项目独立执行 |
| **已有知识库** | 检测到 `AGENTS_KB_{项目名}.md` 或 `docs_kb_{项目名}/` 已存在 | 展示已有内容摘要，询问用户：覆盖重来 / 增量追加 / 取消 |
| **已有 AGENTS.md** | 根目录存在 AGENTS.md | 不覆盖，仅在末尾追加一行知识库引用（`## 知识库` + 入口路径） |
| **无 AGENTS.md** | 根目录不存在 AGENTS.md | 创建最小化入口文件，仅含一行知识库引用 + 一行项目说明，不填充其他内容 |

## 执行流程

### Phase 0: 项目画像（必须先完成）

**目标**：识别技术栈、分层模式、模块边界，让用户确认后再继续。

```
Task Progress:
- [ ] Step 1: 识别技术栈
- [ ] Step 2: 识别分层模式
- [ ] Step 3: 识别模块边界
- [ ] Step 4: 用户确认项目画像
- [ ] Step 5: 工作量预估
- [ ] Step 6: 初始化 manifest.json
```

**Step 1 - 识别技术栈**

扫描项目依赖文件，判断技术栈：

| 文件 | 技术栈 |
|------|--------|
| `pom.xml` / `build.gradle` | Java |
| `go.mod` | Go |
| `package.json` | Node.js/TypeScript |
| `requirements.txt` / `pyproject.toml` | Python |
| `Cargo.toml` | Rust |

同时识别框架（Spring Boot / Gin / Express / Django 等）。

**Step 2 - 识别分层模式**

扫描顶层目录结构，判断项目的代码分层：
- 找出 Controller/Handler/Route 层
- 找出 Service/UseCase 层
- 找出 Repository/DAO/Store 层
- 找出 Model/Entity/Schema 层

**Step 3 - 识别模块边界**

按目录结构或包名识别业务模块。列出发现的模块清单。

**Step 4 - 用户确认**

向用户展示项目画像摘要，要求确认：
1. 技术栈是否正确
2. 分层是否正确
3. 模块划分是否正确
4. 哪些模块需要**完整深度**扫描，哪些只需**骨架级**扫描
5. 知识库输出语言（默认中文，可选英文）

**Step 5 - 工作量预估**

根据扫描结果输出预估：

```
本项目知识库生成预估：
- 源码文件总数: X 个
- 识别模块数: Y 个（full: N 个, skeleton: M 个）
- 预计对话轮次: 约 Z 轮（每轮处理 1 个模块或 1 个 Phase）
- 预计产出文档: W 个文件
```

**Step 6 - 初始化 manifest.json**

在项目根目录创建 `.knowledge-base-init/manifest.json`。完整 schema：

```json
{
  "project": "project-name",
  "techStack": "Java/Spring Boot",
  "framework": "Spring Boot 2.3",
  "language": "zh-CN",
  "createdAt": "2026-06-03",
  "totalSourceFiles": 247,
  "estimatedRounds": 15,
  "phases": {
    "phase-0": { "status": "complete", "completedAt": "2026-06-03T10:00:00" },
    "phase-1": { "status": "complete", "completedAt": "2026-06-03T10:05:00" },
    "phase-2": {
      "status": "in-progress",
      "docs": {
        "01-project-overview": { "status": "complete", "output": "docs_kb_project-name/01-project-overview/README.md" },
        "06-database": { "status": "pending" }
      }
    },
    "phase-3": {
      "status": "pending",
      "modules": {
        "inventory": {
          "depth": "full",
          "status": "pending",
          "totalFiles": 47,
          "batchSize": 15,
          "currentBatch": 0,
          "subtasks": [
            { "id": "scan-service", "status": "pending", "files": 12 },
            { "id": "scan-repository", "status": "pending", "files": 8 },
            { "id": "generate-source-facts", "status": "pending" },
            { "id": "generate-rules", "status": "pending" },
            { "id": "q1-q5-challenges", "status": "pending" },
            { "id": "user-confirm", "status": "pending" }
          ]
        },
        "price": { "depth": "skeleton", "status": "pending" }
      }
    },
    "phase-4": { "status": "pending" },
    "phase-5": { "status": "pending" }
  }
}
```

每次开始工作时，先读 manifest.json 确认当前进度。每完成一个子任务，立即更新 manifest.json。

---

### Phase 1: 骨架生成（全自动）

**目标**：生成知识库目录结构、导航文件和规则模板。

```
Task Progress:
- [ ] 创建 docs_kb_{项目名}/ 目录结构
- [ ] 生成 00-index.md（导航索引）— 参照 assets/templates/index-template.md
- [ ] 生成 AGENTS_KB_{项目名}.md（知识库入口文件）— 参照 assets/templates/agents-kb-template.md
- [ ] 处理 AGENTS.md（已有则追加引用，无则创建最小化入口）
- [ ] 生成 _TEMPLATE.md（业务规则模板）— 参照 assets/templates/business-rules-template.md
```

**AGENTS.md 处理规则**：

知识库不占用 AGENTS.md。仅在 AGENTS.md 中追加一行引用，作为 AI Agent 发现知识库的桥梁：

```markdown
## 知识库
修改代码前，必须先阅读 `AGENTS_KB_{项目名}.md`。
```

- 已有 AGENTS.md：在末尾追加上述引用，不修改任何现有内容
- 无 AGENTS.md：创建最小化文件，仅含标题 + 知识库引用，不填充其他内容

按需生成目录（根据项目特征决定是否需要）：

| 目录 | 生成条件 |
|------|--------|
| `01-project-overview/` | 始终生成 |
| `02-architecture/` | 始终生成 |
| `03-domain-model/` | 有明确领域模型时 |
| `04-business-rules/` | 始终生成 |
| `05-status-flow/` | 有状态机/枚举时 |
| `06-database/` | 有数据库时 |
| `07-api/` | 有 API 端点时 |
| `08-code-style/` | 始终生成 |
| `09-testing/` | 有测试文件时 |
| `11-review-checklists/` | 始终生成 |

模板参考: [assets/templates/](assets/templates/)

---

### Phase 2: 基础设施文档（全自动 + 审核）

**目标**：扫描生成项目概览、架构、数据库、API 等基础设施文档。

```
Task Progress:
- [ ] 生成 01-project-overview/README.md
- [ ] 生成 02-architecture/README.md
- [ ] 生成 06-database/README.md（如有）
- [ ] 生成 05-status-flow/README.md（如有）
- [ ] 生成 07-api/README.md（如有）
- [ ] 生成 03-domain-model/README.md（如有）
- [ ] 执行 Q1 质疑
- [ ] 展示摘要，用户审核
```

**各文档扫描目标与输出格式**：

| 文档 | 扫描什么 | 怎么找 | 输出什么 |
|------|---------|--------|----------|
| project-overview | 项目定位、核心能力、服务端类型 | README.md + 主入口文件 + Controller 包名 | 一句话定位 + 能力域表格 + 端类型表格 |
| architecture | 技术栈、模块划分、依赖拓扑 | pom.xml/go.mod/package.json + 顶层目录 | 技术栈表格 + 模块-基础设施映射表 |
| database | 数据库表、字段、索引 | DDL 文件 / ORM Model / Migration / Mapper XML | 按模块分组的表清单 + 核心字段表 |
| status-flow | 状态枚举、状态机 | enum 类 / const block / 枚举值定义 | 按模块分组的枚举清单 + 状态流转图 |
| api | API 端点、入参、出参 | Controller/Handler/Route 文件 | 按模块分组的端点清单（HTTP方法+路径+方法名） |
| domain-model | 聚合根、实体、值对象 | Model/Entity 目录 + 关联关系 | 按模块分组的聚合根清单 + 核心字段 |

每个文档生成后，立即执行 **Q1 扫描完整性自证**。
全部完成后，展示摘要让用户审核。

---

### Phase 3: 业务规则深度挖掘（逐模块，需人工确认）

**目标**：对每个选中的模块，生成四件套文档。

这是最耗时的阶段，按模块逐个执行。每个模块的流程：

```
对模块 X:
- [ ] 扫描文件清单 → Q1
- [ ] 逐层读取源码 → Q2（每层）
- [ ] 提取业务规则候选 → Q3（每条）
- [ ] 生成 source-facts.md
- [ ] 生成 legacy-and-side-paths.md
- [ ] 生成 governance-gaps.md
- [ ] 生成 {module}-rules.md
- [ ] 执行 Q4 遗漏审计
- [ ] 执行 Q5 置信度标注
- [ ] 展示候选项，用户逐条确认
- [ ] 更新 manifest.json
```

**骨架级 vs 完整深度**：

| 深度 | 产出 | 适用场景 |
|------|------|----------|
| **完整深度** | 四件套全生成（rules + source-facts + legacy + governance），Q1-Q5 全执行 | 核心业务模块 |
| **骨架级** | 只生成 rules.md 的 TL;DR + 核心入口速查 + 禁止事项，只执行 Q1（扫描完整性），跳过 Q2-Q5 | 辅助/简单模块 |

**关键规则**：
- 每扫描完一个子层（如 Service 层），立即将发现追加写入 source-facts.md
- 不要等全部扫完再写，防止中断丢失
- 业务规则候选项必须展示给用户确认后才能写入 rules.md
- source-facts.md 和 governance-gaps.md 可以自动写入
- legacy-and-side-paths.md 可以自动写入（基于反例搜索结果）

用户确认时的分类：
- **[确认]** → 写入 rules.md
- **[推断]** → 展示给用户，确认后写入
- **[猜测]** → 展示给用户，确认后写入，标注"待验证"

---

### Phase 4: 规范与清单（模板化 + 适配）

**目标**：生成编码规范、测试规范、审查清单、术语表。

```
Task Progress:
- [ ] 生成 08-code-style/service-layer-guide.md
- [ ] 生成 11-review-checklists/（通用 + 每模块）
- [ ] 生成 GLOSSARY.md
- [ ] 展示摘要，用户审核
```

**各文档扫描目标**：

| 文档 | 扫描什么 | 输出什么 |
|------|---------|----------|
| service-layer-guide | 现有 Service 代码的分层模式、命名惯例、事务使用 | 分层规范 + 命名规范 + 事务规范 |
| review-checklists | 各模块的禁止事项 + 常见陷阱（从 Phase 3 提取） | 通用清单 + 每模块专项清单 |
| GLOSSARY | 各模块术语 + 业务关键词路由（从 Phase 2-3 提取） | 术语表 + 关键词路由表 + 已知拼写不一致 |

---

### Phase 5: 控制面收尾（全自动）

**目标**：更新索引、生成维护协议、执行最终校验。

```
Task Progress:
- [ ] 更新 00-index.md（任务路由表）
- [ ] 更新 AGENTS_KB_{项目名}.md（文档路由表）
- [ ] 更新 AGENTS.md 中的知识库引用（确保指向正确）
- [ ] 生成 knowledge-base-protocol.md（维护协议）
- [ ] 生成最终覆盖率报告
- [ ] 清理 .knowledge-base-init/（保留 manifest 备查）
```

---

## 五层质疑体系

在每个关键节点**自动执行**，不需要用户触发。所有质疑回答写入 `.knowledge-base-init/challenge-log.md`。

### Q1: 扫描完整性自证

**触发时机**：每个模块/层的文件扫描完成后。

执行：
1. 用 `find` 命令统计该模块下所有源码文件的精确数量（**根据 Phase 0 识别的技术栈调整搜索路径**，如 Java 搜 `src/main/java`、Go 搜项目根目录、Python 搜 `app/` 或 `src/`、Node 搜 `src/` 或 `app/`）
2. 列出你实际读取过的文件路径清单
3. 两者是否一致？如果不一致，哪些没读？为什么？

输出格式：
```
[Q1] 模块 X - Service 层
文件总数 (find): 12
已读文件: 12
覆盖率: 100%
未读文件: 无
```

### Q2: 阅读理解自证

**触发时机**：每个文件的提取完成后。

执行：
1. 这个文件有多少行？
2. 列出文件中所有 public 方法/导出函数的完整签名（含参数和返回类型）
3. 对每个方法，用一句话说明它做了什么
4. 是否有你无法理解或无法归类的代码块？如果有，指出来

**第 4 点是关键**：主动承认不理解的部分，比假装理解更有价值。

**简化规则**：以下类型的文件可跳过 Q2 的完整方法列举，只做简要记录：
- 文件行数 < 50 行
- 纯 DTO/VO/常量定义/配置文件（无业务逻辑）
- 纯工具类（无业务判断，只是格式转换/字符串处理）

简化记录格式：
```
[Q2-skip] XxxDTO.java (32行) — 纯 DTO，含 8 个字段，无业务逻辑
```

### Q3: 业务规则溯源

**触发时机**：每条业务规则写入 rules.md 前。

执行：
1. 这条规则来自哪几行代码？给出文件路径和行号
2. 是否有违反这条规则的代码？用 grep 搜索
3. 如果找到了违反的代码，说明为什么你仍认为这是规则而非例外

### Q4: 遗漏审计

**触发时机**：每个模块的全部文档生成后。

执行：
1. 扫描清单中每个文件是否都在 source-facts 中有对应记录？
2. 该模块的数据访问层（Mapper/DAO/Repository）是否有未提及的操作？
3. 该模块的测试文件是否覆盖了 source-facts 中未记录的场景？
4. 该模块是否有事件/消息/订阅，未在文档中记录？

### Q5: 置信度标注

**触发时机**：每个模块的全部文档生成后。

对每条事实和规则标注置信度：
- **[确认]** 有明确源码证据，且无反例
- **[推断]** 从代码模式推断，但无法 100% 确认
- **[猜测]** 基于命名/注释推测，未深入验证

将所有 [推断] 和 [猜测] 的条目单独列出，提交给用户复核。

---

## 执行保障机制

### manifest.json

完整 schema 见 Phase 0 Step 6。每次开始工作时，先读 manifest.json 确认当前进度。每完成一个子任务，立即更新 manifest.json。

### 增量写入

禁止“读完全部再一次性输出”的模式。正确做法：

```
读完 Service 层 → 立即追加写入 source-facts.md §Service 章节
读完 Repository 层 → 立即追加写入 source-facts.md §Repository 章节
读完 Processor 层 → 立即追加写入 source-facts.md §Processor 章节
```

### 分批策略（大项目保护）

当单个模块的源码文件数 > 15 时，必须拆分为子批次执行：

1. 按代码层级拆分（Service 一批、Repository 一批、其他一批）
2. 每批最多 15 个文件
3. 每批完成后立即增量写入 + 更新 manifest.json
4. 所有批次完成后合并执行 Q4 和 Q5

```
模块 X（47 个文件）:
  Batch 1: Service 层 (12 files) → 扫描 → Q2 → 写入 source-facts §Service
  Batch 2: Repository 层 (8 files) → 扫描 → Q2 → 写入 source-facts §Repository
  Batch 3: Controller + Enum (10 files) → 扫描 → Q2 → 写入
  Batch 4: Model + Others (17 files) → 拆分为两个子批次
  → Q4 遗漏审计（全模块）
  → Q5 置信度标注（全模块）
```

### 阶段检查点

每个 Phase 完成后，输出检查点摘要：

| 检查项 | 结果 |
|---------|------|
| 已生成文件 | [文件列表] |
| 文件覆盖率 | [统计数字] |
| Q1 扫描完整性 | pass/fail |
| Q2 阅读理解 | pass/fail |
| Q3 规则溯源 | pass/fail |
| Q4 遗漏审计 | pass/fail |
| Q5 置信度标注 | pass/fail |
| 下一步 | Phase N+1 描述 |

等待用户确认：▶ 确认后继续 / 修改后继续 / 暂停

---

## 交互设计

本 Skill 有 3 个强制交互点：

| 交互点 | 时机 | 目的 |
|--------|------|------|
| 项目画像确认 | Phase 0 结束后 | 确认技术栈、分层、模块划分 |
| 业务规则确认 | 每个模块 Phase 3 结束后 | 逐条确认业务规则候选项 |
| 阶段检查点 | 每个 Phase 结束后 | 确认进度和质量 |

其余步骤全自动执行，不打断用户。

---

## 恢复执行

如果用户在新 session 中说"继续生成知识库"：

1. 读取 `.knowledge-base-init/manifest.json`
2. 找到第一个 status 不是 "complete" 的 phase
3. 从该 phase 的第一个 pending 子任务开始继续
4. 展示恢复摘要："上次完成到 Phase N 的 XXX，现在从 YYY 继续"

---

## 反例校验（强制步骤）

对每条推断的业务规则，必须搜索是否存在违反该规则的代码：
- 找到违反 → 标记为 legacy-and-side-paths
- 未找到违反 → 规则可信度升级为 [确认]
- 搜索范围不足 → 规则标记为 [待验证]

这不是可选步骤，是 Q3 质疑的一部分。

---

## 补充资源

- 文档模板: [assets/templates/](assets/templates/)
- 质量样例: [assets/examples.md](assets/examples.md)
