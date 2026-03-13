# 沙盘推演 480 任务执行索引（自动生成）

> **文档类型**：M6 任务库全量执行索引  
> **文档后缀**：auto  
> **版本**：v1.0  
> **日期**：2026-03-13  
> **数据来源**：M6_任务库起始清单.md、当前体系的全量沙盘任务库_v2_重新导出.csv  
> **关联记录**：沙盘推演记录_v6_sonnet3_auto.md（含 5 个完整推演场景：场景一～五）  
**测试执行记录**：沙盘推演_480任务测试_00_总览_auto.md（总览）+ 01_GEN / 02_MAP / 03_SYS / 04_LEGACY_E / 05_LEGACY_N 共 5 个分册（480 条均已做完测试记录，可并行查阅）

---

## 1. 说明与图例

- **本索引**：对 M6 所列 480 个任务的「跑完」执行清单；每任务一行，含场景概要、判定、证据、是否已有完整记录。
- **判定**：P1 完全满足 / P2 部分满足 / P3 概率不满足需下钻 / P4 不满足 / **P5 待执行**（已列入本批、尚未做完整推演）。
- **证据**：E0 文档级 / E1 单场景 / E2 可复现 / E3 自动化。
- **完整记录**：指向《沙盘推演记录_v6_sonnet3_auto.md》中的场景编号；空白表示尚未有完整 M2 推演。
- **执行批次**：EXEC-0（GEN 全量 + MAP 001–009）→ EXEC-1（MAP 010–030 + SYS 001–034）→ EXEC-2（LEGACY 320 + SYS 035–050）。

---

## 2. GEN 任务（80 条，EXEC-0）

