# AI Coding 知识库索引

> 本文件是 {项目名} 项目 AI Coding 知识库的导航总目录。
> 不知道某个任务该读哪份文档时，先看本文件的"任务路由"部分。

---

## 一、知识库结构

```
docs_kb_{项目名}/
├── .knowledge-base-init/                # Skill 元数据（隐藏目录）
│   └── manifest.json
└── context/                             # 知识库数据文件
    ├── 00-index.md                      # 本文件（导航）
    ├── GLOSSARY.md                      # 项目术语表
    ├── 01-project-overview/             # 项目概览
    │   └── README.md
    ├── 02-architecture/                 # 架构与依赖
    │   └── README.md
    ├── 03-domain-model/                 # 领域模型
    │   └── README.md
    ├── 04-business-rules/               # 业务规则（按模块拆分）
    │   ├── _TEMPLATE.md                 # 业务规则文档统一模板
    │   └── {module}-rules.md            # 各模块业务规则
    │       └── {module}/                # 各模块深度文档
    │           ├── source-facts.md
    │           ├── legacy-and-side-paths.md
    │           └── governance-gaps.md
    ├── 05-status-flow/                  # 状态流转与枚举
    │   └── README.md
    ├── 06-database/                     # 数据库表速查
    │   └── README.md
    ├── 07-api/                          # 接口约定
    │   └── README.md
    ├── 08-code-style/                   # 编码规范
    │   └── service-layer-guide.md
    ├── 09-testing/                      # 测试规范
    │   └── README.md
    ├── 10-prompt-templates/             # AI 提示词模板（按需生成）
    │   └── README.md
    ├── 11-review-checklists/            # 代码审查清单
    │   ├── ai-generated-code-checklist.md
    │   └── {module}-change-checklist.md
    └── 99-other/
        └── knowledge-base-protocol.md   # 知识库维护协议
```

## 二、任务路由

| 任务类型 | 阅读顺序 |
|---------|---------|
| **修改 {模块A} 逻辑** | AGENTS_KB_{项目名}.md → `{module-a}-rules.md` → `{module-a}/` → `{module-a}-change-checklist.md` |
| **修改 {模块B} 逻辑** | AGENTS_KB_{项目名}.md → `{module-b}-rules.md` → `{module-b}/` → `{module-b}-change-checklist.md` |
| **新增 Controller / Service** | AGENTS_KB_{项目名}.md → `08-code-style/service-layer-guide.md` → 通用清单 |
| **查术语/概念** | `GLOSSARY.md` 术语表 |
| **了解项目全貌** | AGENTS_KB_{项目名}.md → `01-project-overview/README.md` → `02-architecture/README.md` |
| **查数据库表** | `06-database/README.md` |
| **写测试** | `09-testing/README.md` |
| **维护知识库** | `99-other/knowledge-base-protocol.md` |

## 三、维护原则

- **业务规则文档**：跟随业务代码变更同步更新
- **编码规范文档**：由团队统一维护
- **审查清单**：根据线上问题和 Review 反馈持续补充
