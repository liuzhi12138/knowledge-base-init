# knowledge-base-init

![License](https://img.shields.io/github/license/liuzhi12138/knowledge-base-init)
![Version](https://img.shields.io/badge/version-1.0.0-blue)
![Status](https://img.shields.io/badge/status-stable-green)

> **AI Agent 技能** — 为任意项目生成完整的 AI 编程知识库，让 AI 编码助手不再"凭直觉写代码"。

扫描源码 → 提取业务规则 → 生成结构化文档 → 内置五层质量验证。
产出的知识库可被 Qoder、Codex、Cursor 等 AI Agent 直接消费，建立对陌生代码库的深度上下文理解。

---

## 为什么需要它？

AI 编码助手在处理复杂业务代码时，最常犯的错误是：**编造不存在的类、忽略隐含的业务约束、遗漏关键的事务边界**。

根本原因是缺少项目级的上下文文档。本技能通过自动化扫描 + 五层质疑机制，一次性生成全套结构化知识库，让 AI 在每次处理业务代码前都能"读完全书再下笔"。

## 低侵入性设计

知识库**不占用**项目的 `AGENTS.md` 和 `docs/` 目录。对项目的影响仅限一行：

```markdown
## 知识库
处理任何涉及业务代码的任务前，必须先阅读 `AGENTS_KB_{项目名}.md`。
```

不需要时，删掉这一行 + 删除 `AGENTS_KB_*.md` 和 `docs_kb_{项目名}/`（含元数据和数据文件）即可完全移除。

| | 其他方案 | 本技能 |
|---|---------|--------|
| **入口文件** | 直接覆盖 AGENTS.md | 独立 `AGENTS_KB_{项目名}.md`，AGENTS.md 仅加一行引用 |
| **文档目录** | 占用 `docs/ai-context/` | 独立 `docs_kb_{项目名}/`，不与其他文档冲突 |
| **停用成本** | 需删多个文件，清理路由表 | 删一行引用 + 两个目录，改动极小 |
| **多知识库共存** | 不可能 | 可以，每个知识库独立入口、独立目录 |

## 核心能力

| 能力 | 说明 |
|------|------|
| **证据驱动** | 每条事实必须引用源码路径和行号，禁止凭空推断 |
| **五层质疑** | 每个关键节点自动执行 Q1–Q5 质疑，确保产出质量（见下表） |
| **增量写入** | 边扫边写，防止中断丢失已有成果 |
| **断点续传** | 通过 `manifest.json` 跟踪进度，支持跨 session 恢复 |
| **人工兜底** | 业务规则必须经用户逐条确认，AI 只提候选项 |
| **反例校验** | 每条推断规则强制搜索反例，发现例外自动归档为历史旁路 |

### 五层质疑体系

| 层级 | 名称 | 触发时机 | 做什么 |
|:----:|------|---------|--------|
| Q1 | 扫描完整性自证 | 每层文件扫描后 | 用 `find` 统计文件总数，与已读清单比对，确保 100% 覆盖 |
| Q2 | 阅读理解自证 | 每个文件提取后 | 列出所有 public 方法签名及用途，主动报告不理解的代码 |
| Q3 | 业务规则溯源 | 每条规则写入前 | 给出源码行号，搜索是否有违反该规则的代码 |
| Q4 | 遗漏审计 | 模块文档生成后 | 检查 DAO / 测试 / 事件 / 消息是否全部记录在案 |
| Q5 | 置信度标注 | 模块文档生成后 | 对每条事实标注 `[确认]` `[推断]` `[猜测]`，提交用户复核 |

## Quick Start

### 安装

```bash
# 方式一：通过 GitHub clone 到 Agent 的 skills 目录
git clone https://github.com/liuzhi12138/knowledge-base-init.git ~/.qoder/skills/personal/knowledge-base-init

# 方式二：直接将整个项目目录复制到 skills 目录下
cp -r knowledge-base-init ~/.qoder/skills/personal/knowledge-base-init
```

### 使用

在你的项目根目录，对 AI Agent 说：

```
帮我生成知识库
```

AI 将自动进入六阶段工作流：

```
Phase 0  项目画像    → 识别技术栈、分层模式、模块边界，请你确认
Phase 1  骨架生成    → 创建 docs_kb_{项目名}/ 容器和 context/ 数据目录、知识库入口文件和规则模板
Phase 2  基础设施文档 → 生成项目概览、架构、数据库、API 等文档
Phase 3  业务规则挖掘 → 逐模块生成四件套（rules + source-facts + legacy + governance）
Phase 4  规范与清单   → 生成编码规范、审查清单、术语表
Phase 5  收尾校验    → 更新索引、生成维护协议、输出覆盖率报告
```

### 预期产出

```
AGENTS.md                               # 项目原有入口（仅追加一行知识库引用）
AGENTS_KB_{项目名}.md                   # 知识库入口文件（AI 生成，请勿手动编辑）
docs_kb_{项目名}/                      # 知识库容器
├── .knowledge-base-init/               # Skill 元数据（进度追踪，提交到 git）
│   └── manifest.json
└── context/                            # 知识库数据文件
    ├── 00-index.md                     # 导航索引 + 任务路由表
    ├── GLOSSARY.md                     # 项目术语表
    ├── 01-project-overview/README.md   # 项目定位与能力概览
    ├── 02-architecture/README.md       # 技术栈与模块依赖拓扑
    ├── 03-domain-model/README.md       # 聚合根、实体、值对象
    ├── 04-business-rules/              # 业务规则（按模块拆分）
    │   ├── _TEMPLATE.md                # 业务规则文档统一模板
    │   ├── {module}-rules.md           # 模块业务规则（含禁止事项 + 入口速查）
    │   └── {module}/
    │       ├── source-facts.md         # 源码事实（方法签名 + 事务类型）
    │       ├── legacy-and-side-paths.md# 历史旁路与已知例外
    │       └── governance-gaps.md      # 代码治理缺口
    ├── 05-status-flow/README.md        # 状态流转与枚举
    ├── 06-database/README.md           # 数据库表速查
    ├── 07-api/README.md                # 接口约定
    ├── 08-code-style/service-layer-guide.md # 编码分层规范
    ├── 09-testing/README.md            # 测试规范
    ├── 10-prompt-templates/README.md   # AI 提示词模板（按需生成）
    ├── 11-review-checklists/           # AI 代码审查清单
    │   ├── ai-generated-code-checklist.md # AI 生成代码通用检查清单
    │   └── {module}-change-checklist.md   # 各模块变更专项清单
    └── 99-other/
        └── knowledge-base-protocol.md  # 知识库维护协议
```

### 断点续传

如果中途中断，在新 session 中说：

```
继续生成知识库
```

AI 会读取 `docs_kb_{项目名}/.knowledge-base-init/manifest.json` 自动恢复到上次进度。

## 前置依赖

本技能是一个 **AI Agent Skill**，不是独立运行的程序。需要配合以下 AI 编码工具使用：

| Agent | 兼容性 | 说明 |
|-------|:------:|------|
| **Qoder** | 完全兼容 | 原生支持 Skill 体系 |
| **OpenAI Codex** | 完全兼容 | 放入 `~/.codex/skills/` 目录即可 |
| **Cursor** | 可用 | 将 SKILL.md 内容作为 Rules 或 Prompt 使用 |
| **其他 Agent** | 可用 | 将 SKILL.md 内容注入系统提示词 |

支持的编程语言：Java、Go、TypeScript/Node.js、Python、Rust，以及其他有标准目录结构的语言。

## 项目结构

```
knowledge-base-init/
├── SKILL.md                            # 技能定义（指令、工作流、质疑体系）
├── LICENSE                             # MIT 协议
└── assets/
    ├── examples.md                     # 好 / 坏知识条目对比样例
    └── templates/                      # 产出文档模板
        ├── agents-kb-template.md       # → AGENTS_KB_{项目名}.md
        ├── index-template.md           # → 00-index.md
        ├── business-rules-template.md  # → {module}-rules.md
        ├── source-facts-template.md    # → source-facts.md
        ├── governance-gaps-template.md # → governance-gaps.md
        ├── knowledge-base-protocol-template.md  # → knowledge-base-protocol.md
        └── legacy-and-side-paths-template.md  # → legacy-and-side-paths.md
```

## FAQ

**Q: 生成一个中等项目的知识库需要多久？**
A: 取决于项目规模和 Agent 的上下文窗口。通常一个 200+ 文件的 Java 项目需要 15–20 轮对话，建议在时间充裕时启动。

**Q: 可以只为某个模块生成知识库吗？**
A: 可以。Phase 0 阶段会请你选择哪些模块做"完整深度"扫描，哪些只做"骨架级"扫描，哪些跳过。

**Q: 生成的知识库需要定期更新吗？**
A: 建议每次较大重构后重新生成。Phase 5 会产出 `knowledge-base-protocol.md` 维护协议文档，指导后续增量更新。

**Q: 已有 AGENTS.md 或 docs_kb_{项目名}/ 目录怎么办？**
A: 技能会自动检测。已有 AGENTS.md 仅在末尾追加一行知识库引用，不修改任何现有内容；已有知识库会询问你选择覆盖、增量追加或取消。

**Q: 产出文档是中文还是英文？**
A: 默认中文，可在 Phase 0 用户确认阶段切换为英文。

## 已知限制

- **不支持二进制 / 编译产物分析** — 只扫描源码文本，不解析编译产物
- **框架特异性** — 模板以通用后端项目为主，纯前端项目的组件 / 状态管理扫描深度有限
- **跨服务追踪有限** — 只记录出站调用，不追踪下游服务的实现细节
- **测试覆盖率不做运行时验证** — 只静态扫描测试文件，不执行测试
- **大型 Monorepo** — 需按子项目逐个执行，不支持一次性全量扫描

## License

[MIT](LICENSE)