| 序号 | 任务ID | 任务名称 | 首查G | T | PA | 场景概要 | 判定 | 证据 | 完整记录 |
|-----|--------|----------|-------|---|-----|----------|------|------|----------|
| 1 | GEN-DEF-001 | 对象集合可区分 | G0 | T1 | PA1 | 抽象：A/C/B 可区分 | **P1** | E0 | **→ 场景三** |
| 2 | GEN-DEF-002 | 状态空间全定义 | G0 | T1 | PA1 | 抽象：状态空间全定义 | P5 | E0 | — |
| 3 | GEN-DEF-003 | 演化算子全定义 | G0 | T1 | PA1 | 抽象：演化算子全定义 | P5 | E0 | — |
| 4 | GEN-DEF-004 | 相互作用路径明确定义 | G0 | T1 | PA1 | 抽象：相互作用路径明确 | P5 | E0 | — |
| 5 | GEN-DEF-005 | 时间轴与事件序列可表达 | G0 | T1 | PA1 | 抽象：时间轴与事件可表达 | P5 | E0 | — |
| 6 | GEN-DEF-006 | 主体/环境边界不混淆 | G0 | T1 | PA1 | 抽象：主体/环境边界 | P5 | E0 | — |
| 7 | GEN-DEF-007 | 异常态仍可描述 | G0 | T1 | PA1 | 抽象：异常态可描述 | P5 | E0 | — |
| 8 | GEN-DEF-008 | 恢复态仍可回到定义域 | G0 | T1 | PA1 | 抽象：恢复态回定义域 | P5 | E0 | — |
| 9 | GEN-LEGAL-009 | durable 真源唯一 | G1 | T2 | PA5 | 抽象：真源唯一 | P5 | E0 | — |
| 10 | GEN-LEGAL-010 | ephemeral 影子状态不升格为真源 | G1 | T2 | PA5 | 抽象：影子不升格 | P5 | E0 | — |
| 11 | GEN-LEGAL-011 | 跨粒子无旁路写 | G1 | T2 | PA5 | B 仅写 V_B 与授权路径 | **P2** | E1 | **→ 场景二** |
| 12 | GEN-LEGAL-012 | 写边界不越权 | G1 | T2 | PA5 | 抽象：写边界不越权 | P5 | E0 | — |
| 13 | GEN-LEGAL-013 | 时间单调不回退 | G1 | T2 | PA1 | 抽象：时间单调 | P5 | E0 | — |
| 14 | GEN-LEGAL-014 | 接口链完备 | G1 | T2 | PA1 | 抽象：接口链完备 | P5 | E0 | — |
| 15 | GEN-LEGAL-015 | 关闭后禁止写入已终局对象 | G1 | T2 | PA5 | 抽象：关闭后禁写 | P5 | E0 | — |
| 16 | GEN-LEGAL-016 | 重放不产生双写 | G1 | T2 | PA5 | 抽象：重放无双写 | P5 | E0 | — |
| 17 | GEN-BOUND-017 | 通知数量有界 | G3 | T3 | PA2 | 抽象：通知有界 | **P1** | E0 | **→ 场景四** |
| 18 | GEN-BOUND-018 | 上下文数量有界 | G3 | T3 | PA2 | 抽象：上下文有界 | P5 | E0 | — |
| 19 | GEN-BOUND-019 | 义务数量有界 | G3 | T3 | PA2 | 抽象：义务有界 | P5 | E0 | — |
| 20 | GEN-BOUND-020 | 前景栈深度有界 | G3 | T3 | PA2 | 抽象：前景栈有界 | P5 | E0 | — |
| 21 | GEN-BOUND-021 | 待重试队列有界 | G3 | T3 | PA2 | 抽象：重试队列有界 | P5 | E0 | — |
| 22 | GEN-BOUND-022 | 错误重放次数有界 | G3 | T3 | PA2 | 抽象：重放次数有界 | P5 | E0 | — |
| 23 | GEN-BOUND-023 | 广播扇出有界 | G3 | T3 | PA2 | 抽象：广播扇出有界 | P5 | E0 | — |
| 24 | GEN-BOUND-024 | 日志增长可截断 | G3 | T3 | PA2 | 抽象：日志可截断 | P5 | E0 | — |
| 25 | GEN-RECOVER-025 | 能量耗尽后可恢复 | G3 | T3 | PA3 | 抽象：能量可恢复 | P5 | E0 | — |
| 26 | GEN-RECOVER-026 | sleep_debt 可偿还 | G3 | T3 | PA3 | 抽象：sleep_debt 可偿还 | P5 | E0 | — |
| 27 | GEN-RECOVER-027 | refractory 结束后可重新进入 | G3 | T3 | PA3 | 抽象：refractory 后可再入 | P5 | E0 | — |
| 28 | GEN-RECOVER-028 | 被中断后存在返回路径 | G3 | T3 | PA4 | 抽象：中断后有返回路径 | P5 | E0 | — |
| 29 | GEN-RECOVER-029 | reservation 到期后释放 | G3 | T3 | PA4 | 抽象：reservation 释放 | P5 | E0 | — |
| 30 | GEN-RECOVER-030 | 阻塞任务能重新获得调度机会 | G3 | T3 | PA4 | 抽象：阻塞重获调度 | P5 | E0 | — |
| 31 | GEN-RECOVER-031 | 掉线恢复后可重建局部状态 | G3 | T3 | PA3 | 抽象：掉线恢复 | P5 | E0 | — |
| 32 | GEN-RECOVER-032 | 灾害结束后可重新耦合世界 | G3 | T3 | PA4 | 抽象：灾害后重耦合 | P5 | E0 | — |
| 33 | GEN-TERMINAL-033 | notification 必有终局 | G1、G3 | T2、T3 | PA6 | 抽象：notification 终局 | **P1** | E0 | **→ 场景五** |
| 34 | GEN-TERMINAL-034 | obligation 必有终局 | G1、G3 | T2、T3 | PA6 | 抽象：obligation 终局 | P5 | E0 | — |
| 35 | GEN-TERMINAL-035 | bounded context 必有终局 | G1、G3 | T2、T3 | PA6 | 抽象：context 终局 | P5 | E0 | — |
| 36 | GEN-TERMINAL-036 | lease 必有终局 | G1、G3 | T2、T3 | PA6 | 抽象：lease 终局 | P5 | E0 | — |
| 37 | GEN-TERMINAL-037 | staging 记录必能清空 | G1、G3 | T2、T3 | PA6 | 抽象：staging 可清空 | P5 | E0 | — |
| 38 | GEN-TERMINAL-038 | 重试流程必有终局 | G3 | T3 | PA6 | 抽象：重试终局 | P5 | E0 | — |
| 39 | GEN-TERMINAL-039 | 关闭广播必有终局 | G1、G3 | T2、T3 | PA6 | 抽象：关闭广播终局 | P5 | E0 | — |
| 40 | GEN-TERMINAL-040 | 恢复工单必有终局 | G3 | T3 | PA6 | 抽象：恢复工单终局 | P5 | E0 | — |
| 41 | GEN-PROGRESS-041 | P(s) 可定义 | G4 | T4 | PA8 | 抽象：P(s) 可定义 | P5 | E0 | — |
| 42 | GEN-PROGRESS-042 | 不同场类型有不同 P(s) | G4 | T4 | PA8 | 抽象：场类型 P(s) | P5 | E0 | — |
| 43 | GEN-PROGRESS-043 | 消息增长不等于进展 | G4 | T4 | PA8 | 抽象：消息≠进展 | P5 | E0 | — |
| 44 | GEN-PROGRESS-044 | 空转循环可识别 | G4 | T4 | PA8 | 抽象：空转可识别 | P5 | E0 | — |
| 45 | GEN-PROGRESS-045 | 无净进展循环可逃离 | G4 | T4 | PA8 | 抽象：无净进展可逃离 | P5 | E0 | — |
| 46 | GEN-PROGRESS-046 | 长期等待与合法静息可区分 | G4 | T4 | PA8 | 抽象：等待与静息区分 | P5 | E0 | — |
| 47 | GEN-PROGRESS-047 | recovering 微震荡可压制 | G3、G4 | T3、T4 | PA8 | 抽象：微震荡可压制 | P5 | E0 | — |
| 48 | GEN-PROGRESS-048 | 任务卡死可被 timeout breaker 解除 | G4 | T4 | PA8 | 抽象：卡死可解除 | P5 | E0 | — |
| 49 | GEN-COUPLE-049 | 有权消息可达 | G5 | T5 | PA7 | 抽象：有权消息可达 | P5 | E0 | — |
| 50 | GEN-COUPLE-050 | 本地危险信号可达 | G5 | T5 | PA7 | 抽象：危险信号可达 | P5 | E0 | — |
| 51 | GEN-COUPLE-051 | 世界事件可触发主体 | G5 | T5 | PA7 | 抽象：世界事件触发 | P5 | E0 | — |
| 52 | GEN-COUPLE-052 | 主体动作能改变世界 | G5 | T5 | PA9 | 抽象：动作改变世界 | P5 | E0 | — |
| 53 | GEN-COUPLE-053 | 动作影响能被他人感知 | G5 | T5 | PA9 | 抽象：影响可被感知 | P5 | E0 | — |
| 54 | GEN-COUPLE-054 | 感知与作用构成闭环 | G5 | T5 | PA7、PA9 | 抽象：感知作用闭环 | P5 | E0 | — |
| 55 | GEN-COUPLE-055 | 低头场景下本地点名仍可达 | G5 | T5 | PA7 | 抽象：本地点名可达 | P5 | E0 | — |
| 56 | GEN-COUPLE-056 | 灾害广播能重塑全局状态 | G5 | T5 | PA9 | 抽象：灾害广播重塑 | P5 | E0 | — |
| 57 | GEN-CONSIST-057 | 概率性失败能向下钻到结构根因 | G1、G3、G4 | T2、T3、T4 | PA5、PA6、PA8 | 抽象：失败下钻 | P5 | E0 | — |
| 58 | GEN-CONSIST-058 | 场景失败能向上收敛到 T/PA | G1、G3、G4 | T2、T3、T4 | PA5、PA6、PA8 | 抽象：失败上收敛 | P5 | E0 | — |
| 59 | GEN-CONSIST-059 | 同一失败不会出现双真源解释 | G1 | T2 | PA5 | 抽象：无双真源解释 | P5 | E0 | — |
| 60 | GEN-CONSIST-060 | 同一 effect 不会同时被判终局与未终局 | G3 | T3 | PA6 | 抽象：effect 终局一致 | P5 | E0 | — |
| 61 | GEN-CONSIST-061 | 同一循环不被同时判进展与空转 | G4 | T4 | PA8 | 抽象：循环判定一致 | P5 | E0 | — |
| 62 | GEN-CONSIST-062 | 可达性与可作用性不相互矛盾 | G5 | T5 | PA7、PA9 | 抽象：可达可作用一致 | P5 | E0 | — |
| 63 | GEN-CONSIST-063 | 异常工程规则不污染理想主干 | G0、G1 | T1、T2 | PA1、PA5 | 抽象：工程不污染主干 | P5 | E0 | — |
| 64 | GEN-CONSIST-064 | 每个新增要求都能向上追溯 | Gm | T1–T5 | PA1–PA9 | 抽象：要求可追溯 | P5 | E0 | — |
| 65 | GEN-SCHEMA-065 | 失败现象可归类到现有体系 | Gm | T1、T2 | PA1、PA5 | 元验证：可归类 | P5 | E0 | — |
| 66 | GEN-SCHEMA-066 | 无法归类时触发缺口审查 | Gm | T1、T2、T3 | PA1、PA2、PA6 | 元验证：缺口审查 | P5 | E0 | — |
| 67 | GEN-SCHEMA-067 | 双目标失效无桥接时触发公理修订 | Gm | T1、T4 | PA8 | 元验证：公理修订 | P5 | E0 | — |
| 68 | GEN-SCHEMA-068 | 要求层补丁膨胀时上收公理 | Gm | T1–T5 | PA1–PA9 | 元验证：上收公理 | P5 | E0 | — |
| 69 | GEN-SCHEMA-069 | 一般任务与体系绑定任务可区分 | Gm | T1 | PA1 | 元验证：任务可区分 | P5 | E0 | — |
| 70 | GEN-SCHEMA-070 | 任务库跨体系可迁移 | Gm | T1 | PA1 | 元验证：可迁移 | P5 | E0 | — |
| 71 | GEN-SCHEMA-071 | 证据链字段完备 | Gm | T2 | PA5 | 元验证：证据完备 | P5 | E0 | — |
| 72 | GEN-SCHEMA-072 | 概率性不满足必须继续下钻 | Gm | T2、T4 | PA8 | 元验证：P3 下钻 | P5 | E0 | — |
| 73 | GEN-SCHEMA-073 | 长期运行测试覆盖资源与恢复 | Gm | T3 | PA2、PA3 | 元验证：长期覆盖 | P5 | E0 | — |
| 74 | GEN-SCHEMA-074 | 耦合测试同时覆盖输入与输出 | Gm | T5 | PA7、PA9 | 元验证：耦合覆盖 | P5 | E0 | — |
| 75 | GEN-SCHEMA-075 | 根因追溯在 effect/真源 层终止条件清晰 | Gm | T2、T3 | PA5、PA6 | 元验证：根因终止 | P5 | E0 | — |
| 76 | GEN-SCHEMA-076 | P(s) 的选择有适用域声明 | Gm | T4 | PA8 | 元验证：P(s) 适用域 | P5 | E0 | — |
| 77 | GEN-SCHEMA-077 | 合法静息与病态沉寂可区分 | Gm | T3、T4 | PA4、PA8 | 元验证：静息沉寂 | P5 | E0 | — |
| 78 | GEN-SCHEMA-078 | 工程重放任务与理想主干任务分开统计 | Gm | T2 | PA1、PA5 | 元验证：分开统计 | P5 | E0 | — |
| 79 | GEN-SCHEMA-079 | 汇总矩阵可从单任务自动聚合 | Gm | T1–T5 | PA1–PA9 | 元验证：自动聚合 | P5 | E0 | — |
| 80 | GEN-SCHEMA-080 | 任务库存在边际停止规则 | Gm | T1–T5 | PA1–PA9 | 元验证：停止规则 | P5 | E0 | — |

