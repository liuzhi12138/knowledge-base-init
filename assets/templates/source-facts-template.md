# {模块名} 源码事实

> 本文件记录 **{模块名}** 模块的源码事实，由 AI 扫描源码生成。
> 只记录"代码实际做了什么"，不记录"应该做什么"。
> 业务规则以 `{module}-rules.md` 为准。

---

## 一、Service 层

### {ServiceName}

**文件路径**：`path/to/file`
**文件行数**：N 行
**public 方法清单**：

| 方法签名 | 用途 | 事务类型 | 关键行为 |
|---------|------|---------|---------|
| `ReturnType methodName(ParamType param)` | {用途} | {无/本地/分布式} | {一句话描述} |

**不理解/无法归类的代码**：
- {如有，列出具体行号和代码片段}
- {如无，写"无"}

---

## 二、Repository 层

### {RepositoryName}

**文件路径**：`path/to/file`
**文件行数**：N 行
**public 方法清单**：

| 方法签名 | SQL 操作 | 涉及表 | 是否含租户条件 |
|---------|---------|-------|-------------|
| `ReturnType methodName(ParamType)` | SELECT/INSERT/UPDATE/DELETE | {表名} | {是/否} |

---

## 三、Controller 层

### {ControllerName}

**文件路径**：`path/to/file`
**端点清单**：

| HTTP方法 | 路径 | 方法名 | 入参DTO | 调用Service |
|---------|------|-------|---------|------------|
| POST | `/api/{path}` | `methodName` | `XxxDTO` | `XxxService#method` |

---

## 四、枚举/常量

### {EnumName}

**文件路径**：`path/to/file`
**枚举值清单**：

| 枚举值 | code | 业务含义 | 引用位置 |
|-------|------|---------|---------|
| `VALUE_A` | 0 | {含义} | {哪些类引用了} |

---

## 五、Model/Entity 层

### {ModelName}

**文件路径**：`path/to/file`
**对应数据库表**：`{table_name}`
**字段清单**：

| 字段 | 类型 | 数据库列 | 是否计算字段 | 说明 |
|------|------|---------|------------|------|
| `fieldName` | type | column_name | {是/否} | {说明} |

---

## 六、跨服务/消息/事件

| 类型 | 名称 | 方向 | 用途 |
|------|------|------|------|
| HTTP调用 | `XxxClient` | 出站 | {用途} |
| 消息队列 | `XxxChannel` | 入站 | {用途} |
| 事件 | `XxxEvent` | 发布/监听 | {用途} |
