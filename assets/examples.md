# 质量样例

> 本文件展示"好的知识库条目"和"坏的知识库条目"的对比，帮助 AI 理解质量标准。

---

## 1. 业务规则：好 vs 坏

### ❌ 坏例子

```markdown
库存变更通过 TCC 模式实现，保证了分布式事务的一致性。
```

**问题**：没有源码引用，像文档摘抄而非源码事实。AI 在任何项目都可能编出这种"正确但无用的废话"。

### ✅ 好例子

```markdown
**[规则-1] 日常账面库存变更必须经由 TCC 主链**

- 来源：`BookStockProcessor#batchFreezeStockWithVer`（domain/processor/BookStockProcessor.java:45-78）
- 含义：新增业务禁止直接 UPDATE sku_stock 表，必须通过 Repository 的 TCC 方法
- TCC 方法清单：
  - try: `batchFreezeStockWithVer` — 冻结 qty
  - commit: `batchCommitStockWithVer` — 确认变更
  - rollback: `rollbackStockWithVers` — 回滚冻结

**反例搜索结果**：
- `ErpAutoSyncService#syncStock`（domain/service/ErpAutoSyncService.java:112）直接 UPDATE sku_stock，未走 TCC
- 标记为 legacy，详见 `legacy-and-side-paths.md` §ERP 差额同步
```

**好在哪**：有源码路径、有行号、有方法签名、有反例搜索结果。

---

## 2. 源码事实：好 vs 坏

### ❌ 坏例子

```markdown
## Service 层

该模块的 Service 层负责处理库存相关的业务逻辑，包括库存查询、库存变更、
库存预警等功能。主要 Service 类有 SkuStockService 和 StockAlertService。
```

**问题**：泛泛而谈，信息密度极低。没有方法签名，没有调用关系，没有任何具体信息。

### ✅ 好例子

```markdown
## Service 层

### SkuStockService

**文件路径**：`domain/service/SkuStockService.java`
**文件行数**：487 行
**public 方法清单**：

| 方法签名 | 用途 | 事务 | 关键行为 |
|---------|------|------|---------|
| `DataResult writeStockFlowV1(WarehouseStockFlowReqDTO)` | 账面库存变更入口 | Seata TCC | try冻结→commit确认+写流水→rollback回滚 |
| `DataResult writeCanUseStockLockFlow(LockInfoBatch)` | 可用库存占用 | Seata TCC | try冻结→commit占用单→rollback释放 |
| `List<SkuStock> queryBySkuIds(Long corpId, List<Long> skuIds)` | 批量查库存 | 无 | 按 corpId+skuId 查询 |

**不理解/无法归类的代码**：
- L342-L358: `handleLegacyStockSync` 方法，调用了 ERP 适配器但未走 TCC，
  方法上有注释"// TODO: 待迁移到标准链路"
```

**好在哪**：列出了全部 public 方法、每个方法的事务类型、关键行为。还主动报告了不理解的代码。

---

## 3. 质疑日志：好 vs 坏

### ❌ 坏例子

```
[Q1] 扫描完成，共 12 个文件，全部已读。
```

**问题**：没有用工具验证，没有列出文件清单。

### ✅ 好例子

```
[Q1] 模块 inventory - Service 层

find 命令结果: find src/main/java -path "*/inventory/*" -name "*Service*.java" | wc -l → 12

已读文件清单:
1. ✅ SkuStockService.java (487行) — 提取 8 个 public 方法
2. ✅ BookStockProcessor.java (312行) — 提取 5 个 public 方法
3. ✅ CanUseStockProcessor.java (198行) — 提取 4 个 public 方法
...
12. ✅ StockFlowType.java (89行) — 提取 17 个枚举值

覆盖率: 12/12 (100%)
未读文件: 无
```

**好在哪**：用 find 命令给出客观数字，逐文件列出并标注提取结果。

---

## 4. 置信度标注示例

```markdown
## 置信度报告 — inventory 模块

### [确认] 有明确源码证据
1. 账面库存变更必须经由 TCC 主链
2. 库存流水(balance change + flow write)在同一事务内
3. StockFlowType 枚举值不可临时新增

### [推断] 需用户确认
4. canUseQty 的计算公式可能按租户配置不同（Setting 表有开关字段，但未追踪完整的配置读取链路）
5. 批次库存与 SKU 级库存应该是同步变更的（基于代码观察，未找到反例）

### [猜测] 待验证
6. waitShelfQty 字段似乎完全由 WMS 维护，GoodsService 只做镜像写入（基于方法命名推测，未深入追踪 WMS 调用链）
```