---

## 3. MAP 任务（30 条，EXEC-0/EXEC-1）

| 序号 | 任务ID | 任务名称 | 首查G | T | PA | 场景概要 | 判定 | 证据 | 完整记录 |
|-----|--------|----------|-------|---|-----|----------|------|------|----------|
| 81 | MAP-001 | 对象-层级映射完备 | Gm/G0/G1 | T1、T2 | PA1、PA5 | 映射 GEN-DEF→G0 | P5 | E0 | — |
| 82 | MAP-002 | 状态字段→真源对象映射完备 | G1 | T2 | PA5 | 映射 GEN-LEGAL-009→G1 | P5 | E0 | — |
| 83 | MAP-003 | effect 类别→终局状态映射完备 | G3 | T3 | PA6 | 映射 GEN-TERMINAL→G1/G3 | P5 | E0 | — |
| 84 | MAP-004 | 可达性任务→PA7/G5 映射完备 | G5 | T5 | PA7 | 映射 GEN-COUPLE→G5 | P5 | E0 | — |
| 85 | MAP-005 | 可作用性任务→PA9/G5 映射完备 | G5 | T5 | PA9 | 映射 GEN-COUPLE→G5 | P5 | E0 | — |
| 86 | MAP-006 | 恢复类任务→PA3/PA4/G3 映射完备 | G3 | T3 | PA3、PA4 | 映射 GEN-RECOVER→G3 | P5 | E0 | — |
| 87 | MAP-007 | 进展类任务→PA8/G4 映射完备 | G4 | T4 | PA8 | 映射 GEN-PROGRESS→G4 | P5 | E0 | — |
| 88 | MAP-008 | 有界性任务→PA2/G3 映射完备 | G3 | T3 | PA2 | 映射 GEN-BOUND→G3 | P5 | E0 | — |
| 89 | MAP-009 | 写合法性任务→T2/PA5/G1 映射完备 | G1 | T2 | PA5 | 映射 GEN-LEGAL→G1 | P5 | E0 | — |
| 90 | MAP-010 | 世界灾害任务→T3/T5/G8 映射完备 | G8 | T3、T5 | PA4、PA7、PA9 | 映射 灾害→G8 | P5 | E0 | — |
| 91 | MAP-011 | 工程异常任务与理想主干拆分清晰 | G7、G8 | T2、T3 | PA1、PA5、PA6 | 映射 工程/主干 | P5 | E0 | — |
| 92 | MAP-012 | ForegroundStack 任务映射到 G2/G6 | G2、G6 | T2、T3 | PA4、PA7 | 映射 前景栈 | P5 | E0 | — |
| 93 | MAP-013 | WritebackLedger 任务映射到 G1/G7 | G1、G7 | T2、T3 | PA5、PA6 | 映射 写回账本 | P5 | E0 | — |
| 94 | MAP-014 | Obligation 任务映射到 G4/G6 | G4、G6 | T3、T4 | PA6、PA8 | 映射 义务 | P5 | E0 | — |
| 95 | MAP-015 | Notification 任务映射到 G3/G5/G8 | G3、G5、G8 | T3、T5 | PA6、PA7 | 映射 通知 | P5 | E0 | — |
| 96 | MAP-016 | ActivityInstance 任务映射到 G2/G3/G4 | G2、G3、G4 | T3、T4 | PA3、PA4、PA8 | 映射 活动实例 | P5 | E0 | — |
| 97 | MAP-017 | place_id/physical_location 映射清晰 | G2、G5 | T2、T5 | PA1、PA7、PA9 | 映射 位置 | P5 | E0 | — |
| 98 | MAP-018 | baseline_attention 与 effective threshold 分离 | G2、G4 | T2、T4 | PA5、PA8 | 映射 注意力 | P5 | E0 | — |
| 99 | MAP-019 | local/reactive override 适用域映射清晰 | G5 | T5 | PA7、PA9 | 映射 本地覆盖 | P5 | E0 | — |
| 100 | MAP-020 | lease/fencing token 任务映射到 G7 | G7 | T2、T3 | PA5、PA6 | 映射 租约 | P5 | E0 | — |
| 101 | MAP-021 | replay/idempotency 任务映射到 G7 | G7 | T2、T3 | PA5、PA6 | 映射 重放幂等 | P5 | E0 | — |
| 102 | MAP-022 | overload_mode 任务映射到 G8 | G8 | T3、T5 | PA2、PA7 | 映射 过载 | P5 | E0 | — |
| 103 | MAP-023 | compaction/archive 任务映射到 G7/G8 | G7、G8 | T3 | PA2、PA6 | 映射 压缩归档 | P5 | E0 | — |
| 104 | MAP-024 | identity-based obligation filtering 映射完备 | G4、G6 | T4、T5 | PA7、PA8 | 映射 身份义务过滤 | P5 | E0 | — |
| 105 | MAP-025 | timeout breaker 映射完备 | G4 | T4 | PA8 | 映射 超时破除 | P5 | E0 | — |
| 106 | MAP-026 | hysteresis lock 映射完备 | G3、G4 | T3、T4 | PA3、PA8 | 映射 迟滞锁 | P5 | E0 | — |
| 107 | MAP-027 | hazard 强制唤醒映射完备 | G5、G8 | T3、T5 | PA4、PA7 | 映射 灾害唤醒 | P5 | E0 | — |
| 108 | MAP-028 | ContextCloseRequest/ClosedEvent 双向映射完备 | G1、G3、G7 | T2、T3 | PA5、PA6 | 映射 关闭事件 | P5 | E0 | — |
| 109 | MAP-029 | 任务证据字段与汇总矩阵字段一一对应 | Gm | T1、T2 | PA1、PA5 | 映射 证据矩阵 | P5 | E0 | — |
| 110 | MAP-030 | 任务库批次优先级与目标风险等级一致 | Gm | T3、T4 | PA2、PA4、PA8 | 映射 批次风险 | P5 | E0 | — |

---

## 4. SYS 任务（50 条，EXEC-1/EXEC-2）

| 序号 | 任务ID | 任务名称 | 首查G | T | PA | 场景概要 | 判定 | 证据 | 完整记录 |
|-----|--------|----------|-------|---|-----|----------|------|------|----------|
| 111 | SYS-CONTEXT-001 | Context 创建—参与—关闭全生命周期 | G1、G3、G6 | T2、T3 | PA5、PA6 | v6 上下文生命周期 | P5 | E0 | — |
| 112 | SYS-CONTEXT-002 | bounded session 截止后级联清栈 | G1、G3、G6 | T2、T3 | PA5、PA6 | v6 级联清栈 | P5 | E0 | — |
| 113 | SYS-CONTEXT-003 | persistent context 长期运行归档 | G3、G7 | T3 | PA2、PA6 | v6 持久上下文归档 | P5 | E0 | — |
| 114 | SYS-STACK-004 | ForegroundStack push/pop/repair | G2、G6 | T2、T3 | PA4、PA5 | v6 前景栈 | P5 | E0 | — |
| 115 | SYS-STACK-005 | 前景场关闭后 resume_token 恢复原任务 | G3、G4、G6 | T3、T4 | PA4、PA8 | v6 恢复令牌 | P5 | E0 | — |
| 116 | SYS-LOCAL-006 | 物理场与前景场动作并集判定 | G5 | T5 | PA7、PA9 | v6 动作并集 | P5 | E0 | — |
| 117 | SYS-LOCAL-007 | 低头族被点名的最小本地响应 | G5 | T5 | PA7、PA9 | v6 本地点名 | P5 | E0 | — |
| 118 | SYS-ENERGY-008 | energy 归零进入 recovering 并带迟滞锁 | G3、G4 | T3、T4 | PA3、PA8 | v6 energy+next_wakeup | **P1** | E1 | **→ 场景一** |
| 119 | SYS-ENERGY-009 | recovering 期间屏蔽非紧急唤醒 | G3、G4 | T3、T4 | PA3、PA8 | v6 恢复期屏蔽 | P5 | E0 | — |
| 120 | SYS-ENERGY-010 | sleeping 状态下 hazard 唤醒优先级 | G5、G8 | T3、T5 | PA4、PA7 | v6 睡眠 hazard | P5 | E0 | — |
| 121 | SYS-ENERGY-011 | sleep_debt 累积与偿还闭环 | G3 | T3 | PA3 | v6 sleep_debt | P5 | E0 | — |
| 122 | SYS-NOTIF-012 | Notification surfaced/acked/expired/merged | G3、G5 | T3 | PA6 | v6 通知状态机 | P5 | E0 | — |
| 123 | SYS-NOTIF-013 | Notification fanout bounded + backpressure | G3、G5、G8 | T3、T5 | PA2、PA7 | v6 通知有界 | P5 | E0 | — |
| 124 | SYS-NOTIF-014 | 重复通知 collapse/merge | G3、G8 | T3 | PA2、PA6 | v6 通知折叠 | P5 | E0 | — |
| 125 | SYS-OBLIG-015 | Obligation proposed→expired 全状态机 | G4、G6 | T3、T4 | PA6、PA8 | v6 义务状态机 | P5 | E0 | — |
| 126 | SYS-OBLIG-016 | Obligation blocked 后重新获得推进机会 | G4、G6 | T3、T4 | PA4、PA8 | v6 义务推进 | P5 | E0 | — |
| 127 | SYS-OBLIG-017 | Identity-based obligation filtering | G4、G6 | T4、T5 | PA7、PA8 | v6 身份义务过滤 | P5 | E0 | — |
| 128 | SYS-OBLIG-018 | 循环阻塞义务的 deadlock breaker | G4 | T4 | PA8 | v6 义务死锁破除 | P5 | E0 | — |
| 129 | SYS-LEDGER-019 | WriteEvent-first durable 写回 | G1、G7 | T2、T3 | PA5、PA6 | v6 写回 G1-R001 相关 | **P2** | E1 | **→ 场景二** |
| 130 | SYS-LEDGER-020 | ActionReceipt 到达前禁止 durable 成功写回 | G1、G7 | T2 | PA5 | v6 等 receipt 再写回 | P5 | E0 | — |
| 131 | SYS-LEDGER-021 | WritebackLedger replay 幂等 | G1、G7 | T2、T3 | PA5、PA6 | v6 重放幂等 | P5 | E0 | — |
| 132 | SYS-LEDGER-022 | StagingLedger 长滞留清理 | G3、G7 | T3 | PA2、PA6 | v6 staging 清理 | P5 | E0 | — |
| 133 | SYS-LEASE-023 | resource lease 获取/续租/过期/释放 | G6、G7 | T2、T3 | PA5、PA6 | v6 租约 | P5 | E0 | — |
| 134 | SYS-LEASE-024 | lease 过期后旧写入被 fencing token 拒绝 | G7 | T2 | PA5 | v6 fencing | P5 | E0 | — |
| 135 | SYS-ROUTE-025 | emit→route→apply 四阶段闭环 | G1、G5 | T2、T5 | PA1、PA9 | v6 四阶段 | P5 | E0 | — |
| 136 | SYS-ROUTE-026 | 乱序消息不破坏真源 | G1、G7 | T2、T3 | PA5、PA6 | v6 乱序 | P5 | E0 | — |
| 137 | SYS-ROUTE-027 | 网络分区后的 split-brain 修复 | G7 | T2、T3 | PA5、PA6 | v6 分区修复 | P5 | E0 | — |
| 138 | SYS-ENGINEER-028 | 掉线重连后的状态重建 | G7 | T3 | PA3、PA4 | v6 掉线恢复 | P5 | E0 | — |
| 139 | SYS-ENGINEER-029 | 系统重启后的 replay + jitter | G7、G8 | T3 | PA3、PA6 | v6 重启重放 | P5 | E0 | — |
| 140 | SYS-ENGINEER-030 | 时钟漂移下 TTL/截止语义保持一致 | G7 | T2、T3 | PA1、PA6 | v6 时钟 | P5 | E0 | — |
| 141 | SYS-OVERLOAD-031 | 大规模群聊 mention 风暴降载 | G8 | T3、T5 | PA2、PA7 | v6 风暴降载 | P5 | E0 | — |
| 142 | SYS-OVERLOAD-032 | 全局 overload_mode 切换与退出 | G8 | T3 | PA2、PA4 | v6 overload 切换 | P5 | E0 | — |
| 143 | SYS-PERSIST-033 | 日志 compaction/archive/snapshot | G7、G8 | T3 | PA2、PA6 | v6 持久化 | P5 | E0 | — |
| 144 | SYS-PERSIST-034 | 继续运行链 continue-as-new/rollover | G7、G8 | T3 | PA2、PA6 | v6 继续运行 | P5 | E0 | — |
| 145 | SYS-HAZARD-035 | 世界级地震触发 hazard broadcast | G5、G8 | T3、T5 | PA4、PA7、PA9 | v6 地震灾害 | P5 | E0 | — |
| 146 | SYS-HAZARD-036 | 火山/火灾/停电导致场强制关闭迁移 | G5、G8 | T3、T5 | PA4、PA6、PA9 | v6 灾害迁移 | P5 | E0 | — |
| 147 | SYS-HAZARD-037 | 物理道路封锁改变可达 field 与 obligation | G5、G8 | T5 | PA7、PA9 | v6 道路封锁 | P5 | E0 | — |
| 148 | SYS-HAZARD-038 | 医院超载/资源稀缺场景的抢占调度 | G3、G4、G8 | T3、T4 | PA3、PA4、PA8 | v6 抢占调度 | P5 | E0 | — |
| 149 | SYS-MULTI-039 | 多 agent 协作 participant/role 一致性 | G5、G6 | T2、T5 | PA5、PA7、PA9 | v6 多 agent | P5 | E0 | — |
| 150 | SYS-MULTI-040 | 多人同时 claim 同一 deliverable 冲突仲裁 | G6、G7 | T2、T3 | PA5、PA6 | v6 冲突仲裁 | P5 | E0 | — |
| 151 | SYS-PROGRESS-041 | 长期无净进展但消息活跃的伪活性识别 | G4 | T4 | PA8 | v6 伪活性 | P5 | E0 | — |
| 152 | SYS-PROGRESS-042 | 合法静息期不被误判为空转 | G4 | T4 | PA8 | v6 静息 | P5 | E0 | — |
| 153 | SYS-LONGRUN-043 | 从 rest→work→rest 日周期长期稳定 | G3、G4 | T3、T4 | PA2、PA3、PA8 | v6 日周期 | P5 | E0 | — |
| 154 | SYS-LONGRUN-044 | 跨日跨周累计任务与精力债务平衡 | G3、G4 | T3、T4 | PA2、PA3、PA8 | v6 精力债务 | P5 | E0 | — |
| 155 | SYS-LONGRUN-045 | 海量 context 共存下路由局部化 | G3、G5、G8 | T3、T5 | PA2、PA7、PA9 | v6 路由局部化 | P5 | E0 | — |
| 156 | SYS-LONGRUN-046 | 海量 agent 并发下不全局扫描 | G8 | T3 | PA2 | v6 不全局扫描 | P5 | E0 | — |
| 157 | SYS-ADV-047 | 模型调用失败不阻塞热路径 | G8 | T3 | PA4 | v6 模型失败 | P5 | E0 | — |
| 158 | SYS-ADV-048 | 慢存储不阻塞热路径 | G8 | T3 | PA4 | v6 慢存储 | P5 | E0 | — |
| 159 | SYS-GOV-049 | 人工强制介入与系统自动规则边界 | G6、G7 | T2、T5 | PA5、PA9 | v6 治理边界 | P5 | E0 | — |
| 160 | SYS-GOV-050 | 规则热更新不破坏已有 durable 真源 | G7 | T2、T3 | PA5、PA6 | v6 热更新 | P5 | E0 | — |

---

## 5. LEGACY 任务（320 条，EXEC-2）

LEGACY 任务共 320 条：**LEGACY-E001～LEGACY-E220**（220 条极端/压力场景）、**LEGACY-N001～LEGACY-N100**（100 条正常/基线场景）。  
执行状态统一为：**判定 P5（待执行）**，**证据 E0**，**完整记录 —**。  
任务名称、推荐场景与典型失败代码见 **CSV 原文**（`docs/rule/gpt/当前体系的全量沙盘任务库_v2_重新导出.csv`）。

### 5.1 LEGACY 执行汇总表（按类）

| 类别 | 任务ID 范围 | 条数 | 主验证 G | 判定 | 证据 | 完整记录 |
|------|-------------|------|----------|------|------|----------|
| 通知与背压 | LEGACY-E001～E025 | 25 | G3、G5、G8 | P5 | E0 | — |
| 前景栈与切换 | LEGACY-E026～E050 | 25 | G2、G5、G6 | P5 | E0 | — |
| 能量与睡眠 | LEGACY-E051～E070 | 20 | G3、G8 | P5 | E0 | — |
| 写回与崩溃恢复 | LEGACY-E071～E095 | 25 | G1、G3、G7 | P5 | E0 | — |
| 网络与时间一致性 | LEGACY-E096～E115 | 20 | G1、G3、G7 | P5 | E0 | — |
| 义务与死锁 | LEGACY-E116～E140 | 25 | G4、G6 | P5 | E0 | — |
| 并发与权限 | LEGACY-E141～E160 | 20 | G1、G6、G7 | P5 | E0 | — |
| 物理场与本地冲突 | LEGACY-E161～E180 | 20 | G2、G5 | P5 | E0 | — |
| 灾害与世界级风险 | LEGACY-E181～E200 | 20 | G5、G8 | P5 | E0 | — |
| 长期运行 | LEGACY-E201～E220 | 20 | G3、G4、G8 | P5 | E0 | — |
| 基线矩阵（正常）| LEGACY-N001～N100 | 100 | G0～G8 | P5 | E0 | — |

### 5.2 LEGACY 全量任务 ID 列表（320 条）

以下为 320 条 LEGACY 任务 ID 的完整列表，便于与 CSV 对照执行与标注。

- **LEGACY-E001**～**LEGACY-E220**（220 条）
- **LEGACY-N001**～**LEGACY-N100**（100 条）

---

## 6. 汇总统计

| 项目 | 数值 |
|------|------|
| 任务总数 | 480 |
| 已完整推演（有 M2 记录）| 5（场景一：SYS-ENERGY-008/G3-R003；场景二：GEN-LEGAL-011/SYS-LEDGER-019/G1-R001；场景三～五：GEN-DEF-001、GEN-BOUND-017、GEN-TERMINAL-033）|
| P1 判定 | 4 |
| P2 判定 | 1 |
| P5 待执行 | 475 |
| EXEC-0 任务数 | 89（GEN 80 + MAP 001–009）|
| EXEC-1 任务数 | 71（MAP 010–030 + SYS 001–034）|
| EXEC-2 任务数 | 320（LEGACY 320 + SYS 035–050）|

---

*文档结束。本索引与《沙盘推演记录_v6_sonnet3_auto.md》配套使用，供 M3 汇总与后续批次执行参考。*
