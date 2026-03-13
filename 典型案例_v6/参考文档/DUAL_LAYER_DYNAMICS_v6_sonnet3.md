# 异步耦合三粒子动力学系统
## 多智能体协作平台的第一性原理描述

> 版本：v6.0（sonnet3）
> 日期：2026-03-12
> 前版：v5.0（已存档）；FA 模型 v1.5（内容并入 L2 层）
> 核心变化：
> 1. 三粒子架构：A（世界）+ C（基础设施）+ Bᵢ（智能体），C 升级为独立粒子
> 2. 层级树（L0→L1→L2→L3→EP）：公理、运行时细化、工程补丁三者严格分层
> 3. 双重视角：每章同时提供"物理直觉 + 文件系统映射"
> 4. 维度优先原则：行为差异通过维度参数表达，不用 if-else 补丁规则
> 5. v5 + FA 全量内容在本文框架内有明确归位

---

## 撰写原则

| 视角 | 类比 | 理解方式 |
|------|------|---------|
| **物理直觉** | 粒子动力学 | 系统由粒子构成，粒子有变量，变量按演化方程演化 |
| **文件系统映射** | 存储结构 | 粒子=目录，变量=文件，演化=函数调用（读哪些文件、执行什么、写哪些文件）|

## 层级声明

| 层级 | 名称 | 判断标准 |
|------|------|---------|
| **L0** | 公理层 | 不可再简化；去掉则系统在逻辑上无法定义 |
| **L1** | 本体层 | 直接从 L0 推出；去掉则系统无法实例化 |
| **L2** | 运行时细化层 | 维度参数及其语义映射；保证理想条件下无限演化闭环 |
| **L3** | 策略/配置层 | 具体阈值/预设名称；去掉只影响数值，不影响闭环逻辑 |
| **EP** | 工程补丁层 | 仅在非理想条件（LLM 不完美、网络故障、时钟漂移）下才需要 |

**最小目标**：在理想条件下，系统满足 L0–L2 即可实现最小无限时间演化。L3 和 EP 是增强，不是前提。

**维度优先原则**：优先通过增加维度参数表达复杂性，而非增加 if-else 补丁规则。每个维度的语义定义本身就包含其对系统行为的影响。

---

---

# 第一部分：本体（Ontology）【L0 + L1 + L2】

> **物理直觉**：系统中存在什么？有哪些粒子，每个粒子有哪些状态变量？
>
> **文件系统映射**：顶级目录结构是什么？每个目录下有哪些子目录和文件？

---

## 第一章：系统构成与粒子定义【L0】

### 1.1 三粒子系统

```
物理直觉：
  系统 = 世界粒子 A  ∪  耦合介质场粒子 C  ∪  {智能体粒子 Bᵢ | i=1..n}

  A：世界状态数据库
     ——持有全局时钟、自然环境状态、所有交互上下文（InteractionContext）
     ——确定性演化（无 LLM），是系统共享的"物理现实"

  C：耦合介质场粒子（coupling medium field particle）
     ——确定性演化，没有自主意志
     ——物理类比：如电磁场之于电荷间的相互作用——C 是 A 与 B 之间相互影响传播的介质
     ——工程类比：邮政系统 + 调度室 + 仲裁机构
     ——职责：跨粒子路由、调度协调、资源锁管理、TTL 清理

  Bᵢ：智能体粒子
     ——有主观状态（记忆/控制/能力），核心演化由 LLM API 驱动
     ——不同 Bᵢ 之间有写隔离，通过 C 的路由间接影响

文件系统映射（顶级目录）：
  /
  ├── world/          ← 世界粒子 A 的存储空间（V_A）
  ├── coupling/       ← 耦合介质场粒子 C 的存储空间（V_C）
  ├── agents/
  │   ├── agent_1/   ← 智能体粒子 B₁ 的存储空间（V_B₁）
  │   ├── agent_2/
  │   └── ...
  └── event_log/     ← 全局事件日志（append-only，所有状态变更的唯一真源）
                        不属于任何粒子，由系统维护
```

### 1.2 粒子的三元组结构【L0】

每个粒子 P 是一个三元组：

```
P = ( V_P,  View_P,  R_P )

V_P    ：粒子 P 的本体变量集（真状态）
         只由 P 自身或授权的系统外力写入
         文件系统映射 → /P/ 目录下的所有文件

View_P ：粒子 P 对外暴露的可见投影函数
         View_P(observer) → 对特定观察者可见的状态子集
         不持有新信息，不独立存储
         文件系统映射 → /P/ 的读权限配置（其他粒子能看哪些文件）

R_P    ：粒子 P 的演化规则集，细分为四子规则：
           R_P^assemble  ：组装本次演化的输入上下文
           R_P^evolve    ：从上下文计算输出（动作提议 + 状态更新提议）
           R_P^emit      ：将输出转化为向外部发出的事件序列
           R_P^apply     ：将事件写回 V_P
         文件系统映射 → 函数定义（输入哪些文件、执行什么、修改哪些文件）

特殊说明：
  A 和 C 的 assemble/evolve 退化为确定性规则（无 LLM）
  Bᵢ 的 evolve 调用 LLM API
  A 和 C 的 assemble 等同于"读取相关输入"，evolve 等同于"执行确定性规则"
```

### 1.3 写隔离原则【L0 公理】

> **任何粒子不能直接写另一粒子目录下的文件。**

```
跨粒子影响的唯一合法路径：
  粒子 P 产生 Output
  → R_P^emit 转化为事件序列
  → 事件路由到目标方 R_Q^apply
  → 由目标方 Q 的规则决定如何更新自己的 V_Q

非法操作（严禁）：
  Bᵢ 直接修改 /world/ 下的文件            ← 禁止
  Bᵢ 直接修改 /coupling/ 下的文件         ← 禁止
  Bᵢ 直接修改 /agents/agent_j/ 下的文件   ← 禁止（i ≠ j）
  C  直接修改 /world/ 下的文件             ← 禁止
  C  直接修改 /agents/agent_i/ 下的文件    ← 禁止
  A  直接修改 /agents/agent_i/ 下的文件    ← 禁止

系统外力（合法例外，三类）：
  scheduler-originated events  ：调度器触发产生的元事件
  system-maintenance events    ：如强制写入 Event Log
  system-physics events        ：时间驱动的 energy/mood 自然衰减等
  → 这些权限由 L0 公理层授权，通过 R_P^apply 路径执行，不破坏写隔离
```

### 1.4 系统元设施【L0】

以下元设施不是粒子，但参与运行语义：

| 元设施 | 职责 | 文件系统映射 |
|--------|------|------------|
| **调度器**（Scheduler） | 持有瞬时运行态，决定哪些粒子何时演化 | 逻辑层；调度队列持久化到 /coupling/scheduler_queue.json |
| **全局事件日志**（Global Event Log） | append-only，所有状态变更的唯一真源 | /event_log/（不属于任何粒子）|
| **恢复机制**（Recovery Facility，可选）| 处理强制中断后的不一致状态 | /coupling/writeback_ledger/ + /coupling/staging_ledger/ |

普通粒子不能直接写另一粒子的 V_P。元设施可以向粒子注入系统外力事件，此权限由公理层授权，不破坏写隔离。

---

## 第二章：变量定义【L1 + L2】

> **物理直觉**：每个粒子内部存储了哪些状态？
>
> **文件系统映射**：每个目录下有哪些具体文件和子目录？

### 2.1 世界粒子 A 的变量（/world/ 目录）

```
物理直觉：
  V_A = 世界的客观状态，是系统共享的"物理现实"

文件系统映射：
  /world/
  ├── clock.json                    ← 单调递增的全局时钟（系统唯一时间真源）
  ├── natural_state/                ← 自然环境状态（地图、物理空间等，占位待扩展）
  └── contexts/                     ← 所有交互上下文（InteractionContext 集合）
      ├── ctx_001/
      │   ├── field_profile.json    ← 场的 9 维参数维度（见 §2.1.1）
      │   ├── participants.json     ← 参与者集合（静态成员集，closed 场固定）
      │   ├── roles.json            ← 角色配置（host/recorder/reviewer 等）
      │   ├── messages/             ← 消息流（append-only）
      │   │   └── [分层存储]         hot(24h内存) / warm(30d快速存储) / cold(归档)
      │   ├── obligations/          ← 义务状态（活跃义务的权威真源在此）
      │   ├── agenda.json           ← 议程（bounded 场专有，含乐观锁 version 字段）
      │   ├── org_memory.md         ← 组织记忆（group 类型有）
      │   ├── files/                ← 共享文件（group/topic 类型）
      │   └── meta.json             ← status/place_id/last_message_at/deadline 等
      └── ctx_002/ ...
```

**V_A 的形式化定义**：

```
V_A = {
  clock         : Timestamp,        // 单调递增，绝对时钟（唯一真源）
  natural_state : NaturalState,     // 占位，未来扩展（地图、物理环境等）
  interaction_contexts : Map<context_id, InteractionContext>
}
```

#### 2.1.1 FieldProfile（场的参数维度）【L2】

FieldProfile 是 V_A 中每个 InteractionContext 的参数描述块。它是 V_A 的**参数维度**（parameter dimension），不是独立粒子，也不是完整本体（完整 Field 本体见 §1.7）。

FieldProfile 的 **9 个维度**直接驱动运行时行为——维度的取值即其语义，无需另立 if-else 规则：

```
FieldProfile = {
  arity            : solo | dyadic | group | public_many
    // 参与规模
    // solo:        单人（规范约束/消息路由语义退化）
    // dyadic:      双人（对另一方发言默认至少 direct 优先级）
    // group:       多人固定群体（成员制，fan-out 路由）
    // public_many: 不定人数开放人群（禁止 full fan-out，改用订阅+点名）

  co_presence      : co_located | remote | hybrid
    // 共处方式（近似"是否共享同一物理现场"，不单独表达感知带宽）
    // co_located: 物理共处，physical_location + place_id 成为关键耦合变量
    // remote:     远程（视频会议 = remote + synchrony:live）
    // hybrid:     部分同场部分远程

  synchrony        : async | stream | live
    // 交互节奏（影响消息窗口取法和 wakeup 频率）
    // async:  异步，偏摘要+thread heads；recent window 取 warm_messages 摘要
    // stream: 连续消息流，偏 recent messages；recent window 取 hot_messages[-200:]
    // live:   实时连续交互，偏极小 recent slice；recent window 取 hot_messages[-50:]

  lifecycle        : ephemeral | bounded | persistent
    // 生命周期
    // ephemeral:  短暂，参与者散去或消息超时自然结束（无需 conclusion）
    // bounded:    有议程有结论，显式关闭（需检查 agenda + obligation）
    // persistent: 无限存续（论坛/群组）

  task_binding     : none | weak | strong | mission_locked
    // 任务绑定强度（purpose/deliverable/exit_constraint 三层语义的合并近似）
    // none:           无明确任务
    // weak:           有主题但不强制产出
    // strong:         有明确任务或议程（需要 agenda/deliverable）
    // mission_locked: 任务强绑定，不完成不退出；active_key 强制绑定
    // 影响：defaultActiveKey、pending_obligations 是否全量注入 assemble

  visibility       : public | members_only
    // 读权限（v1 只覆盖读权限；写权限/加入权限由 roles+allowedActions 承载）
    // public:       所有人可读（但非成员不在推送列表）
    // members_only: 仅成员可读

  attention_impact : background | engaged | focused | locked
    // 对注意力的占用与外界屏蔽强度（专用于门控计算）
    // 单调性：background→最低外界门槛，locked→最高外界门槛（几乎全部屏蔽）
    // 驱动：fieldExternalThreshold / fieldLocalThreshold / wakeupPolicyFromField
    // 注意：不通过 apply 修改 baseline_attention_policy 基线（L0 约束）

  normative_force  : casual | conventional | formal | binding
    // 规范约束强度（binding 需要配合 ContextState.obligations 才能闭环）
    // casual:      几乎无义务
    // conventional:有惯例但较灵活
    // formal:      有明确规则与行为期待
    // binding:     必须响应/参与/产出（通知 ack_required=true，assemble 义务全量注入）

  cognitive_demand : minimal | moderate | high | intensive
    // 【v6 新增，第9维】该场对 agent 认知资源的实际消耗强度（专用于能量动力学）
    // 与 attention_impact（门控）独立：一个场可以高度屏蔽外界（locked），
    //   但认知负荷不一定极高（如静默专注写作场）；也可能门槛低但持续消耗高（如高强度会议）
    // minimal:   -0.5/h — 轻度协调、社交浏览（论坛、休闲群聊）
    // moderate:  -1.0/h — 常规小组协作、例行会议（普通群聊、日常 chat）
    // high:      -2.5/h — 聚焦讨论、专项协作（焦点任务场、专题会议）
    // intensive: -4.0/h — 高强度决策、危机应对（全员大会、紧急攻关）
    // 驱动：fieldEnergyModifier（唯一驱动维度，统一替代 v5 的 task_binding 版本
    //        和 FA 的 attention_impact 版本，消除二义性）
}

文件系统映射：
  → /world/contexts/ctx_N/field_profile.json（原"8维"→ 现"9维"）
```

**类型对照（向后兼容）**：原 `type` 枚举退居语义标签，不是行为驱动层：

| 原 type | FieldProfile 典型参数组合（含新增 cognitive_demand）|
|---------|--------------------------------------------------|
| forum | arity=public_many, lifecycle=persistent, visibility=public, attention_impact=background, normative_force=casual, **cognitive_demand=minimal** |
| group/space | arity=group, lifecycle=persistent, visibility=members_only, attention_impact=engaged, normative_force=conventional, **cognitive_demand=moderate** |
| chat | arity=dyadic, lifecycle=persistent, visibility=members_only, attention_impact=engaged, normative_force=conventional, **cognitive_demand=moderate** |
| topic | arity=group, lifecycle=bounded, task_binding≥strong, attention_impact=focused, normative_force≥formal, **cognitive_demand=high** |
| 高强度 session | arity=group, lifecycle=bounded, task_binding=mission_locked, attention_impact=locked, normative_force=binding, **cognitive_demand=intensive** |

**三层权限独立说明**：

```
层1（参与权）: arity + participants → 谁能加入这个场
层2（读权限）: field.visibility（public/members_only）→ 谁能读取内容
层3（动作权限）: roles + allowedActions → 谁能发言/主持/评审
// 三层各自独立，不可相互替代
// 例：visibility=public 的论坛（可读）但 participants=closed（不可加入）
//     → 非成员有读权限但无参与权，不收到推送通知但可主动查询
```

#### 2.1.2 InteractionContext 完整结构【L1 + L2】

```
InteractionContext = {
  id           : context_id,

  // ── 场参数化（行为驱动层）──
  field        : FieldProfile,              // 8维场参数（见 §2.1.1）

  // ── 向后兼容（语义标签，不是一等公民）──
  type?        : forum | group | chat | topic,

  // ── 三层权限 ──
  participants : Set<participant_id>,        // closed 时的固定成员集
  roles?       : Map<AgentId, string>,      // host/recorder/reviewer 等

  // ── 生命周期结构 ──
  agenda?      : List<AgendaItem>,          // 仅 bounded 类型有
  obligations? : List<Obligation>,          // normative_force=binding 时（权威真源）
  archived_obligations_ref? : string,       // 归档义务的存储引用（string 类型，非数组）
  conclusion?  : Document,                  // bounded 结束时生成
  status?      : "open" | "active" | "closing" | "closed",
  start_at?    : Timestamp,
  end_at?      : Timestamp,
  deadline?    : Timestamp | null,

  // ── 内容 ──
  messages     : List<Message>,             // 消息流（append-only）
  org_memory?  : Document,                  // 组织记忆（group 类型有，如 ORG.md）
  files?       : Map<file_id, FileState>,   // 共享文件

  // ── 系统字段 ──
  place_id?    : string | null,             // co_located 场的物理锚点（必填）
  last_message_at? : Timestamp | null,      // 用于 ephemeral remote 场关闭检测
  quota?       : { max_notifications_per_minute: number, max_participants: number },
  metadata?    : Map<string, unknown>,      // 含 last_progress_at（有效进展追踪）
}
```

**ContextState 字段必要性约束**（按 FieldProfile 条件，违反时 R_A^apply 无法正确路由）：

```
FieldProfile 条件                              | 必填字段（缺失则行为未定义）
co_presence = co_located                      | place_id（非 null）
lifecycle = bounded                            | agenda, status, start_at, deadline
lifecycle = bounded AND task_binding ≥ strong | metadata.last_progress_at（N-tick 逃生依赖）
normative_force = binding                      | obligations（非空）
normative_force ∈ {formal, binding}           | roles（含 host 角色）
arity = group 且有持久组织记忆               | org_memory
lifecycle = ephemeral AND co_presence = remote | last_message_at（超时关闭检测依赖）
synchrony = live AND task_binding ≥ strong    | quota（防止高频场的通知爆炸）
```

**Obligation（义务）7态状态机**【L2】：

```
Obligation = {
  id                : string,
  source_field_id   : context_id,          // 产生此义务的场
  owner_agent_id    : AgentId,             // 义务执行者
  type              : "respond" | "submit" | "acknowledge" | "attend" | "conclude",

  // 7 态状态机
  state             : "proposed"   // 已提出，等待被接受
                    | "accepted"   // 已接受，等待执行
                    | "active"     // 执行中
                    | "blocked"    // 被依赖阻塞（见 blocking_on）
                    | "done"       // 已完成
                    | "cancelled"  // 已取消
                    | "expired",   // 超时自动过期

  blocking_on?      : string[],    // 阻塞该 obligation 的其他 obligation id
  agenda_item_ref?  : string | null, // 关联的 AgendaItem（完成后自动更新议程项状态）
  deadline?         : Timestamp | null,
  ttl_sec?          : number | null,
  satisfied_by?     : string | null, // 满足该 obligation 的 action id
  metadata?         : Map<string, unknown>,
}

// 权威真源声明：Obligation 的权威存储在 InteractionContext.obligations 内
// C 的 /coupling/active_obligations/ 是其活跃子集的索引（active/accepted/proposed）
// 用于调度器快速查询，不是独立真源
```

**AgendaItem 结构**【L2】：

```
AgendaItem = {
  id              : string,
  title           : string,
  status          : "pending" | "in_progress" | "completed" | "cancelled",
  assigned_to?    : AgentId,
  version         : number,      // 乐观锁版本号，每次成功写入后递增
  updated_at      : Timestamp,

  // 以下字段属于工程补丁 EP16（热点任务队列化分配，防乐观锁饥饿）
  task_binding_mode? : "optimistic_lock" | "queued",  // 默认 optimistic_lock
  claim_queue?    : AgentId[],   // queued 模式下的 FIFO 认领队列
}
// Obligation 完成时的 AgendaItem 联动规则（L2 桥接层）：
// 当所有 agenda_item_ref=X 的 Obligation 均 state=done 时，
// 系统自动将 AgendaItem X 的 status 更新为 "completed"
```

**消息分层存储参数**【L2 + L3】：

```
// 消息按时间分三层存储（L2 结构定义，L3 为具体数值）
MessageStoragePolicy = {
  hot_window_hours        : 24,  // [L3] 最近 24h：内存热存储（context.hot_messages）
  warm_window_days        : 30,  // [L3] 24h-30d：快速存储（context.warm_messages_ref）
  cold_archive_after_days : 30   // [L3] 30d 以上：归档，仅支持语义检索
}

// assemble 时 visibleSlice 按 field.synchrony 取不同窗口（影响 ambient_context 槽位）：
getRecentMessages(context, field):
  field.synchrony = live   → hot_messages[-50:]         // 实时场：最近 50 条
  field.synchrony = stream → hot_messages[-200:]        // 流式场：最近 200 条
  field.synchrony = async  → summarize(warm_messages, maxTokens=1000)  // 异步场：摘要化

// 重要：hot_messages 是从 messages（append-only 序列）切片的派生量，不独立存储
// warm_messages_ref 是持久化到快速存储层的引用，由系统定期归档
```

**ephemeral lifecycle 的关闭语义**【L2】：

```
// co_located + ephemeral（物理对话）：事件驱动
on PhysicalLocationChanged from Bᵢ { from: old_place, to: new_place }:
  for each ctx in contextsAtPlace(old_place):
    if ctx.field.lifecycle == "ephemeral" AND physicalPresenceMembers(ctx.id) == ∅:
      triggerContextClose(ctx.id, reason="all_participants_left")

// remote + ephemeral（线上短暂对话）：Scheduler 定时触发
if now - ctx.last_message_at > EPHEMERAL_TIMEOUT:   // [L3: 具体值待定]
  triggerContextClose(ctx.id, reason="idle_timeout")

// hybrid + ephemeral：物理成员全部离场 OR 消息超时，两者之一满足即可
if physicalPresenceMembers(ctx.id) == ∅ OR (ctx.last_message_at AND now - ctx.last_message_at > EPHEMERAL_TIMEOUT):
  triggerContextClose(ctx.id, reason="hybrid_ephemeral_ended")

// ephemeral 关闭行为（不同于 bounded）：
//   - 不需要 conclusion
//   - 不需要检查 obligation 满足情况
//   - 不需要 agenda 完成
//   - 快速清理，消息 TTL 内保留后自动过期
```

#### 2.1.3 V_A 派生量（不独立存储，实时计算）

```
// 物理同场成员（co_located 场可见性判断 + ephemeral 关闭检测）
physicalPresenceMembers(context_id: ContextId) = {
  Bᵢ | interaction_contexts[context_id].place_id != null
       AND Bᵢ.control.physical_location == interaction_contexts[context_id].place_id
}

// 辅助：通过 place_id 查找所有关联的 co_located context
contextsAtPlace(place_id: string) = {
  c | c ∈ interaction_contexts, c.field.co_presence == "co_located", c.place_id == place_id
}
// 注：physicalPresenceMembers 入参为 context_id（ContextId 类型），
//     不能直接传入 place_id（类型不匹配）

// 数字场参与者（通知路由，不受 foreground 影响）
digitalParticipants(context_id) =
  interaction_contexts[context_id].participants   // 静态成员集，closed 场固定

// 认知前景聚焦成员（local/external 阈值分流判断）
foregroundFocused(context_id) = {
  Bᵢ | Bᵢ.control.foreground_stack.frames[0]?.field_id == context_id
}
// 说明：v5 的 context_current_members = {Bᵢ | location == context_id} 在 v6 失效
// 因为 location 已拆分为 physical_location + foreground_stack
// 三个独立概念各有用途：物理感知、通知路由、注入门槛分流
```

---

### 2.2 耦合介质场粒子 C 的变量（/coupling/ 目录）

```
物理直觉：
  V_C = 粒子间相互作用的介质状态
       类比电磁场——不是 A 和 B 之间的简单缓冲，而是独立的协调实体，
       有自己的状态和演化方程，双向影响 A 和 B

文件系统映射：
  /coupling/
  ├── notifications/              ← 消息通道（耦合信号的传播状态）
  │   ├── agent_1.queue.json     ← agent_1 的待处理通知队列
  │   ├── agent_2.queue.json
  │   └── ...
  ├── resource_leases/            ← 排他性资源租约（发言权、主持权等）【EP12】
  │   └── lease_XXX.json
  ├── writeback_ledger/           ← 写回事件化队列（WriteEvent 暂存）【EP4】
  │   └── pending_events.jsonl
  ├── staging_ledger/             ← 推理意图暂存（PendingIntent）【EP5】
  │   └── intents.json
  ├── active_obligations/         ← 活跃义务索引（从 InteractionContext.obligations 派生）
  │   └── [快速查询用，非权威真源]
  └── scheduler_queue.json        ← 调度协调状态（待触发的唤醒列表）
```

#### 2.2.1 Notification（通知对象）完整结构【L1 + L2】

```
Notification = {
  id              : string,                  // 全局唯一（幂等投递）
  recipient       : AgentId,
  source_event_id : string,
  source_field_id : context_id | null,       // 来源场（用于 local/external 分流）

  priority        : "ambient" | "mention" | "direct" | "urgent",

  semantic_class  : "ambient"        // 环境背景噪声
                  | "mention"        // 被点名提及
                  | "direct"         // 直接指向 recipient 的事件
                  | "alarm"          // 系统报警（服务宕机、超时等）
                  | "reactive_local" // 物理同场本地触发（低头族被点名）
                  | "hazard"         // 自然灾害、火灾等物理危险
                  | "evacuation"     // 强制撤离指令
                  | "emergency",     // 系统性全局紧急事件
                  // hazard/evacuation/emergency 可触发 Life Threat Override（见 §12.3）

  status          : "pending"        // 已入缓冲，等待注入
                  | "surfaced"       // 已被注入某次 assemble（"看到了"，不等于"处理了"）
                  | "acknowledged"   // agent 显式确认（"实际处理了"）
                  | "merged"         // 与相同 collapse_key 的通知合并
                  | "archived"       // 正常归档
                  | "expired",       // TTL 到期

  collapse_key    : string | null,   // 相同 key 的通知合并（防通知爆炸）
  ttl_sec         : number,          // 必须有终局，禁止永久 pending
  ack_required    : boolean,
  deadline        : Timestamp | null,
  modality        : "message" | "call" | "system" | null,
  created_at      : Timestamp,
  surfaced_count  : number,          // 被 surfaced 的次数（可多次）
}

// 强约束：每条通知必须有终局（merged/archived/expired 至少其一），
//         禁止存在无 TTL 的永久 pending 通知

// 重要区分：surfaced ≠ acknowledged
// 一条通知可多次 surfaced（看到了但未处理），
// 只有出现在 Output.acknowledged_notif 中才算 acknowledged，随后立即归档
```

**通知终局迁移规则**（C 的 R_C^apply 执行）：

```
// 规则1：acknowledged → 立即归档
on notification.status → "acknowledged":
  scheduleArchive(notification, delay=0)

// 规则2：surfaced 但长期未 acknowledged → TTL 过期
if notification.status == "surfaced" AND notification.surfaced_count >= MAX_SURFACE_COUNT:
  notification.status = "expired"   // [L3: MAX_SURFACE_COUNT 建议值 5]

if now > notification.created_at + notification.ttl_sec:
  notification.status = "expired"

// 规则3：ack_required=true 且超过 deadline → 触发 ObligationTimeout
if notification.ack_required AND notification.deadline AND now > notification.deadline:
  if notification.status NOT IN {acknowledged, archived, expired, merged}:
    emit ObligationTimeout { notification_id, agent_id }

// 规则4：collapse_key 去重 → merged
on new notification with same collapse_key as pending/surfaced notification:
  older.status = "merged"
  // 新通知继承 older 的 surfaced_count
```

#### 2.2.2 ResourceLease（排他性资源租约）【EP12，见工程补丁章节】

```
ResourceLease = {
  resource_id : string,               // 如 "speaking_right:ctx_123"
  holder      : AgentId,
  token       : number,               // 版本令牌，每次续约递增
  lease_until : Timestamp,
  purpose     : string,               // 资源用途描述
}
// 文件系统映射 → /coupling/resource_leases/lease_XXX.json
```

#### 2.2.3 WriteEvent（写回账本事件）【EP4，见工程补丁章节】

```
WriteEvent = {
  event_id        : string,
  idempotency_key : string,           // 防止重复应用
  causal_parent_ids : string[],       // 因果依赖的前驱事件 ID
  actor_id        : AgentId,
  target_object   : string,           // 写回目标（如 "context:xxx.agenda[0]"）
  intent          : string,           // 意图描述
  effect_patch    : Map<string, unknown>,  // 变更内容
  created_at      : Timestamp,
  applied_at?     : Timestamp | null, // 物化时间，null 表示尚未物化
}
// WritebackLedger 是 Global Event Log 中 AgentEvolved 事件的细粒度展开
// 不是独立的第二真源；WriteEvent.round_id 反向关联到对应的 AgentEvolved.event_id
```

#### 2.2.4 PendingIntent（推理意图暂存）【EP5，见工程补丁章节】

```
PendingIntent = {
  id          : string,
  agent_id    : AgentId,
  action_id   : string,               // 等待此 action 的 ActionReceipt
  write_events : WriteEvent[],        // LLM 生成的推理意图（durable 暂存）
  created_at  : Timestamp,
  ttl_sec     : number,               // 意图有效期（默认 3h），超时 → abandoned
  status      : "pending" | "committed" | "abandoned",
}
// 文件系统映射 → /coupling/staging_ledger/intents.json
// 必须在 commitWriteEvents（ActionReceipt 到来）之前持久化
```

---

### 2.3 智能体粒子 Bᵢ 的变量（/agents/agent_i/ 目录）

```
物理直觉：
  V_Bᵢ = 智能体的主观状态（记忆、控制、能力）

文件系统映射：
  /agents/agent_i/
  ├── memory/                     ← 记忆库（KeyedMemoryStore）
  │   ├── self.json               ← 自我模型（identity, persona）[persistent, pinned]
  │   ├── entity_<id>.json        ← 对某实体的建模（user/peer agent）[long, retrievable]
  │   ├── date_YYYY-MM-DD.json    ← 每日工作记忆 [working, pinned]
  │   ├── context_<id>.json       ← 工作上下文记忆 [working, pinned when active]
  │   └── topic_<label>.json      ← 语义长期记忆 [long, retrievable]
  ├── control.json                ← 控制状态（ControlState，见 §2.3.2）
  └── capability.json             ← 能力描述（tools + skills）
```

**V_Bᵢ 的形式化定义**：

```
V_Bᵢ = {
  memory     : KeyedMemoryStore,   // 记忆库（统一承载原 self_model/other_model/memory）
  control    : ControlState,       // 控制状态
  capability : CapabilityModel,    // 工具与技能
}
```

#### 2.3.1 Memory Store（键控记忆库）【L1 + L2】

```
KeyedMemoryStore = Map<MemoryKey, MemoryEntry>

MemoryKey 类型（典型范式）：
  self                     // 自我模型（identity, persona）
  entity:<entity_id>       // 对某个实体的建模（user, peer agent）
  date:<YYYY-MM-DD>        // 每日工作记忆
  context:<context_id>     // 工作上下文（任务、协作项目）
  topic:<topic_label>      // 语义长期记忆（按主题/领域）

MemoryEntry = {
  content     : Document | StructuredDocument,
                // 对大多数 key：自由文本
                // 对固定 key（如 self）：允许结构化 schema
                //   访问时可通过 field path：memory[self].identity / memory[self].persona.public_summary
  tier        : persistent | long | working,   // 更新频率/衰减属性
  access      : pinned | retrievable,          // 如何进入 assemble 上下文
  mutability  : self_writable | system_derived | protected,

  // 域隔离字段（工程补丁 EP7，但在此处定义为结构字段）
  source_field_id        : context_id | null,  // 产生该记忆时的前景场（系统自动填入，LLM 不填写）
                                                // null = 无场产生（休息/散步时的内在洞察）
  source_activity_preset : string | null,      // 产生时的活动 preset（调试用）
  created_at             : Timestamp,
  cross_context_relevance : number | null,     // 跨场相关性分数 [0,1]（null=未评估）
}
```

**各原类别到 Memory Store 的映射**：

| 原类别 | MemoryKey | tier | access |
|--------|-----------|------|--------|
| self_model.identity | `self` | persistent | pinned（摘要注入）|
| self_model.persona | `self` | persistent | pinned（摘要注入）|
| other_model.user_profile | `entity:user_id` | long | retrievable |
| other_model.peer_models | `entity:agent_id` | long | retrievable |
| long_memory.semantic | `topic:*` | long | retrievable |
| long_memory.episodic | `date:*`（历史）| long | retrievable |
| working_memory.daily_mem | `date:today` | working | pinned（全文）|
| working_memory.workspaces | `context:ctx_id` | working | pinned when active |

**访问规则**：
- `pinned + date:today`：每次 assemble 必然注入（今日记忆全文 + 自我模型摘要）
- `pinned + context:* where context==active_key`：活跃工作上下文全文注入
- `retrievable`：通过相关性检索注入（检索机制在附录 F 留待收敛）
- `date:recent_N_days`：近 N 日记忆以摘要方式注入

#### 2.3.2 ControlState（控制状态）完整定义【L1 + L2】

```
// MoodState（v5/v6 二维结构，替代 MoodEnum）
MoodState = {
  valence    : -2 | -1 | 0 | 1 | 2,   // 情感效价（-2=极负 .. +2=极正）
  activation : 0 | 1 | 2 | 3 | 4,     // 激活程度（0=沉睡 .. 4=亢奋）
  label?     : string                   // 可选语义标签（"stressed", "calm" 等）
}

// ForegroundFrame（认知前景栈帧）
ForegroundFrame = {
  field_id   : context_id | null,      // 该帧的前景场（null=无数字前景）
  active_key : string | null,
  object_ref : string | null,
  entered_at : Timestamp,
  switch_reason : "agent_action" | "user_explicit" | "system_p0" | "location_change" | "preemption",
  returnable : boolean,
  interrupted_activity_snapshot : ActivityInstance | null  // push 时保存被打断的活动快照（含 resume_token）
}

// ForegroundStack（认知前景栈，替代单值 foreground_field_id）
ForegroundStack = {
  frames         : ForegroundFrame[],  // frames[0] 是当前生效前景（栈顶）
  cooldown_until : Timestamp | null,   // 切换冷却【EP14】
  last_switch_at : Timestamp | null
}

// ResumeToken（中断恢复令牌，由系统在 pushForeground 时生成，LLM 不感知内容格式）
ResumeToken = {
  resumed_context_id    : context_id | null,
  resumed_active_key    : string | null,
  context_snapshot_hash : string,      // 验证 Compaction 是否发生
  created_at            : Timestamp,
  expires_at            : Timestamp    // TTL 推荐 24h
}

// ActivityRuntimeProfile（7维门控，ActivityInstance 的内部成员）
ActivityRuntimeProfile = {
  mode             : thinking | reading | writing | discussing | listening |
                     browsing | resting | entertaining | exercising | sleeping |
                     recovering | working,
                     // 含义：当前主体主要在做什么
                     // 影响：energy 演化方向、next_wakeup 默认策略、记忆写回类型

  medium           : none | phone | computer | face_to_face | voice_call | mixed,
                     // 含义：当前主导输入输出接口（合并近似：设备类型+交互通道+身体耦合方式）
                     // 注：与 field.co_presence 不同——co_presence 描述场参与者的共处，
                     //     medium 描述 agent 当前如何行动

  input_openness   : open | filtered | narrow | closed_except_urgent,
                     // 含义：对外界非中断式输入的开放程度
                     // 影响：哪些通知可进入本轮上下文

  output_bandwidth : minimal | light | normal | high,
                     // 含义：当前适合输出多少/什么复杂度的动作（量级约束，非能力上限）

  interrupt_tolerance : high | medium | low | urgent_only,
                     // 含义：当前活动能承受多强的打断
                     // 影响：调度器唤醒门槛中的活动贡献项

  energy_profile   : recovering | neutral | consuming | highly_consuming,
                     // 含义：该活动对 energy 更新的系统性倾向（非状态值！）
                     // recovering→+5/h, neutral→±0.5/h, consuming→-3/h, highly_consuming→-6/h

  mobility         : free | limited | anchored,
                     // 含义：当前活动对场所/上下文切换的容忍度（合并近似：物理移动+认知场切换）
}

// ActivityInstance（活动的完整运行时对象，替代 ActivityType 枚举）
ActivityInstance = {
  preset              : string,                  // 活动预设名称（LLM 只能输出此字段）
  profile             : ActivityRuntimeProfile,  // 由系统从 preset 展开（LLM 不直接填写）
  object_ref          : string | null,           // 当前工作对象引用
  toolset             : string[],                // 使用中的工具集
  writeback_policy    : "light" | "normal" | "task_bound" | "conversation_bound",
  reservation_until   : Timestamp | null,        // 专注保留窗口（类比"日历预约"）
  resume_token        : ResumeToken | null,       // 被中断后的恢复令牌（系统生成）
}

// ControlState 完整定义（v5/v6 权威版本）
ControlState = {
  // ── 位置（双分离，替代 v4 单值 location）──
  physical_location   : string | null,           // 物理所在地（place_id），null=无物理锚点

  // ── 认知前景场（栈结构，替代单值 foreground_field_id）──
  foreground_stack    : ForegroundStack,          // frames[0].field_id 是当前生效前景

  // ── 活动（ActivityInstance，替代 ActivityType 枚举）──
  current_activity    : ActivityInstance,

  // ── 注意力门控（分层设计）──
  baseline_attention_policy : "ambient" | "mention" | "direct" | "urgent",
  // 这是 agent 的绝对人格基线，只允许 LLM/用户显式修改
  // 场进入/活动切换/系统周期调用均不得修改此字段（L0 约束）
  // 场/活动只参与 RuntimeEnvelope 的瞬时 maxPriority 计算，不写回基线

  current_stress_modifier : 0 | 1 | 2,           // 应激附加值（系统可自动衰减，不触碰基线）
  last_high_stress_at : Timestamp | null,         // 追踪最近进入高压场的时间

  // ── 工作上下文 ──
  active_key          : MemoryKey | null,         // 权威真源；ForegroundFrame.active_key 是快照

  // ── 调度 ──
  next_wakeup         : Timestamp,               // 不变量：不得为 null
  last_evolved_at     : Timestamp,               // Δt 计算基准
  wakeup_refractory_until : Timestamp | null,     // 唤醒不应期（防高频抢占）

  // ── 执行资源 ──
  energy              : Float[0,100],
  mood                : MoodState,               // 二维结构（valence × activation）

  // ── 能量安全锁（工程补丁 EP1 引入的字段，但在此处定义为结构成员）──
  energy_recovery_lock : boolean,                // Hysteresis Lock 激活标志
  consecutive_above_critical_hours : number,     // 连续保持 energy >= CRITICAL 的小时数

  // ── 睡眠跟踪 ──
  sleep_start         : Timestamp | null,
  sleep_target_hours  : number | null,           // 由 onEnterSleeping 智能设置【EP9】
  sleep_debt_hours    : number,                  // [0, 24]，【EP8】

  // ── 资源租约引用 ──
  active_lease_refs   : string[],                // 持有的 ResourceLease resource_id 列表
}
```

**便捷访问器**（运行时提供，非 durable 字段）：

```
foreground_field_id = foreground_stack.frames[0]?.field_id ?? null  // 向后兼容别名
activity_profile    = current_activity.profile
attention_policy    = baseline_attention_policy   // 向后兼容别名
```

**raisePriority（基线+应激的加法形式化）**：

```
// 四级优先级数值映射
PRIORITY_LEVELS = { ambient: 0, mention: 1, direct: 2, urgent: 3 }

// 饱和加法（超出 urgent 则停在 urgent）
raisePriority(base: Priority, stress_modifier: 0|1|2): Priority =
  idx = PRIORITY_LEVELS[base] + stress_modifier
  return PRIORITY_LEVELS_INVERSE[ min(idx, 3) ]
  // stress_modifier=0: 不提升；=1: 上调一级；=2: 上调两级（最多到 urgent）
```

**null foreground 时的虚拟场语义**（foreground_field_id=null 时的 fallback）：

```
virtualFieldFromActivity(activity: ActivityInstance): VirtualFieldProfile =
  mode = activity.profile.mode
  match mode:
    sleeping, recovering, resting → { attention_impact: "background", task_binding: "none" }
    thinking, writing, working    → { attention_impact: "focused",    task_binding: "weak" }
    exercising                    → { attention_impact: "locked",     task_binding: "none" }
    default                       → { attention_impact: "background", task_binding: "none" }

// 使用规则：foreground_field_id = null 时，
//   fieldExternalThreshold 等函数使用 virtualFieldFromActivity(current_activity)
//   ambient_context 的部分 A（前景场消息）为空
//   source_field_id=null 的记忆被视为"与虚拟场同域"，优先注入
```

**有效门槛计算**（ephemeral，每轮 assemble 前计算，不写回基线）：

```
current_field_profile = (foreground_field_id != null)
  ? interaction_contexts[foreground_field_id].field
  : virtualFieldFromActivity(current_activity)

effective_threshold = maxPriority(
  raisePriority(baseline_attention_policy, current_stress_modifier),
  fieldExternalThreshold(current_field_profile),
  activityInputThreshold(current_activity.profile),
  activityInterruptThreshold(current_activity.profile)
)
// 此值只在本轮生效，不写回任何 control 字段
```

#### 2.3.3 ActivityInstance 参数维度说明【L2】

`ActivityRuntimeProfile` 是 Activity 完整本体的运行时投影（runtime projection），不是完整本体本身（完整本体见 §1.7）。它是 ControlState 的参数维度，类比于 FieldProfile 之于 InteractionContext。

**LLM 约束**：
- LLM 只能在 `control_delta` 中输出 `activity_preset` 字符串名称
- 系统（R_B^apply）负责展开为完整 ActivityInstance
- 禁止 LLM 直接输出 ActivityRuntimeProfile 的完整 JSON（防止内部矛盾维度组合）
- `resume_token` 由系统在 pushForeground 时自动生成，LLM 不填写
- 自定义活动（非预设）须经 ActivityValidator 验证合法性后才可写入

**活动预设完整参数表**（9列 = 7维 + writeback_policy + object_ref 语义）：

```
preset               | mode      | medium     | input_openness       | output_bandwidth | interrupt_tolerance | energy_profile   | mobility  | writeback_policy     | object_ref 语义
---------------------|-----------|------------|----------------------|------------------|---------------------|------------------|-----------|----------------------|-----------------
using_phone          | browsing  | phone      | open                 | light            | high                | neutral          | free      | light                | null（浏览无特定对象）
using_computer       | working   | computer   | filtered             | normal           | medium              | consuming        | limited   | normal               | 当前工作文档/任务（可选）
writing_on_computer  | writing   | computer   | narrow               | high             | low                 | consuming        | anchored  | task_bound           | "doc:<doc_id>"（写作文档）
thinking_deep        | thinking  | none       | narrow               | minimal          | urgent_only         | consuming        | anchored  | task_bound           | "topic:<topic>"（思考议题，可选）
resting              | resting   | none       | filtered             | minimal          | medium              | recovering       | limited   | light                | null
exercising           | exercising| none       | closed_except_urgent | minimal          | urgent_only         | highly_consuming | free      | light                | null
recovering           | recovering| none       | narrow               | minimal          | low                 | recovering       | anchored  | light                | null
sleeping             | sleeping  | none       | closed_except_urgent | minimal          | urgent_only         | recovering       | anchored  | light                | null
discussing           | discussing| face_to_face/voice_call | filtered | normal  | medium              | consuming        | anchored  | conversation_bound   | null（对象由场提供）
```

**writeback_policy 含义**：
- `light`：只写关键决策，energy 状态记录；休息/睡眠类活动
- `normal`：常规工作观察和结论，通用工作活动
- `task_bound`：优先写入 `memory[context:*]`；深度任务类活动（写作/思考）
- `conversation_bound`：优先写入 `memory[entity:*]` 和 `memory[date:*]`；讨论/协作类活动

**ActivityProfile 合法性约束**（ActivityValidator 的判断依据）：

```
维度条件                                        | 约束                                    | 原因
mode = writing                                 | medium ∈ {computer, phone}              | 写作需要可输出的接口
mode = discussing / listening                  | medium ∈ {face_to_face, voice_call, phone} | 口头交互需对应媒介
mode = sleeping / recovering                   | output_bandwidth ≠ high                 | 睡眠/恢复不做高带宽输出
mode = sleeping                                | input_openness ≠ open                   | 睡眠状态不主动开放输入
medium = none                                  | output_bandwidth = minimal              | 无外部接口则无外部输出
energy_profile = recovering                    | mode ∈ {sleeping, resting, recovering}  | 正能量只来自休息类活动
energy_profile = highly_consuming              | mode ∈ {exercising, working（极限）}    | 极高消耗只来自高强度活动
mobility = anchored + output_bandwidth = high  | 需确认当前场支持高带宽输出              | anchored 状态的高带宽输出需场允许
energy_profile = highly_consuming + output_bandwidth = high | 系统监控 energy 下降速率 | 双高组合需要告警

违反处理：
  LLM 输出的 preset 名称不存在于预设表 → 系统拒绝，保持上一轮 current_activity
  自定义 profile 不满足合法性约束 → ActivityValidator 降级到最近合法预设并告警
```

#### 2.3.4 Capability Model【L1】

```
CapabilityModel = {
  tools  : ToolSpec,        // 外部可调用的 affordances（相对稳定，管理员设置）
  skills : List<Skill>,     // 内部可组合的 procedures/policies（可通过学习获取）
}

// tools：由系统管理员通过 system-maintenance event 更新，agent 不自主修改
// skills：可通过 acquire_skill(skill_id) 动作更新
//         注意：acquire_skill 不属于 B→A 写操作，而是 B 对自身 V_B.capability 的更新
//               需要系统授权事件，通过 system-maintenance event 路径，不经过 R_A^apply
// assemble 时：以 capability_summary 形式注入（非全量）
```

---

## 第三章：耦合拓扑【L0 + L1】

> **物理直觉**：粒子之间通过什么路径相互影响？
>
> **文件系统映射**：哪些目录之间允许数据流？方向和类型是什么？

### 3.1 事件发射拓扑

```
允许的事件发射路径（写隔离原则内的合法跨粒子影响）：

  Bᵢ → V_A ：智能体的动作更新世界状态（发消息、更新 context 等）
  Bᵢ → V_C ：智能体的动作更新耦合场（触发通知创建、义务更新等）
  A  → V_C ：世界时钟/事件触发耦合场广播（WorldEvent → NotificationRequest）
  C  → V_Bᵢ：耦合场向智能体投递（通知到达 → 写入 agent_i.queue.json）
              注：C 不能直接写 V_Bᵢ 的本体字段（如 memory/control）
              C 只能向 Bᵢ 的队列追加通知，或触发 Bᵢ 的调度唤醒
  C  → V_A ：耦合场请求世界层执行规则（如：义务满足 → context 关闭请求）
              注：C 发送 ContextCloseRequest → A 的 R_A^apply 处理（A 自己修改 V_A）

禁止的路径（严禁，无例外）：
  A  → V_Bᵢ：直接写智能体状态                ← 禁止
  Bᵢ → V_Bⱼ：直接写其他智能体（i ≠ j）        ← 禁止
  C  → V_A（R_A^apply 以外的直接写）          ← 禁止
  C  → V_Bᵢ（notifications/queue 以外的直接写）← 禁止
```

### 3.2 事件流的文件系统映射

```
"Bᵢ → V_A" 的实现：
  Bᵢ 产出 Output
  → R_Bᵢ^emit 创建事件（写入 /event_log/）
  → R_A^apply 读取事件 → 修改 /world/contexts/...

"Bᵢ → V_C" 的实现：
  Bᵢ 产出 Output
  → R_Bᵢ^emit 创建事件
  → R_C^apply 读取事件 → 修改 /coupling/...

"C → V_Bᵢ（通知投递）" 的实现：
  R_C^apply 计算路由结果
  → 将通知追加到 /coupling/notifications/agent_i.queue.json
  → 下次 Bᵢ 演化时，R_Bᵢ^assemble 读取该文件作为输入

"C → V_A（场关闭请求）" 的实现：
  R_C^apply 产出 ContextCloseRequest 事件
  → 发射到 R_A^apply
  → A 自己修改 /world/contexts/ctx_id/meta.json

"/event_log/ 是所有事件的唯一真源（append-only）"：
  任何跨目录的数据流都必须先在 event_log 中留下记录
  系统状态可从 event_log 完整重建（crash recovery 的前提）
```

---

## 第四章：可见性（Visibility）【L1】

> **物理直觉**：每个粒子在演化时能"观察到"什么？不同观察者看到的是同一系统的不同投影。
>
> **文件系统映射**：当某粒子的演化函数被调用时，它能读取哪些目录下的哪些文件？

### 4.1 View_B（智能体对外暴露）

```
View_Bᵢ(observer) = {
  // 所有人可见
  memory[self].identity,

  // 世界层物理位置耦合（co_located 场感知）
  control.physical_location,

  // 世界层认知前景（通知路由分流：local vs external）
  control.foreground_stack.frames[0]?.field_id,

  // 世界层通知路由（唤醒阈值）
  control.baseline_attention_policy,

  // 可选公开
  control.current_activity.profile.mode,
  memory[self].persona.public_summary   // 若 persona 设置了 public_summary
}
```

### 4.2 View_A（世界对外暴露）

```
View_A(Bᵢ) = {
  clock,
  time_of_day,                          // 派生量

  // 开放上下文：摘要 + 近期切片（非全量对象正文）
  public_context_windows = {
    { id: c.id, type: c.type,
      summary: computeSummary(c),        // 派生：从消息+组织记忆生成摘要
      messages: c.messages[-K:] }
    ∣ c ∈ interaction_contexts,
      c.field.visibility == "public"
  },

  // 成员可见内容（通过 participants 集合判断成员资格）
  { c.messages[-K:], c.org_memory
    ∣ c ∈ interaction_contexts
    AND Bᵢ ∈ c.participants
    AND c.field.arity ∈ {dyadic, group} },

  notification_buffer[i]               // 仅 Bᵢ 自己可见（通过 C 传递）
}

// 原则：View_A(Bᵢ) 暴露的是可见查询窗口（摘要+切片），非默认全量对象正文

// 物理场与认知前景场并存时的可见性（"低头族"场景）：
// 若 Bᵢ.physical_location 对应的 co_located context ≠ foreground_stack.frames[0].field_id
// 则 View_A(Bᵢ) 同时暴露两个场的可见切片：
//   - 认知前景场（主）：interaction_contexts[foreground_field_id].messages[-K:]
//   - 物理所在场（背景感知）：interaction_contexts[physical_location_context_id].messages[-K:]（轻量切片）
// 这保证 agent 即使认知聚焦于数字场，也能感知物理现场的 reactive_local 类事件
```

### 4.3 View_C（耦合场对外暴露）

```
View_C(Bᵢ) = {
  notifications/agent_i.queue.json    // 只能看自己的通知队列
}

View_C(A) = {
  scheduler_queue.json,               // 调度状态（A 用于判断是否触发）
  路由元信息                           // 广播策略
}

View_C(调度器元设施) = {
  /coupling/ 完整内容                  // 调度器有权查看所有耦合状态
}
```

### 4.4 Assemble 时的文件读取清单（Bᵢ 演化输入）

```
当智能体 Bᵢ 的 LLM API 被调用时，读取的文件清单（按 assemble 规则）：

从 /agents/agent_i/ 读取（自身状态）：
  pinned_memory：
    - memory/self.json（摘要）            ← 永远注入（identity, persona）
    - capability_summary                  ← 工具+技能描述（摘要化）

  control_state（全量）：
    - control.json                        ← 包含 physical_location/foreground_stack/
                                             current_activity/baseline_attention_policy/
                                             current_stress_modifier/energy/mood/
                                             active_key/next_wakeup/energy_recovery_lock 等

  active_workspace（按 active_key 检索）：
    - memory/context_<active_key>.json（全文，若 active_key 非 null）

  recent_working_memory：
    - memory/date_today.json（全文）      ← 永远注入
    - memory/date_<recent_N>.json（摘要） ← 近 N 日记忆窗口

  retrieved_long_memory（检索，非全量）：
    - memory/topic_* / memory/entity_*（与当前上下文相关的片段）

从 /world/ 读取（通过 View_A 过滤）：
  ambient_context（认知前景场 + 物理所在场，取并集）：
    部分 A：前景场内容（主要场景）
      - interaction_contexts[foreground_field_id].messages[-K:]
      - interaction_contexts[foreground_field_id].org_memory（若有）
    部分 B：物理所在场（低头族场景的附加感知）
      - 若 physical_location 对应的场 ≠ foreground_field_id：
        interaction_contexts[physical_location_context_id].messages[-K:]（轻量切片）

  pending_obligations（条件性，全量注入，不可被 context window 截断）：
    - 条件：field.task_binding ∈ {strong, mission_locked}
            OR field.normative_force == binding
    - 内容：当前场内 agent 的所有 active/accepted/proposed Obligation

从 /coupling/ 读取（通过 View_C 过滤）：
  surfaced_notifications：
    - notifications/agent_i.queue.json 中满足 canInject(e, Bᵢ) 的通知

注意：
  - 没有任何文件以全量原始文本方式无条件注入，每个槽位都有摘要化/检索化/过滤规则
  - pending_obligations 是唯一例外：全量注入，不被 context window 截断（身份优先义务过滤）

assemble 与 FieldProfile 的参数化关系：
  field.synchrony = async        → ambient_context 偏摘要+线索头，减少实时切片
  field.synchrony = live         → ambient_context 仅最近 N 条（N 极小）
  field.task_binding = strong    → active_workspace 强制完整注入，不因 window 截断
    OR mission_locked            → pending_obligations 全量注入
  field.normative_force=binding  → pending_obligations 全量注入（身份优先义务过滤）
  field.attention_impact=locked  → 外部通知门槛提升（压制场外干扰）
```

---

## 第五章：状态空间【L1】

> **物理直觉**：系统的完整状态由哪些部分构成？
>
> **文件系统映射**：哪些文件的总和完整描述系统在任意时刻的状态？

### 5.1 S_global（粒子本体真状态空间）

```
S_global(t) = V_A(t) × V_C(t) × V_B₁(t) × V_B₂(t) × ... × V_Bₙ(t)

文件系统映射：
  = 所有 /world/、/coupling/、/agents/agent_i/ 目录下文件的当前值
```

用于描述"系统目前的自然状态是什么"。

### 5.2 S_total（完整前向演化状态空间）

```
S_total(t) = S_global(t)

// 三粒子架构的关键简化：
// 在 v6 三粒子模型中，notification_buffer、WritebackLedger、StagingLedger、
// ResourceLease、Obligation 活跃集合等 v5 中称为"M_meta^persist"的全部内容，
// 均已归属 V_C（耦合介质场粒子的本体变量）。
// 因此 S_global = S_total，不再需要附加 M_meta^persist 项。
//
// 这是三粒子模型相比 v5 双粒子模型的重大简化：
//   v5: S_total = S_global × notification_buffer × M_meta^persist
//   v6: S_total = S_global = V_A × V_C × V_B₁ × V_B₂ × ... × V_Bₙ
//
// 其中 V_C 包含：
//   /coupling/notifications/       ← 原 notification_buffer
//   /coupling/scheduler_queue.json ← 原 Scheduler.pending_wakeups
//   /coupling/writeback_ledger/    ← 原 WritebackLedger
//   /coupling/staging_ledger/      ← 原 StagingLedger
//   /coupling/resource_leases/     ← 原 ResourceLease 状态集合
//   /coupling/active_obligations/  ← 原 Obligation 活跃集合索引
```

### 5.3 三粒子架构对状态空间的简化意义

```
v5（双粒子）的问题：
  S_global(t) = V_A(t) × V_B₁(t) × ... × V_Bₙ(t)
  但系统的前向演化还依赖 notification_buffer（它不属于任何粒子），
  因此必须引入 S_total = S_global × notification_buffer × M_meta^persist。
  这造成系统状态空间在概念上"有一部分游离于粒子之外"，显得不干净。

v6（三粒子）的解决：
  将 notification_buffer 和 M_meta^persist 归入粒子 C（耦合介质场粒子）的本体变量 V_C。
  S_global 本身即为完整前向演化状态：S_global = S_total。
  系统的所有状态都有明确的"持有者"（A、C 或某个 B），概念干净。

文件系统映射的直接意义：
  任意时刻系统的完整状态 = /world/ + /coupling/ + /agents/*/
  没有任何状态"游离"在三个顶级目录之外（event_log 是审计投影，不是前向演化状态）
```

**两层日志的粒度关系**：

```
WritebackLedger 中的 WriteEvent（字段级变更）
是 Global Event Log 中某轮 AgentEvolved 事件的细粒度展开，不是独立的第二真源。
WriteEvent.round_id 反向关联到对应的 AgentEvolved.event_id。
```

---

## 第六章：变量类型分类【L1】

> 系统中所有变量（文件）属于以下七种类型之一：

| 类型 | 定义 | 文件系统类比 | 写入者 | 例子 |
|------|------|------------|--------|------|
| **本体变量** | 粒子的独立真状态，只有粒子自身或授权外力可写 | 粒子目录下的读写文件 | 粒子自身 | `V_A.clock`, `V_B.memory` |
| **参数维度** | 描述粒子/context 性质的参数字段，是 V_P 的内部结构，不是独立粒子 | 配置文件（影响系统函数行为）| 随所属粒子 | `FieldProfile`（V_A 的参数）, `ActivityRuntimeProfile`（V_B 的参数）|
| **派生变量** | 由本体变量完全决定，不独立存储，实时计算 | 从文件动态计算的值（无存储）| 无（只读）| `physicalPresenceMembers(ctx)`, `time_of_day`, `foreground_field_id` |
| **可见视图** | 本体变量经访问规则后的投影函数 | 只读挂载点（不存储）| 无（只读）| `View_A(Bᵢ)`, `View_C(Bᵢ)` |
| **耦合缓冲变量** | 粒子间的中间容器，属于 V_C，不属于 V_A 或 V_B | /coupling/ 目录下的文件 | C 自身 | `notifications/agent_i.queue.json` |
| **工程辅助变量** | 工程补丁引入的额外追踪字段，理想连续动力学中不必要 | 监控/临时状态文件 | 系统外力（非 LLM）| `sleep_debt_hours`, `energy_recovery_lock`, `consecutive_above_critical_hours` |
| **审计投影** | Global Event Log 的派生查询视图，非独立本体，不独立存储 | 无（按需从 event_log 重建）| 无（只读）| `event_log.filter(actor==Bᵢ, date==today)` + aggregate |

**注意**：`system_trace` 不是独立本体对象，属于审计投影。不存在"向 system_trace 写入"的操作，只有"向 Global Event Log 追加结构化事件"。在三粒子架构中，`notification_buffer`（原 v5 的耦合缓冲）已归入 V_C 成为本体变量，由"耦合缓冲变量"这个类型承载。

---

## 第七章：本体层与运行时投影的降维声明【L1】

> 说明 FA 模型中的"运行时 schema"与完整本体的关系，避免误将投影当作完整本体。

**Field 完整本体（理论层）**：

```
完整 Field 本体定义为 f = (P, A, U, Q, V, B, T, K)：
  P: Purpose     ← 场的目的/议题/stakes
  A: Actors      ← 参与者与角色集合
  U: Rules       ← 显性规则与隐性规范
  Q: Power       ← 权力/地位/权限结构
  V: Visibility  ← 信息可见性与暴露机制
  B: Boundary    ← 边界与进出条件
  T: Tempo       ← 时间节奏与响应窗口
  K: Resource    ← 资源、成本、激励结构
```

| 本体维度 | 工程对应（FieldProfile 或 ContextState）| 说明 |
|---------|---------------------------------------|------|
| Purpose (P) | `task_binding`（强度）+ `ContextState.agenda`（内容）| 近似，purpose 内容在 agenda/title |
| Actors (A) | `arity`（规模）+ `ContextState.participants/roles` | 具体 actor 集合在 ContextState |
| Rules (U) | `normative_force`（强度）+ `ContextState.obligations` | 规则内容在 obligation 对象 |
| Power (Q) | `ContextState.roles`（部分）| **v1 FieldProfile 无直接对应维度** |
| Visibility (V) | `visibility` | 当前只覆盖读权限 |
| Boundary (B) | `lifecycle`（时间）+ `co_presence`（空间）+ `visibility`（访问）| 三维共同近似 |
| Tempo (T) | `synchrony` | 映射较好 |
| Resource (K) | **v1 未建模** | 后续版本议题 |

**Activity 完整本体（理论层）**：

```
完整 Activity 本体定义为 a = (O, G, M, I, L, Y, W, C)：
  O: Object      ← 当前工作对象
  G: Goal Stack  ← 目标层级
  M: Tools       ← 工具与媒介
  I: Input Policy← 输入采样/过滤策略
  L: Interruptibility ← 可打断性
  Y: Output Policy← 输出样式与通道
  W: Writeback Policy ← 状态写回策略
  C: Transition  ← 完成与切换条件
```

| 本体维度 | 工程对应（ActivityRuntimeProfile 或 ActivityInstance）| 说明 |
|---------|-----------------------------------------------------|------|
| Object (O) | `ActivityInstance.object_ref` | v1.3 已补全 |
| Goal Stack (G) | `ContextState.agenda` + `ControlState.active_key` | 外置承载 |
| Tools (M) | `medium`（主导接口近似）| 不完整表达工具体系 |
| Input Policy (I) | `input_openness` | 映射较好 |
| Interruptibility (L) | `interrupt_tolerance` | 映射良好 |
| Output Policy (Y) | `output_bandwidth` + `mode` | 量级+类型共同近似 |
| Writeback Policy (W) | 由 `mode × medium × field_profile` 隐式派生 | 未显式化为独立字段 |
| Transition (C) | `mobility` + Scheduler 策略 | 合并近似 |

**降维原则**：引入一种新的真实世界交互形态，**不需要新增本体类型**，只需为 FieldProfile 赋一组参数值，并接入通用函数即可。

---

---

# 第二部分：演化方程（Dynamics）【L1 + L2】

> **物理直觉**：系统迭代一次，各粒子的状态如何变化？
>
> **文件系统映射**：每次演化 = 一次函数调用。函数签名：输入哪些文件，执行什么逻辑，修改哪些文件。

---

## 第八章：单次演化的统一结构【L1】

### 8.1 四阶段结构（所有粒子通用）

```
阶段 1：上下文组装（R_P^assemble）
  C(t) = Assemble( V_P(t),  View_Q(P) for accessible Q,  notification_buffer[P] )
  文件系统：读取 V_P + 可见的其他粒子文件 → 输出 context C

阶段 2：演化（R_P^evolve）
  Output(t) = Evolve( C(t) )
  文件系统：对于 B：调用 LLM API（C 是 prompt，Output 是响应）
           对于 A/C：执行确定性函数

阶段 3：事件发射（R_P^emit）
  Events_out(t) = Emit( Output(t) )
  文件系统：将 Output 转化为事件序列，写入 /event_log/，路由到目标方

阶段 4：写回（R_P^apply）
  V_P(t+1) = Apply( V_P(t),  Events_in(t) )
  Events_in = 自身发出的更新事件 + 系统外力事件
  文件系统：修改粒子自己目录下的文件（只有自己可写）
```

### 8.2 时间结构与演化频率

| 粒子 | 演化模式 | 频率 |
|------|---------|------|
| 世界 A | 固定步长 + 事件触发 | 混合型（Hybrid）|
| 基础设施 C | 事件触发 + 内部定时任务 | 混合型（Hybrid）|
| 智能体 Bᵢ | 事件触发 + 时钟触发 | 自适应步长（Adaptive）|

---

## 第九章：世界粒子 A 的演化方程 R_A【L1 + L2】

世界粒子是确定性粒子（无 LLM），assemble/evolve 退化为确定性规则。

**函数签名（文件系统映射）**：

```
输入：/world/ 当前状态 + 来自 Bᵢ 的事件 + 系统时钟
修改：/world/contexts/ 中对应 context 的文件
发射：事件到 /coupling/（触发 C 的通知路由）或 /event_log/
```

### 9.1 visibleSlice 函数【L2】

决定某个 observer 此刻能从某个 context 看到什么：

```
visibleSlice(field: FieldProfile, observer: AgentState, context: ContextState): VisibleContextSlice

// visibility = "public"：observer 可见 context 基本元信息和 public message slice
// visibility = "members_only"：只有满足成员条件的 observer 可见
//   成员条件：
//     isMember = context.participants.includes(observer.id)
//     isSamePhysicalPlace = (field.co_presence == "co_located"
//                            AND context.place_id != null
//                            AND observer.control.physical_location == context.place_id)
//     return isMember OR isSamePhysicalPlace

// 同步性修正（synchrony 影响窗口取法）：
//   async:  偏摘要 + thread heads（减少实时切片）
//   stream: 偏 recent messages
//   live:   仅最近极小 recent slice
```

### 9.2 R_A^emit（主动广播）

```
// 时钟触发（简单广播；未来可扩展为剧情事件、环境广播等）
on clock crosses threshold τ:
  emit WorldEvent { type, ts, recipients, priority }
  → 路由到受影响 Bᵢ 的 notification_buffer（通过 C）
```

### 9.3 R_A^apply（核心演化规则）

```
on event MessagePosted { ctx_id, content, author } from Bᵢ:
  /world/contexts/{ctx_id}/messages/ ← append new message
  /world/contexts/{ctx_id}/meta.json ← update last_message_at = now
  emit NotificationRequest { ctx_id, source_event } → R_C^apply

on event AgendaUpdated / ObligationSatisfied / ... from Bᵢ:
  /world/contexts/{ctx_id}/agenda.json ← 更新对应字段（含 version 乐观锁）
  /world/contexts/{ctx_id}/obligations/ ← 更新义务状态
  emit NotificationRequest → R_C^apply

on event PhysicalLocationChanged { from: old_place, to: new_place } from Bᵢ:
  // physicalPresenceMembers 是派生量，无需写文件
  // 直接发射事件给 C，让 C 处理通知路由和 ephemeral 关闭检测
  emit LocationChangedEvent { agent: Bᵢ, from: old_place, to: new_place } → R_C^apply
  // 发射给 event_log（可回放）
  GlobalEventLog.append({ event_type: "PhysicalLocationChanged", ... })

on event FocusChanged { action, field_id } from Bᵢ:
  // foregroundFocused 是派生量，实时计算，无需写文件
  // local/external 注入阈值分流规则在下次 canInject 时生效
  GlobalEventLog.append({ event_type: "FocusChanged", ... })

on clock tick:
  /world/clock.json ← clock += Δt
  emit ClockTickEvent → R_C^apply
  // C 负责：检查 remote+ephemeral 场超时关闭 + 调度唤醒检查

// 场关闭执行（由 C 请求，A 执行）：
on ContextCloseRequest { ctx_id, reason } from C:
  /world/contexts/{ctx_id}/meta.json ← status = "closed"
  // 归档消息（ephemeral: TTL内保留；bounded/persistent: 完整存储）
  // 生成 conclusion（若 bounded）
  emit ContextClosedEvent { ctx_id, reason } → R_C^apply
  GlobalEventLog.append({ event_type: "ContextClosed", ctx_id, reason, ... })
```

### 9.4 closeCondition 函数【L2】

```
closeCondition(field: FieldProfile, context: ContextState): boolean

lifecycle = persistent → false（默认不关闭）

lifecycle = ephemeral →
  co_located: physicalPresenceMembers(context.id) == ∅
  remote:     now - context.last_message_at > EPHEMERAL_TIMEOUT  // [L3]
  hybrid:     以上两者之一满足

lifecycle = bounded →
  task_binding = weak:         时间到 OR 主持人关闭
  task_binding = strong:       agenda 全完成 OR 主持人明确 close
  task_binding = mission_locked: deliverables 齐全 OR 显式强制中止
  normative_force = binding:   所有 pending_obligations.state ∈ {done/cancelled/expired}

// ephemeral 关闭：不需要 conclusion、obligation 检查、agenda 完成
// bounded 关闭：需要检查 agenda + obligation，生成 conclusion
```

---

## 第十章：耦合介质场粒子 C 的演化方程 R_C【L1 + L2】

C 是确定性粒子，负责所有跨粒子路由、调度协调、资源锁管理。

**重要约束（写隔离修复）**：C 的 R_C^apply **不能直接写** V_A 或 V_B 的本体字段。C 只能：
1. 修改 V_C（/coupling/ 目录下的文件）
2. 发射事件触发 A 或 B 的 R_P^apply 来修改它们自己的 V_P

**函数签名（文件系统映射）**：

```
输入：/coupling/ 当前状态 + 来自 A/B 的事件 + 内部定时任务
修改：/coupling/ 下的各个文件
发射：事件到 R_A^apply（如 ContextCloseRequest）或触发 Bᵢ 的调度唤醒
```

### 10.1 通知路由（NotificationRequest → Notification 投递）

```
on NotificationRequest { ctx_id, source_event } from A:
  // 计算优先级
  for each Bⱼ in digitalParticipants(ctx_id):
    relation = computeRelationInfo(source_event, Bⱼ, ctx_id)
    priority = priorityOf(source_event, field_profile, actor, Bⱼ, relation)
    notif = Notification {
      id: uuid(), recipient: Bⱼ,
      source_event_id: source_event.id,
      source_field_id: ctx_id,
      priority: priority,
      semantic_class: classifySemanticClass(source_event, relation),
      status: "pending",
      ttl_sec: computeTTL(field_profile),
      collapse_key: computeCollapseKey(source_event, ctx_id),
      ...
    }

    // public_many 场：禁止 full fan-out，只对直接点名者发个人通知
    if field_profile.arity == "public_many":
      if source_event.direct_mentions.includes(Bⱼ):
        /coupling/notifications/agent_j.queue.json ← push notif
      // else: 不发通知，Bⱼ 下次 time-driven wakeup 时通过 visibleSlice 主动查询

    // group/dyadic：正常 fan-out（含令牌桶限流【EP20 Notification 幂等投递】）
    else:
      if rateLimiter.allow(Bⱼ, ctx_id):
        /coupling/notifications/agent_j.queue.json ← push notif
      // else: 超限通知进入 batch_queue

  // 判断是否触发即时唤醒
  for each Bⱼ where canWakeup(notif, Bⱼ):
    /coupling/scheduler_queue.json ← push WakeupRequest{ agent: Bⱼ, reason: notif.id }
```

### 10.2 canInject 完整公式【L2】

```
// 1. 判断事件来源（local vs external）
isLocalEvent(e, Bᵢ) = e.source_field_id == Bᵢ.control.foreground_stack.frames[0]?.field_id

// 2. 外部门槛（来自其他场的事件，含场+活动+基线的最严格者）
effective_external_threshold = maxPriority(
  raisePriority(Bᵢ.baseline_attention_policy, Bᵢ.current_stress_modifier),
  fieldExternalThreshold(field_profile),
  activityInputThreshold(activity.profile),
  activityInterruptThreshold(activity.profile)
)

// 3. 场内门槛（来自当前前景场的事件，门槛更低）
effective_local_threshold = maxPriority(
  fieldLocalThreshold(field_profile),
  activityLocalThreshold(activity.profile)
)

// 4. 最终判断
canInject(e, Bᵢ) =
  if isLocalEvent(e, Bᵢ): priority(e) >= effective_local_threshold
  else:                    priority(e) >= effective_external_threshold

// 门槛映射函数（L2，维度语义的一部分）：
fieldExternalThreshold:
  background → "ambient"   // 认知负荷低，外界容易进入
  engaged    → "mention"
  focused    → "direct"
  locked     → "urgent"

fieldLocalThreshold:
  background → "ambient"
  engaged    → "ambient"
  focused    → "mention"
  locked     → "direct"   // 即使 locked 场，场内事件仍可在 direct 级进入

activityInputThreshold:
  open                 → "ambient"
  filtered             → "mention"
  narrow               → "direct"
  closed_except_urgent → "urgent"

activityLocalThreshold:
  open                 → "ambient"
  filtered             → "ambient"
  narrow               → "mention"
  closed_except_urgent → "direct"

activityInterruptThreshold:
  high        → "ambient"
  medium      → "mention"
  low         → "direct"
  urgent_only → "urgent"
```

### 10.3 canWakeup 完整公式【L2】

```
// canWakeup 比 canInject 更严格（防止过度频繁唤醒）
// 原则：唤醒门槛 >= 注入门槛

// step1：判断来源（同 canInject）
isLocalEvent(e) = e.source_field_id == Bᵢ.foreground_stack.frames[0]?.field_id

// step2：计算有效唤醒门槛
wakeup_external_threshold = maxPriority(
  raisePriority(baseline_attention_policy, current_stress_modifier),
  wakeupPolicyFromField(current_field_profile),
  wakeupPolicyFromActivity(current_activity.profile)
)
// 注：wakeup 不叠加 field/activity 的瞬时注入门控
//     唤醒是"是否触发一次演化"的粗粒度决定

wakeup_local_threshold = "ambient"  // 场内事件总是能唤醒（与当前任务直接相关）

// step3：判断
canWakeup(e, Bᵢ) =
  if isLocalEvent(e): priority(e) >= wakeup_local_threshold
  else:               priority(e) >= wakeup_external_threshold

// 唤醒门槛映射函数（v1.1 修订，修正原 v1.0 倒置错误）：
wakeupPolicyFromField:
  background → "mention"   // 认知负荷最低，外界 mention 级即可唤醒
  engaged    → "mention"   // 参与但未专注，mention 级可唤醒
  focused    → "direct"    // 聚焦状态，直接消息才打断
  locked     → "urgent"    // 锁定状态，仅紧急事件打断

wakeupPolicyFromActivity:
  high        → "mention"
  medium      → "mention"
  low         → "direct"
  urgent_only → "urgent"

// 局部场优先级修正（来自当前前景场且满足以下条件，则降低一级唤醒门槛）：
// 条件：task_binding ∈ {strong, mission_locked}
//       OR normative_force ∈ {formal, binding}
//       OR synchrony == "live"

// 特殊规则（Life Threat Override，见 §12.3）：
// semantic_class ∈ {hazard, evacuation, emergency} → 强制唤醒，无视所有门槛
```

### 10.4 调度协调

```
// 调度器触发（由 C 的 scheduler_queue 管理）
on scheduled_wakeup in scheduler_queue where scheduled_at <= now:
  trigger R_Bᵢ^evolve for target agent Bᵢ

// next_wakeup 到期（time-driven）
on now >= Bᵢ.control.next_wakeup:
  trigger R_Bᵢ^evolve for Bᵢ

// 用户直接输入（绕过 attention_policy）
on user_message to Bᵢ:
  immediately trigger R_Bᵢ^evolve
```

### 10.5 资源租约管理（/coupling/resource_leases/）

```
// acquire：申请租约
acquireLease(resource_id, agent_id, purpose, lease_duration_sec):
  if existing active lease found: return null  // 已有有效租约，拒绝
  create new ResourceLease { resource_id, holder: agent_id, token: now_ms, ... }
  /coupling/resource_leases/lease_XXX.json ← write new lease
  Bᵢ.control.active_lease_refs ← push resource_id  // 通过事件触发 Bᵢ 的 apply

// renew：续约（必须在 lease_until 前，且 token 匹配）
renewLease(resource_id, agent_id, current_token): ...

// release：主动释放
releaseLease(resource_id, agent_id, current_token): ...

// Sweeper（C 的定时任务，每 60s 扫描）：
for each lease in /coupling/resource_leases/:
  if now > lease.lease_until:
    lease.status = "expired"
    emit LeaseExpiredNotification → agent Bᵢ（告知其租约已过期，通过 coupling/notifications/）
```

### 10.6 ephemeral 场超时检测

```
// C 在每次 clock tick 时检查 remote + ephemeral 场
on ClockTickEvent from A:
  for each ctx in interaction_contexts where field.lifecycle=ephemeral AND field.co_presence=remote:
    if ctx.last_message_at AND now - ctx.last_message_at > EPHEMERAL_TIMEOUT:  // [L3]
      emit ContextCloseRequest { ctx_id: ctx.id, reason: "idle_timeout" } → R_A^apply

// C 处理 LocationChangedEvent（检查 co_located + ephemeral 场关闭）
on LocationChangedEvent { agent: Bᵢ, from: old_place, to: new_place } from A:
  // 位置变化通知路由
  for ctx in contextsAtPlace(old_place) ∪ contextsAtPlace(new_place):
    for Bⱼ in physicalPresenceMembers(ctx.id):
      if Bⱼ != Bᵢ:
        /coupling/notifications/agent_j.queue.json ← push ambient notification

  // 检查 ephemeral 场关闭
  for ctx in contextsAtPlace(old_place):
    if ctx.field.lifecycle == "ephemeral" AND physicalPresenceMembers(ctx.id) == ∅:
      emit ContextCloseRequest { ctx.id, reason: "all_participants_left" } → R_A^apply
```

### 10.7 ObligationTimeout 处理

```
// ack_required=true 且超过 deadline 的通知，C 触发义务状态更新
on ObligationTimeout { notification_id, agent_id } from [定时器/C 内部触发]:
  // 找到对应的 pending/overdue obligation
  for each ctx in interaction_contexts:
    for each obligation in ctx.obligations:
      if obligation.owner_agent_id == agent_id
         AND obligation.deadline != null
         AND now > obligation.deadline
         AND obligation.state ∈ {"active", "accepted", "proposed"}:
        obligation.state = "expired"
        // 向 agent 发 urgent 通知（C 写自己的 coupling 目录，不直接写 V_B）
        /coupling/notifications/agent_id.queue.json ← push {
          priority: "urgent",
          semantic_class: "alarm",
          ack_required: true,
          metadata: { event: "obligation_expired", obligation_id: obligation.id }
        }
```

### 10.8 ContextClosedEvent 级联处理

C 收到 A 发射的 ContextClosedEvent 后，处理所有 agent 侧的级联清除（通过发通知，不直接写 V_B）：

```
on ContextClosedEvent { ctx_id, reason } from A:
  // 对所有前景场含该 ctx_id 的 agent，发送系统通知（触发 Bᵢ 自己 pop 栈）
  for each Bᵢ where foreground_stack contains ctx_id:
    /coupling/notifications/agent_i.queue.json ← push {
      type: "context_closed",
      ctx_id: ctx_id,
      priority: "mention",
      modality: "system",
      // agent 收到此通知后，在下次演化的 R_B^apply 中自己执行 popForeground
    }

  // 归档该场的所有 pending 通知
  for each agent_queue in /coupling/notifications/:
    for notif in queue where notif.source_field_id == ctx_id AND notif.status == "pending":
      notif.status = "archived"

  // 清除义务活跃索引
  /coupling/active_obligations/ ← remove obligations with source_field_id = ctx_id

  // 触发 Obligation 超时仲裁（若有未完成义务）
  // A 已将 obligations 标记为 expired；C 负责通知相关 agent
```

### 10.9 WritebackLedger 管理（事件提交触发物化）

```
on WriteEventCommitted { agent_id, write_event } from B:
  /coupling/writeback_ledger/pending_events.jsonl ← append write_event（先写事件）

// 物化：等 ActionReceipt 确认后（或同步处理）
// 注：C 不直接写 V_A 或 V_B；物化通过向目标粒子发射事件触发
//     例：write_event.target_object = "world/contexts/ctx_123/agenda.json"
//     → C 发射 ApplyWriteEvent → R_A^apply 修改 agenda
//     → write_event.target_object = "agents/agent_1/memory/context_xxx.json"
//     → C 发射 ApplyWriteEvent → R_B^apply 修改 memory

// 拓扑排序（依赖 causal_parent_ids，防因果乱序）【EP4 详细实现见工程补丁章节】
```

### 10.10 priorityOf 函数【L2】

```
priorityOf(event: DomainEvent, field: FieldProfile, actor: AgentId, recipient: AgentId, relation: RelationInfo): EventPriority

// RelationInfo 完整结构：
RelationInfo = {
  is_direct_mention      : boolean,
  is_direct_target       : boolean,
  is_response_to_recipient: boolean,
  actor_role_in_context? : string | null,
  recipient_role_in_context?: string | null,
  actor_is_superior      : boolean,
  has_obligation_to_respond: boolean,
  obligation_urgency?    : "low" | "normal" | "high" | null,
  social_valence?        : "positive" | "neutral" | "negative" | null,
}

// 基线（来自 attention_impact）：
//   background → ambient / engaged → ambient|mention / focused → 至少 mention / locked → 至少 direct

// 上调规则：
//   is_direct_mention = true → 至少 mention
//   is_direct_target = true  → 至少 direct
//   has_obligation_to_respond = true → 至少 direct（high urgency → urgent）
//   actor_is_superior AND normative_force=formal → 至少 direct
//   normative_force=binding AND has_obligation → urgent
//   modality="call" → 至少 direct（来电侵入性强）
//   arity=dyadic → 对另一方发言至少 direct
```

---

## 第十一章：智能体粒子 Bᵢ 的演化方程 R_B【L1 + L2】

智能体是 LLM 粒子，核心演化由 LLM API 完成。

### 11.1 R_B^assemble（读文件，组装 LLM 输入）

```
函数签名（文件系统映射）：
  输入：/agents/agent_i/ 的部分文件 + View_A(Bᵢ) + View_C(Bᵢ)
  输出：Context C（LLM 的 prompt 内容）

组装逻辑（见第四章 §4.4 的完整文件读取清单）：
C = {
  pinned_memory,         // memory[self]（摘要）+ capability_summary
  control_state,         // control.json 全量
  active_workspace,      // memory[context:<active_key>] 全文（若 active_key 非 null）
  recent_working_memory, // memory[date:today] 全文 + memory[date:recent_N] 摘要
  retrieved_long_memory, // memory[topic:*/entity:*] 相关片段（检索，非全量）
  surfaced_notifications,// coupling/notifications/agent_i.queue.json 中满足 canInject 的通知
  pending_obligations,   // 条件性全量注入（field.task_binding∈{strong,mission_locked} 或 normative_force=binding）
  ambient_context,       // 前景场消息切片 + 物理场轻量切片（两部分并集）
}

关键原则：没有任何文件以全量原始文本方式无条件注入，每个槽位都有过滤规则。

身份优先义务过滤：在大型场景中，context window 可能不足时，
  第一步：无条件注入本 agent 的所有 active/accepted/proposed Obligations
  第二步：用剩余预算按相关性注入其他内容
// 原因：如果 agent 自己的 obligations 被截断，会导致 normative_force=binding 的语义失效
```

### 11.2 R_B^evolve（LLM API 调用）

```
函数签名：
  输入：Context C（上一步组装的 prompt）
  输出：Output（LLM 的结构化响应）

Output = {
  action_proposals      : List<Action>,           // 工具调用序列
  control_delta         : ControlStateDelta?,     // 更新 control 的提议
  memory_updates        : List<MemoryUpdate>?,    // 更新 memory 的条目
  schedule_update       : SchedulingDelta?,       // 设置 next_wakeup
  acknowledged_notif    : List<notification_id>?, // 显式确认处理的通知
  text_response         : String?,                // 若有外部对话接收方
}

ControlStateDelta = {
  physical_location?  : string | null,            // 物理位置变化
  focus_action?       : "push" | "pop",           // ForegroundStack 操作
  new_field_id?       : context_id | null,        // push 时的目标场
  activity_preset?    : string,                   // 活动预设名称（LLM 只能输出字符串）
  energy_adjustment?  : Float[-3, +3],            // 每轮上限（超出截断）
  mood?               : MoodState,                // 使用 MoodState 二维结构
  attention_policy?   : Priority,                 // 修改 baseline（LLM 主动行使自主权）
  active_key?         : MemoryKey | null,
}

Action = {
  type    : string,  // "post_message" | "update_agenda" | "satisfy_obligation" | "submit_deliverable" | ...
  payload : Map<string, unknown>,
  // id 由系统在 R_B^apply 时分配，LLM 不填写
}

LLM 约束：
  - activity_preset 只能输出字符串名称，系统负责展开为 ActivityInstance
  - 不能直接输出完整 ActivityRuntimeProfile JSON
  - 不能直接写其他粒子的文件（写隔离）
  - ForegroundStack 变化只能通过 focus_action 声明，不能直接写 foreground_stack
  - resume_token 由系统生成，LLM 不填写
```

### 11.3 R_B^emit（发射事件）

```
函数签名（文件系统映射）：
  输入：Output（LLM 响应）
  发射：事件到 /world/（经 R_A^apply）和 /coupling/（经 R_C^apply）

发射规则：
  for each action in Output.action_proposals:
    if action is world-interaction (post_message, update_agenda, etc.):
      emit action_event → R_A^apply
    if action creates notifications or triggers obligations:
      emit action_event → R_C^apply

  if Output.control_delta.physical_location changed:
    emit PhysicalLocationChanged { from: old_place, to: new_place } → R_A^apply

  if Output.control_delta.focus_action in {"push", "pop"}:
    emit FocusChanged { action, field_id } → R_C^apply（C 通知相关方）

  // 写回账本（Split-Brain 防护【EP4/EP5】）
  for each write_event in generateWriteEvents(Output):
    stagingLedger.save(PendingIntent{ write_events: [write_event], ... })  // 先暂存
    // 等 ActionReceipt 确认后：
    // commit write_event → /coupling/writeback_ledger/（正式记录）
    // C 再物化到目标 V_P（通过发射事件）

主要 B→A 写操作：
| 动作类型 | 更新的 V_A 变量 |
|---------|--------------|
| 发帖/回帖 | interaction_contexts[forum_id].messages |
| 在群组/话题发言 | interaction_contexts[context_id].messages |
| 创建 interaction_context | interaction_contexts（新增）|
| 发起一对一对话 | interaction_contexts（type=chat，新增）|
| 位置变化 | 触发派生量重算（无 V_A 独立写）|

注意：acquire_skill(skill_id) 不属于 B→A 写操作，
      是 B 对自身 V_B.capability 的更新，需要系统授权事件
```

### 11.4 R_B^apply（写回 V_Bᵢ）

```
函数签名（文件系统映射）：
  输入：Output（LLM 响应）+ 系统外力事件
  修改：/agents/agent_i/ 下的文件（只有 Bᵢ 自己可写）

// ── 写入 Global Event Log（必须包含所有本轮变更）──
GlobalEventLog.append({
  event_id      : uuid(),
  ts            : now,
  actor         : Bᵢ,
  event_type    : "AgentEvolved",
  round_status  : "pending",              // Phase 1：intent 先记录
  physical_location : control.physical_location,
  foreground_field  : control.foreground_stack.frames[0]?.field_id ?? null,
  activity_mode     : control.current_activity.profile.mode,
  energy_before     : control.energy,
  actions           : [...],
  control_delta     : { old → new },
  memory_updates    : [...],
  notif_surfaced    : [...],
})

// ── 系统外力写入（每次演化必然执行）──
/agents/agent_i/control.json ← update:
  last_evolved_at = now                                  // Δt 计算基准，每轮必更新
  energy = clamp(energy + energy_delta_system + Output.control_delta.energy_adjustment, 0, 100)
  mood   = systemUpdateMood(mood, current_activity.profile.mode)
  current_stress_modifier = decayStressModifier(current_stress_modifier, last_high_stress_at, now)
  energy_recovery_lock = evaluateHysteresisLock(energy, consecutive_above_critical_hours)  // EP1
  consecutive_above_critical_hours = updateCriticalHours(energy, consecutive_above_critical_hours, Δt)
  sleep_debt_hours = updateSleepDebt(current_activity.profile.mode, Δt)  // EP8
  active_lease_refs = validateAndCleanLeases(active_lease_refs, now)  // EP13

// ── 将 surfaced_notifications 标记为 surfaced ──
for e in surfaced_notifications:
  /coupling/notifications/agent_i.queue.json ← update notification[e].status = "surfaced"
  notification[e].surfaced_count += 1

// ── Agent 自主写入（由 Output 决定）──
/agents/agent_i/control.json ← update（按 Output.control_delta）：
  physical_location ← Output.control_delta.physical_location（若有）

  // Hysteresis Lock 执行钩子【EP1】（在展开 activity_preset 之前校验）
  if Output.control_delta.activity_preset != null:
    newProfile = ActivityPresets[Output.control_delta.activity_preset].profile
    if energy_recovery_lock AND newProfile.energy_profile ∈ {consuming, highly_consuming}:
      Output.control_delta.activity_preset = "recovering"  // 系统覆盖，不执行高耗能活动
      // 下次 assemble 注入："系统拒绝了上次高耗能活动切换请求，原因：energy_recovery_lock"
  current_activity ← system.expandPreset(Output.control_delta.activity_preset)（若有）

  baseline_attention_policy ← Output.control_delta.attention_policy（若有，LLM 主动修改基线）
  active_key ← Output.control_delta.active_key（若有）
  next_wakeup ← Output.schedule_update.next_wakeup（若有，否则系统默认填入）

// ── ForegroundStack 操作（专用路径，不通过 control_delta 直接写）──
if Output.control_delta.focus_action == "push":
  pushForeground(agent, Output.control_delta.new_field_id, reason)
  // pushForeground 内部生成 resume_token，保存 interrupted_activity_snapshot
if Output.control_delta.focus_action == "pop":
  popForeground(agent)  // 含条件性 active_key 恢复 + resume_token 注入

// ── object_ref 自动派生更新（autoUpdateObjectRef，在 action 处理后执行）──
// 不依赖 LLM 主动申报，由系统从 action 类型自动推断
for each action in Output.action_proposals:
  if action.type === "write_to" AND action.target_doc_id:
    control.current_activity.object_ref = "doc:" + action.target_doc_id
  if action.type === "edit_file" AND action.file_path:
    control.current_activity.object_ref = "file:" + action.file_path
  if action.type === "reply_in_context":
    // object_ref 保持不变——回复不改变当前工作对象
  if action.type === "switch_activity":
    control.current_activity.object_ref = null  // 切换活动时清除，新活动重新绑定
// 注：object_ref 是 Activity 本体 Object (O) 维度的运行时承载
//     它存入 current_activity.object_ref，而非 ControlState 顶层字段

// ── Memory 写入（自主记忆写回）──
for (key, content, tier) in Output.memory_updates:
  // 系统自动注入 source_field_id（不依赖 LLM 申报）
  /agents/agent_i/memory/{key}.json ← {
    content, tier,
    source_field_id: foreground_stack.frames[0]?.field_id ?? null,  // 系统强制注入
    source_activity_preset: current_activity.preset,
    created_at: now,
    ...
  }

// ── 确认通知（显式）──
for e in Output.acknowledged_notif:
  /coupling/notifications/agent_i.queue.json ← update notification[e].status = "acknowledged"
  // acknowledged 后由 C 安排归档（见 §10.1 终局规则）

// 若物理位置发生变化：
if control.physical_location changed:
  emit PhysicalLocationChanged { from: old_place, to: new_place }
  → R_A^apply 处理

// 若认知前景变化（push/pop ForegroundStack）：
// → emit FocusChanged（在 pushForeground/popForeground 内部处理）

// activity_started 不再作为本体变量。需要"活动开始时间"时，
// 通过查询 GlobalEventLog 中该 agent 最近的 activity_changed 事件时间戳得到（派生量）
```

**ForegroundStack 操作语义**（唯一合法前景场切换路径）：

```
// push：进入新场
pushForeground(agent, newFieldId, reason, returnable=true, world):
  1. 冷却检查（防场切换抖动）【EP14】
  2. context 状态二次校验：if context.status == "closed": 拒绝 push【EP17】
  3. 生成 resume_token（系统生成，LLM 不填写）
  4. 构建 activitySnapshot（含 resume_token）作为 interrupted_activity_snapshot
  5. ForegroundStack.frames.unshift(new ForegroundFrame{ field_id: newFieldId, ... })
  6. 设置切换冷却（P0 强制切换不受冷却限制）

// pop：返回上一帧
popForeground(agent, world):
  1. frames.shift() 弹出栈顶
  2. Stale Frame 校验：若弹出的帧 context 已关闭/不存在，递归弹出【EP18】
  3. Location-Stack 一致性校验：若是 co_located 场但物理位置不符，跳过并递归【EP18】
  4. resume_token 注入：若 interrupted_activity_snapshot 有有效 resume_token，
     注入到下次 assemble（软恢复或完整恢复）
  5. 条件性 active_key 恢复：
     只有当 control.active_key 未被 LLM 主动修改时，才恢复到新栈顶帧的 active_key

栈操作语义总结：
| 场景 | 操作 |
|------|------|
| 正常进入新场 | push（returnable=true）|
| agent 主动放弃当前前景 | pop |
| P0 生存规则强制退出 | push null frame（returnable=true），恢复后 pop |
| ContextClosedEvent | 通过系统通知触发：agent 在下次演化时 pop 该 field_id 的帧 |
| 完成紧急任务返回原场 | pop，恢复之前帧 |
```

**active_key 同步规则**（不变量 I10）：

```
control.active_key 是 active_key 的唯一权威真源
ForegroundFrame.active_key 是压栈时的历史快照（"该帧进入时记录的工作上下文"）

push 时：新帧保存当前 control.active_key 作为快照；control.active_key 更新为新场的 active_key 或 null
pop 时：只有当 control.active_key == 被弹出帧.active_key 时，
        才将 control.active_key 恢复为被恢复帧的旧值
        否则尊重 LLM 在处理紧急任务期间对 active_key 的主动修改
```

### 11.5 energy 与 mood 的更新动力学【L2】

```
// 时间常量（L3 配置值）
TICK_HOURS = 1                  // 参考单位：1 小时
MAX_DELTA_T_HOURS = 72          // 离线超过 72h 的 Δt 截断【EP10】

// Δt 安全计算（防时钟回退 + 极端截断）【EP10】
Δt = clamp( max(0, now_hours - last_evolved_at_hours), 0, MAX_DELTA_T_HOURS )

// 系统物理规则部分（场 + 活动联合决定，每小时修正量）
energy_delta_system =
  ( fieldEnergyModifier(current_field_profile)           // 场的能量消耗（由 cognitive_demand 驱动）
  + activityEnergyModifier(current_activity.profile)     // 活动的能量消耗（由 energy_profile 驱动）
  ) × Δt

// ── fieldEnergyModifier（由 FieldProfile.cognitive_demand 统一驱动）──
// v6 新增 cognitive_demand 维度后，fieldEnergyModifier 有唯一权威定义，
// 消除了 v5（task_binding 驱动）与 FA（attention_impact 驱动）的二义性矛盾
fieldEnergyModifier(field_profile):
  cognitive_demand = minimal    → -0.5/h   // 轻度协调（论坛、休闲群聊）
  cognitive_demand = moderate   → -1.0/h   // 常规协作（普通群聊、日常 chat、例行会议）
  cognitive_demand = high       → -2.5/h   // 聚焦讨论（专题会议、焦点任务场）
  cognitive_demand = intensive  → -4.0/h   // 高强度决策（全员大会、危机应对、紧急攻关）

// attention_impact 只驱动门控（fieldExternalThreshold/fieldLocalThreshold），不驱动 energy
// task_binding 只驱动 active_key 绑定策略和 pending_obligations 注入策略，不驱动 energy

// ── activityEnergyModifier（由 ActivityRuntimeProfile.energy_profile 驱动）──
activityEnergyModifier(activity_profile):
  recovering       → +5.0/h    // 睡眠、休息——正向恢复
  neutral          → +0.5/h    // 轻度活动——微弱恢复
  consuming        → -3.0/h    // 工作、深度思考——消耗
  highly_consuming → -6.0/h    // 高强度活动——大量消耗

// ── Agent 自主提议部分──
energy_adjustment ∈ [-3, +3]   // 每轮上限（超出截断），LLM 在 Output 中提议

// ── 最终更新 ──
energy(t+Δt) = clamp(energy(t) + energy_delta_system + energy_adjustment, 0, 100)

// ── Energy 阈值 → 行为约束（P0 生存规则，完整表）──
// 这些规则由 R_B^apply 前置检查强制执行，优先于任何 LLM 输出
//
// energy 范围          | output_bandwidth 上限 | 禁止的 energy_profile    | 系统强制动作
// [0, EXHAUSTED=0]    | minimal（强制）       | consuming, highly_consuming | 切 recovering + energy_recovery_lock=true
// (0, CRITICAL=15)    | light（上限）         | highly_consuming           | 降级 output_bandwidth + 建议 recovering
// [CRITICAL, SAFE=30) | normal（上限）        | highly_consuming（lock=true 时）| 持续锁定，等解锁双条件
// [SAFE=30, 100]      | 无限制               | 无（lock=false 时）        | 正常运行
//
// 具体阈值（L3 参数，当前推荐值）：
//   EXHAUSTED = 0       // energy 归零
//   CRITICAL  = 15      // 临界状态
//   SAFE      = 30      // 安全线（Hysteresis Lock 解锁条件 A）
//
// 与 effective_actions 的联动：
//   actionsAllowedByEnergy(control.energy) 实现上述约束
//   energy ≤ 0: 只允许 resting/recovering 类活动对应的动作
//   energy < CRITICAL: 禁止 highly_consuming 活动的高带宽输出动作

// mood 的更新逻辑（弱闭环，已知不完整）：
// 系统外力：基于活动类型的倾向（长期高强度工作 → valence 趋负）
// LLM 可在此基础上调整（通过 Output.control_delta.mood）
// 两个函数 fieldSocialValence / activityAffect 尚未正式定义（已知缺口，见附录 F）
```

---

## 第十二章：调度层【L1 + L2】

### 12.1 触发条件

```
条件 1（time-driven）：
  now >= Bᵢ.control.next_wakeup

条件 2（event-driven）：
  canWakeup(e, Bᵢ) = true  for some pending e in notification_buffer[i]

条件 3（用户直接输入）：
  用户通过 UI 向 Bᵢ 发送消息 → 绕过 attention_policy，立即触发
```

### 12.2 中断语义

| 当前状态 | 处理方式 |
|---------|---------|
| Bᵢ 未在演化 | 直接触发，无冲突 |
| Bᵢ 正在演化（协作式）| 等待当前演化安全点完成，事件追加到下次 C |
| Bᵢ 正在演化（强制中断）| 仅限 urgent 事件；协作式优先，抢占式极限情况使用；需恢复机制 |

### 12.3 Life Threat Override【L2】

```
// 当通知的 semantic_class ∈ {hazard, evacuation, emergency} 时：
//   绕过所有门槛（包括 energy_recovery_lock），强制唤醒
//   代价：积累 sleep_debt_hours += 8（透支惩罚）
//         mood → { valence: -1, activation: 4, label: "adrenaline_override" }
// 这是 P0 生存规则的特例：生命威胁面前，能量保护规则让位于物理生存

LIFE_THREAT_SEMANTIC_CLASSES = ["hazard", "evacuation", "emergency"]
```

### 12.4 next_wakeup 更新规则

```
// 若 Output 包含 schedule_update.next_wakeup：直接写入 control.next_wakeup
// 若 Output 未指定：系统按活动类型设置默认值（L3 策略参数）：

| 活动 | 默认唤醒间隔（策略建议范围）|
|------|--------------------------|
| sleeping / recovering | +6~8h |
| resting | +30~60min |
| writing_on_computer | +10~25min |
| thinking_deep | +15~40min（若无外界事件）|
| discussing + live | 主要依赖 event-driven |
| recovering | 按恢复节律 |
| live + mission_locked 场 | 缩短 fallback |
| async + casual 场 | 放宽 fallback |
| energy < 20 | 拉长 wakeup 间隔 |

// MIN_WAKEUP_INTERVAL 保护【EP10】：
next_wakeup = max(computed, now + MIN_WAKEUP_INTERVAL_SEC)
// 防止 next_wakeup ≤ now 导致 busy loop
```

---

## 第十三章：核心映射函数汇总【L2】

> 汇总所有 L2 层的桥接函数，形成"Field × Activity × AgentState → 运行时行为"的完整映射。

**总公式**（Ψ 函数）：

```
Π_i(t) = Ψ( field_profile, activity_profile, control_state, incoming_events, Δt )
  → { Perception, ActionSpace, Δcontrol, next_wakeup }

输入暴露：Exposure_i = Ω( field_profile, events )
         → 场负责决定一个输入有没有资格到达 agent
感知摄取：Perception_i = Φ( Exposure_i, activity_profile, control_state )
         → 活动负责决定 agent 怎么处理已暴露输入
输出动作：Action_i = Γ( field_profile, activity_profile, control_state, Perception_i )
         → 场决定输出合法性，活动决定表达方式
状态写回：Δcontrol = B( control_state, Perception_i, Action_i )
世界更新：W(t+1) = U( W(t), Action_i )
再调度：  (activity(t+1), field(t+1)) = Σ( ... )
```

**函数清单**：

| 函数 | 输入 | 输出 | 章节 |
|------|------|------|------|
| `visibleSlice` | field, observer, context | 可见内容切片 | §9.1 |
| `canInject` | event, Bᵢ | bool | §10.2 |
| `canWakeup` | event, Bᵢ | bool | §10.3 |
| `priorityOf` | event, field, actor, recipient, relation | Priority | §10.9 |
| `fieldExternalThreshold` | FieldProfile | Priority | §10.2 |
| `fieldLocalThreshold` | FieldProfile | Priority | §10.2 |
| `activityInputThreshold` | ActivityRuntimeProfile | Priority | §10.2 |
| `activityInterruptThreshold` | ActivityRuntimeProfile | Priority | §10.2 |
| `activityLocalThreshold` | ActivityRuntimeProfile | Priority | §10.2 |
| `wakeupPolicyFromField` | FieldProfile | Priority | §10.3 |
| `wakeupPolicyFromActivity` | ActivityRuntimeProfile | Priority | §10.3 |
| `allowedActions` | field, contextState, agentState | ActionType[] | 见下 |
| `fieldEnergyModifier` | FieldProfile | Float/h | §11.5 |
| `activityEnergyModifier` | ActivityRuntimeProfile | Float/h | §11.5 |
| `defaultActiveKey` | field, context_id | MemoryKey\|null | 见下 |
| `closeCondition` | field, contextState | bool | §9.4 |
| `virtualFieldFromActivity` | ActivityInstance | VirtualFieldProfile | §2.3.2 |
| `raisePriority` | base, stress_modifier | Priority | §2.3.2 |

**allowedActions 权限矩阵**【L2】：

```
allowedActions(field, contextState, agent):
  1. 确定 agent 的角色：role = contextState.roles[agent.id] ?? (member if in participants else guest)
  2. 从权限矩阵查询基础动作集：

Permission Matrix（Role → Action）：
  Action              | guest | member | reviewer | owner | host | admin
  read_messages       |  ✓   |   ✓   |    ✓    |   ✓  |  ✓  |  ✓
  send_message        |  ✗   |   ✓   |    ✓    |   ✓  |  ✓  |  ✓
  join_request        |  ✓   |   ✗   |    ✗    |   ✗  |  ✗  |  ✗
  satisfy_obligation  |  ✗   |   ✓   |    ✓    |   ✓  |  ✓  |  ✓   // 履行自己的义务
  submit_deliverable  |  ✗   |   ✗   |    ✗    |   ✓  |  ✓  |  ✓
  update_agenda       |  ✗   |   ✗   |    ✗    |   ✓  |  ✓  |  ✓
  assign_obligation   |  ✗   |   ✗   |    ✗    |   ✗  |  ✓  |  ✓
  close_context       |  ✗   |   ✗   |    ✗    |   ✗  |  ✓  |  ✓
  waive_obligation    |  ✗   |   ✗   |    ✗    |   ✗  |  ✓  |  ✓
  force_close         |  ✗   |   ✗   |    ✗    |   ✗  |  ✗  |  ✓

  3. context.status 过滤（closing/closed 时收缩）
  4. visibility=members_only 对 guest 的额外限制

// 与活动组合（最终有效动作集）：
final_actions = intersect(
  union(
    allowedActions(field, contextState, agent),           // 前景场 + 角色约束
    allowedActions(physical_location_field, contextState, agent)  // 物理场动作（低头族悖论修复）
  ),
  actionsAffordedByMedium(current_activity.profile.medium),      // 媒介约束
  actionsAllowedByBandwidth(current_activity.profile.output_bandwidth), // 带宽约束
  actionsAllowedByMobility(current_activity.profile.mobility),   // 移动性约束
  actionsAllowedByEnergy(control.energy),                        // 能量约束（P0 规则）
  actionsSupportedByCapability(agent.capability)                 // 能力约束
)
// 低头族悖论：物理在会议室但认知前景在手机群聊时，
// 有效动作 = 前景场动作 ∪ 物理场动作（取并集），再与媒介/带宽/移动性/能量/能力取交集
// 这允许 agent 在物理会议室被点名时立刻回应，无需先 switch_field

// actionsAllowedByEnergy 映射（P0 生存规则）：
//   energy ≤ 0 → 只允许 resting/recovering 类动作
//   energy < CRITICAL_ENERGY → 禁止 highly_consuming 活动对应的动作
//   energy >= SAFE_ENERGY AND energy_recovery_lock=false → 无限制

// actionsSupportedByCapability 映射：
//   agent 只能输出其 capability.tools 或 capability.skills 支持的动作类型

defaultActiveKey(field, context_id): MemoryKey | null
  task_binding = none          → null
  task_binding = weak          → 可选 context:<id>
  task_binding = strong        → 默认 context:<id>
  task_binding = mission_locked → 强制 context:<id>
```

### 13.8 RuntimeEnvelope（运行时包络）概念说明

`RuntimeEnvelope` 是 Ψ 函数在每轮 assemble 前的**瞬时门控展开**。它不是一个持久化的数据结构，而是一个每轮重新计算的临时对象，汇聚了当前 field + activity + control 共同决定的运行时边界。

```
RuntimeEnvelope（每轮 assemble 前实时计算，不持久化）= {
  // 输入门控
  external_inject_threshold : Priority,        // 场外通知的注入门槛（§10.2 fieldExternalThreshold）
  local_inject_threshold    : Priority,        // 场内通知的注入门槛（§10.2 fieldLocalThreshold）
  reactive_local_override   : boolean,         // 是否允许物理现场最小反应动作集合（低头族）
  wakeup_threshold          : Priority,        // 唤醒门槛（§10.3 canWakeup）

  // 输出边界
  allowed_action_classes    : ActionClass[],   // 合法动作集合（§13.7 allowedActions）
  reactive_local_actions    : ActionClass[],   // 物理现场点名时允许的最小反应动作

  // 工作绑定
  recommended_active_key    : string | null,   // 推荐 active_key（§13.7 defaultActiveKey）
  recommended_next_wakeup   : Timestamp,       // 推荐下次唤醒时间（§12.4）

  // 资源
  energy_delta_model        : EnergyDeltaPolicy, // 能量变化模型（§11.5 fieldEnergyModifier + activityEnergyModifier）
  mobility_gate             : "free" | "limited" | "anchored",  // 来自 activity.profile.mobility

  // 系统级降载信号【EP11】
  overload_mode             : "normal" | "degraded" | "shed",
  fairness: {
    self_maintenance_due    : boolean,         // 是否欠自维护时间（resting/recovering 债务）
    reservation_active      : boolean,         // 当前 activity 是否在保留窗口内
  }
}
```

**RuntimeEnvelope 的三个重要原则**：

1. **不持久化，但被采纳的字段必须通过 R_B^apply 写回真状态**
   - `recommended_next_wakeup` → 必须写回 `control.next_wakeup`，否则 time-driven 链断裂
   - `energy_delta_model` → 通过 apply 更新 `control.energy`
   - `recommended_active_key`（强制类）→ mission_locked 场由系统自动写回

2. **`recommended_attention_policy` 故意不出现在 RuntimeEnvelope 中**
   - attention_policy 基线不可被场或活动修改（I7）
   - 有效门槛通过实时 maxPriority 计算得到，不通过 envelope 传递

3. **它是 Ψ 函数的门控展开前半段**（Perception 门控），而非完整的 Ψ 输出

**各字段的定义章节快速索引**：

| RuntimeEnvelope 字段 | 定义章节 | 核心函数/驱动维度 |
|---------------------|---------|-----------------|
| external_inject_threshold | §10.2 | fieldExternalThreshold（由 **attention_impact** 驱动）+ activityInputThreshold + activityInterruptThreshold |
| local_inject_threshold | §10.2 | fieldLocalThreshold（由 **attention_impact** 驱动）+ activityLocalThreshold |
| wakeup_threshold | §10.3 | wakeupPolicyFromField（由 **attention_impact** 驱动）+ wakeupPolicyFromActivity |
| allowed_action_classes | §13.7 | allowedActions（权限矩阵）|
| energy_delta_model | §11.5 | fieldEnergyModifier（由 **cognitive_demand** 驱动，v6 新增维度）+ activityEnergyModifier（由 **energy_profile** 驱动）|
| recommended_next_wakeup | §12.4 | defaultNextWakeup |
| mobility_gate | §2.3.2 | activity.profile.mobility |
| overload_mode | EP11 | 系统降载信号 |

---

---

# 第三部分：约束（Constraints）【L0 + L1 + L2】

> **物理直觉**：系统的什么性质在任何时刻都必须成立？
>
> **文件系统映射**：文件的值域约束 + 系统的运行保证

---

## 第十四章：硬不变量（Hard Invariants）【L0 + L1】

任意时刻、任意演化步骤后，以下条件必须成立：

```
I1 [写隔离]【L0】：
  V_Bᵢ 只能由 Bᵢ 自身或授权系统外力写入
  V_A 只能由 A 自身或授权系统外力写入
  V_C 只能由 C 自身或授权系统外力写入
  跨粒子影响只通过事件序列传递
  文件系统：各粒子目录只有自己可写

I2 [接口完备性]【L0】：
  所有跨粒子可见性通过 View_P(observer) 函数定义
  不存在隐式可见
  文件系统：只有通过 View 函数授权的文件才能被其他粒子读取

I3 [可回溯性]【L1】：
  层 1（唯一真源）：Global Event Log（/event_log/）
    ── 系统元设施维护，append-only，结构化，不可篡改
    ── 记录所有状态变更事件
  层 2（主观记忆材料）：Memory Store 中各条目
    ── agent 可读写，摘要化，主观化
    ── 服务于下次演化，不是审计真源
  审计投影（非独立层）：GlobalEventLog.filter(actor=Bᵢ, date=d) + aggregate
    ≈ 原 system_trace（派生查询视图，按需重建，不是独立数据结构）
  WritebackLedger 是 Global Event Log 某轮事件的细粒度展开，不是第二真源

I4 [通知生命周期明确性]【L1】：
  每条通知对象有确定的生命周期状态迁移（见 §10.1 终局迁移规则）
  不允许无状态消失
  surfaced ≠ acknowledged（重要区分）

I5 [energy 有界性]【L1】：
  ∀ Bᵢ: energy ∈ [0, 100] 在任何演化步骤后保持
  由 clamp 保证，agent 提议 + 系统物理更新均受此约束

I6 [next_wakeup 有定义值]【L1】：
  ∀ t: control.next_wakeup 必须是一个有效时间戳（不得为 null/undefined）
  若 Output 未提供，系统按默认策略设置
  保证 time-driven 唤醒链不断裂

I7 [baseline_attention_policy 不被场/活动自动覆盖]【L0】：
  control.baseline_attention_policy 是 agent 的绝对人格基线
  场进入/活动切换/系统周期调用均不得修改此字段
  只允许 LLM 通过 Output.control_delta.attention_policy 或用户显式操作修改
  有效门槛（effective_threshold）由基线与场/活动联合计算，瞬时生效，不写回基线
  防止"永久勿扰死锁"（场退出后基线自动恢复）

I8 [通知生命周期必须终局]【L1】：
  每条 Notification 必须最终进入 {merged | archived | expired} 之一
  禁止存在无 TTL 的永久 pending 通知

I9 [canInject 使用 local/external 分流]【L2】：
  来自当前前景场的通知使用 local 门槛（更低）
  来自其他场的通知使用 external 门槛（更严）
  不允许对所有来源的通知使用相同门槛

I10 [active_key 主容器与同步规则]【L1】：
  control.active_key 是 active_key 的唯一权威真源
  ForegroundFrame.active_key 是压栈时的历史快照
  同步规则见 §11.4（push/pop 时的条件性恢复）
```

---

## 第十五章：稳定性条件（Stability Conditions）【L1 + L2】

保证系统可无限时间演化的最小必要条件：

```
S1 [调度链不断]【L1】：
  ∀ Bᵢ, ∀ t: next_wakeup 不为 null
  充要：满足 I6

S2 [能量恢复路径存在]【L2】：
  ∃ activity preset 使得 energy_profile = recovering（energy_delta > 0）
  最小要求：sleeping/resting 类活动必须产生正能量增益（+5/h 或 +0.5/h）
  充要：activityEnergyModifier 有正值路径（recovering → +5/h）

S3 [记忆空间有界]【L2】：
  Memory Store 必须有压缩/遗忘机制（tiered access + retrievable + TTL 降级）
  充要：以下参数（L3 默认值，见 EP7 详细实现）：
    working_ttl_hours            = 24    // working tier 24h 后降级
    long_ttl_days                = 90    // long tier 90d 后降级或遗忘
    persistent_max_entries       = 500   // persistent tier 条目上限（LRU 淘汰）
    cross_context_ttl_multiplier = 0.5   // 跨场记忆加速降级（有效 TTL 减半）

S4 [通知空间有界]【L1 + L2】：
  /coupling/notifications/ 必须有 TTL 清理机制
  充要：满足 I8

S5 [交互上下文可关闭]【L2】：
  /world/contexts/ 中的每个 context 必须有关闭路径
  ephemeral: 参与者散去或超时关闭
  bounded:   closeCondition 满足时可关闭
  persistent: 手动关闭

S6 [演化函数无死状态]【L1】：
  ∀ 合法状态 s: R_P(s) 必须产生合法下一状态
  不存在"永久停滞"的状态（在理想 LLM 条件下）
  注：LLM 不完美导致的死锁由工程补丁 EP2 处理

S7 [durable 写回先写事件]【L1】：
  影响系统长期真相的写回一律先写 WriteEvent（含 idempotency_key），再物化状态
  LLM 必须等 ActionReceipt 才能写回记忆（Split-Brain 防护）
  → 保证崩溃恢复的可行性（从 /coupling/writeback_ledger/ replay WriteEvent）
  注：此条件确保 G6 [世界可追溯] 目标可达；工程实现见 EP4+EP5

S8 [因果顺序保证]【L1】：
  WriteEvent 的应用顺序由 causal_parent_ids（逻辑时钟）决定，不由 created_at（wall clock）决定
  → 防止断网重连后批量提交的 Event 乱序导致因果颠倒
  注：S7 的前提是写了 WriteEvent；S8 的前提是正确地应用 WriteEvent；二者共同保证可回放
```

---

## 第十六章：规则优先级【L0 + L1】

当多条规则发生冲突时，按以下优先级决定最终行为：

```
P0 生存规则（Survival Rules）← 最高优先级，不可被任何其他规则覆盖
  由系统层 R_B^apply 强制执行，不经过 LLM 决策
  - energy ≤ 0 → 强制切换 recovering 活动 + 激活 energy_recovery_lock
  - energy < CRITICAL_THRESHOLD → 降级 output_bandwidth，禁止 highly_consuming 活动  [L3]
  - system 崩溃恢复 → 强制清除悬空引用
  特例：Life Threat Override → semantic_class ∈ {hazard, evacuation, emergency} 时绕过 energy_recovery_lock

P1 安全规则（Safety Rules）
  系统自动触发，可通知 LLM 但不等待 LLM 确认
  - obligation deadlock timeout → 强制仲裁/关闭
  - context closed → 通知 agent pop ForegroundStack
  - stale reference → 强制 null 化并告警

P2 场规则（Field Rules）
  RuntimeEnvelope 建议，LLM 参与决策
  - mission_locked → 要求留在场内完成任务
  - normative_force=binding → 要求满足 obligations
  - closeCondition → 关闭前检查完整性

P3 活动规则（Activity Rules）
  - mobility=anchored → 不建议切换场
  - interrupt_tolerance=urgent_only → 过滤低优先级唤醒

P4 Agent 自主规则（Agent Autonomy）
  - attention_policy → agent 的基线偏好
  - active_key 绑定建议 → LLM 自主决定
```

---

## 第十七章：时间公设【L2】

```
T1 [时钟回退保护]：
  Δt = max(0, now - last_evolved_at)
  实现层使用 monotonic clock 计算 Δt，wall clock 只用于人类可读时间戳
  时钟回退时 Δt 自动截断为 0，不产生负能量

T2 [极端 Δt 截断]：
  MAX_DELTA_T_HOURS = 72  [L3]
  离线超过 72h 的 agent，energy 变化以 72h 计算，语义等同于"长时间休眠"

T3 [最小唤醒间隔]：
  MIN_WAKEUP_INTERVAL = 60s  [L3]
  next_wakeup = max(now + 60s, computed_next_wakeup)
  防止 next_wakeup ≤ now 导致 busy loop

T4 [因果顺序不依赖 wall clock]：
  WriteEvent 的应用顺序由 causal_parent_ids（逻辑时钟）决定
  created_at（wall clock）只用于展示和 TTL 计算

T5 [clock skew 容忍]：
  CLOCK_SKEW_TOLERANCE = 5s  [L3]
  多节点部署时，时间差 < 5s 视为同时发生
  超过则发 clock_skew_warning，不自动修正数据
```

---

---

# 第四部分：目标（Goals）

> **物理直觉**：系统为什么存在？其运行要达到什么效果？
>
> **文件系统映射**：系统的验收标准是什么？什么叫"系统工作正常"？

---

## 第十八章：系统级目标

系统级目标定义"平台正常工作"的充要条件：

```
G1 [无限时间演化]：
  系统可以无限期运行，不会因内部逻辑而停止
  充要条件：满足第三部分所有约束条件（I1-I10 + S1-S6）

G2 [智能体认知连续性]：
  每个 Bᵢ 在连续的演化轮次中，能感知到自己的历史状态和工作上下文
  实现：Memory Store + ForegroundStack resume_token + source_field_id 域隔离

G3 [事件可达性]：
  ∀ 合法事件 e 发生，∀ 应该感知到它的 Bᵢ：
    e 最终会到达 Bᵢ 的 assemble 输入（TTL 内通过 C 的路由）
  实现：notification_buffer 的可靠投递 + TTL 终局约束

G4 [动作可执行性]：
  ∀ 合法动作 a 被 Bᵢ 提议：
    a 能被 A 接受并产生预期的 V_A 变更
  实现：allowedActions 校验 + R_A^apply 确定性处理

G5 [并发一致性]：
  多个智能体同时修改共享状态时，不产生冲突或数据损坏
  实现：
    messages → append-only（天然无冲突）
    AgendaItem → 乐观锁（version 字段）；热点场景升级为 queued（EP16）
    Obligation → CAS（state 字段）
    ForegroundStack → per-agent 单写者 + context 状态二次校验
    ResourceLease → token fence（EP12）
    context.participants → append-only（加入）+ host-only-delete（移除）
    Notification → append-only 写入 + CAS 状态变更

G6 [世界可追溯]：
  系统所有状态变更可从 Global Event Log（/event_log/）完整重建
  充要条件：满足 S7（WriteEvent-first）+ S8（因果顺序保证）
  // Memory Store 中的主观内容是"主观记忆材料"，服务于下次演化，不是审计真源
  // 审计视图（原 system_trace）= GlobalEventLog.filter(actor=Bᵢ) + aggregate，
  //   按需重建，是派生查询视图，不是独立数据结构
  //   不存在"向 system_trace 写入"的动作，只存在"向 event_log 追加结构化事件"
```

**注意**：以上是系统级目标，不是智能体级目标。智能体追求什么（完成任务、维护关系等）由 LLM 在每次演化中自主决定，不在此定义。

---

## 第十九章：涌现行为

以下行为由系统机制自然涌现，不需要特殊规则：

| 涌现行为 | 来源机制 |
|---------|---------|
| 智能体有一致的"个性" | memory[self] access=pinned，每次必然注入摘要；ActivityInstance.profile 驱动行为风格 |
| 智能体会"遗忘"细节 | 只注入 retrievable 的检索结果，非全量，远期细节自然衰退；source_field_id 隔离不同场的记忆 |
| 智能体会"专注"并拒绝打扰 | baseline_attention_policy 升高 + activity.input_openness=narrow → effective_threshold 升高 → 通知不注入 |
| 智能体会"漂移"（若 energy/mood 无约束）| 系统物理规则保证漂移有界（clamp + 场×活动时间驱动模型）|
| 中断后能恢复之前的工作 | ForegroundStack 保存 interrupted_activity_snapshot；pop 时 resume_token 注入 assemble |
| 群体讨论与共识 | 多 Bᵢ 共享 interaction_context（field 参数化），消息互相可见，各自独立 Evolve |
| 正式场景产生可追踪的义务 | normative_force=binding 的场 → Obligation 7态状态机 → 义务明确归属与追踪 |
| 世界可追溯 | 可追溯性依赖 Global Event Log；interaction_context 不要求本身永远 append-only |
| 对话需要"约定位置" | 物理对话：physical_location + co_located 场；数字对话：foreground_stack + context；两者可以不同（低头族） |
| Agent 的 time-driven 唤醒链不会断裂 | next_wakeup 不变量（I6）保证任何时刻都存在下一次唤醒时间 |
| 跨场记忆不会互相污染 | MemoryEntry.source_field_id 域隔离；assemble 时按 field 过滤相关记忆 |

---

---

# 第五部分：工程补丁（Engineering Patches）【EP】

> **物理直觉**：理想模型在计算机系统的有限资源和故障环境中运行时，需要哪些额外机制？
>
> **文件系统映射**：哪些代码是"理想模型之外"的防护性补丁？
>
> **重要原则**：工程补丁**不修改**第一至第四部分的定义。它们是实现层的额外代码，解决的是计算机系统特有的工程问题，而非社会交互动力学的理论问题。删掉以后，理想闭环的理论结构未必立刻坍塌，但实现层更容易抖动、漂移、抱死、双写或在极端情况下崩溃。

---

## 第二十章：工程补丁列表

### EP1：Hysteresis Lock（能量迟滞锁）

```
问题：
  energy 在 SAFE_ENERGY 阈值附近可能产生高频微震荡：
  consuming 活动 → energy↓ → 切 resting → energy↑ → 切回 consuming → ...
  在 0~1 之间无限循环，永远无法真正恢复

为何不是理论必要：
  理想的连续动力学系统中，energy 的演化是平滑的，不会产生这种离散震荡。
  震荡是离散时间步长（Δt 非无穷小）的数值副作用，是计算机实现的工程问题。
  理想 LLM 也不会反复申请消耗型活动以致 energy 归零。

实现方案：
  当 energy ≤ 0：强制切换 recovering 活动 + 激活 energy_recovery_lock = true
  energy_recovery_lock = true 时：禁止切换到 consuming/highly_consuming 活动
  解锁双条件（满足任一）：
    条件 A：energy >= SAFE_ENERGY（= 30）  [L3]
    条件 B：energy >= CRITICAL_ENERGY（= 15）且 consecutive_above_critical_hours >= 4  [L3]
  consecutive_above_critical_hours：跟踪连续保持 energy >= CRITICAL 的小时数（双条件解锁辅助字段）
```

### EP2：N-tick 超时逃生 + Obligation 死锁仲裁

```
问题：
  理论上，演化函数无死状态（S6）。但工程实现中，两类情况会导致场长期停滞：
  1. LLM 的幻觉导致前景场无任何有效进展（义务未完成，议程未推进）
  2. Obligation 循环依赖（A 等 B，B 等 A）导致场死锁，无法自然关闭

为何不是理论必要：
  理想 LLM 不会产生幻觉；理想义务系统不会有循环依赖。
  在理想条件下，agent 总能做出进展性动作，义务总能被正常履行或取消。

实现方案（两层机制）：

// ── 机制一：N-tick 场停滞逃生（通用，不依赖义务结构）──
参数：
  NO_PROGRESS_TICKS_THRESHOLD  = 5 个 tick（约 5h）  [L3]
  NO_PROGRESS_STRICT_THRESHOLD = 10 个 tick（约 10h） [L3]

触发条件（基于 EP3 的 PROGRESS_ACTIONS 白名单）：
  当前景场连续 NO_PROGRESS_TICKS_THRESHOLD 个 tick 无有效进展（last_progress_at 未更新）：
    C 向该场所有 agent 注入 urgent 级系统告警 { escape_permitted: true }
    agent 有权选择 pop 当前帧（逃生），P2 场规则（mission_locked）被 P0/P1 覆盖

  当达到 NO_PROGRESS_STRICT_THRESHOLD：
    强制标记 ForegroundStack 帧 returnable=false
    agent 获得强制逃生权

// ── 机制二：Obligation 死锁超时仲裁（针对 normative_force=binding 的场）──
参数：
  OBLIGATION_GRACE_PERIOD_HOURS   = 2h    [L3]  // 超时宽限期
  DEADLOCK_DETECTION_WINDOW_HOURS = 4h    [L3]  // 无进展判定窗口
  FORCE_CLOSE_AFTER_HOURS         = 12h   [L3]  // 系统强制关闭等待时间

处理流程（C 的 Scheduler 定期执行）：
  步骤1：将超时 obligation 标记为 overdue
    条件：now > obligation.deadline + OBLIGATION_GRACE_PERIOD_HOURS
    动作：发 urgent 通知给 obligation.owner_agent_id（ack_required=true）

  步骤2：检测无进展死锁
    条件：now - context.metadata.last_progress_at > DEADLOCK_DETECTION_WINDOW_HOURS
          AND 所有 pending obligations 均为 overdue
    动作：尝试重新分配 obligation 给同角色的其他可用 agent

  步骤3：若无法重新分配 → 通知 host 仲裁
    向 context.roles[host] 发 urgent 通知（ack_required=true）

  步骤4：若超过 FORCE_CLOSE_AFTER_HOURS → 系统强制介入
    将所有 overdue obligations → expired（reason: "force_closed_by_system"）
    context.status = "closing"，context.conclusion = "[系统强制关闭]"
    触发 ContextClosedEvent → C 处理级联清除

// 两种机制的区别：
//   N-tick 逃生：通用，针对场整体停滞（不依赖义务结构）
//   Obligation 仲裁：针对具体义务未履行（需要 normative_force=binding 的场）
//   两者可同时触发（场停滞 + 义务超时）
  注：有效进展的定义见 EP3
```

### EP3：PROGRESS_ACTIONS 白名单（防水群欺骗 N-tick）

```
问题：
  N-tick 超时逃生机制依赖 context.metadata.last_progress_at 判断是否有进展。
  若任何消息追加都能刷新该字段，则 LLM 水群/幻觉呢喃会让死锁永远无法被检测到。

为何不是理论必要：
  理想 LLM 不会水群；理想 agent 的每次发言都是实质性推进。

实现方案：
  只有以下"有效进展动作"才能刷新 last_progress_at：
    submit_deliverable / update_agenda / satisfy_obligation
    assign_obligation / conclude / close_context
    update_deliverable / resolve_blocker
  普通 send_message / short_reply / acknowledge / ping 不触发刷新
  仅在 task_binding ∈ {strong, mission_locked} 的场中启用
```

### EP4：WriteEvent-first + 拓扑排序

```
问题：
  如果先物化状态再写 WriteEvent，崩溃时可能丢失变更（无法恢复）。
  断网重连后大量 WriteEvent 批量提交时，若按到达顺序而非因果顺序执行，会导致状态错乱。

为何不是理论必要：
  理想系统中，动作提交是原子的，网络永不断裂，系统永不崩溃。

实现方案：
  先写 WriteEvent → 再物化状态（原子承诺点在 WriteEvent 写入）
  崩溃恢复从 WriteEvent 日志 replay（使用 idempotency_key 保证幂等）
  materializeStates 使用 Kahn 算法拓扑排序（按 causal_parent_ids），防因果乱序
  孤儿事件（parent 永远不到达）：等待超时后降级追加，不阻塞整个 materialize 链
```

### EP5：StagingLedger / PendingIntent 暂存（防假性遗忘）

```
问题：
  Split-Brain 问题：agent 发出 action → 等待 ActionReceipt → 写入记忆。
  若 ActionReceipt 延迟（3h 网络延迟/重负载），LLM 已经"认为"动作成功了但记忆未写入。
  若此时系统崩溃，LLM 的推理意图（"为什么要这样做"）永久丢失（假性遗忘）。

为何不是理论必要：
  理想系统中，动作提交是原子的，ActionReceipt 即时返回，网络永不延迟。

实现方案：
  在等待 ActionReceipt 期间，先把 LLM 的推理意图持久化到 StagingLedger（PendingIntent）
  等 ActionReceipt 到来后，从 StagingLedger 取出意图，提交正式 WriteEvent
  PendingIntent 有 TTL（默认 3h），超时 → abandoned，通知 agent 补记
  系统重启时：恢复未完成的 PendingIntent，继续等待或标记 abandoned
```

### EP6：Split-Brain 防护（ActionReceipt 前不写记忆）

```
问题：
  LLM 发出 action 后，假设 action 成功并立即写回记忆；
  实际上 action 可能失败（ActionReceipt.status = failed/unknown），
  导致记忆与世界状态不一致（记忆说"做了"，世界说"没做"）。

为何不是理论必要：
  理想系统中，动作要么原子成功要么原子失败，不存在"已提交但结果未知"的中间态。

实现方案：
  LLM 必须等到 ActionReceipt 确认后才可提交正式 WriteEvent 写回记忆
  ActionReceipt 状态机：
    success   → 正常 commitWriteEvent
    failed    → WriteEvent 记录 action_result="failed"，下次 assemble 注入"上次动作失败，请确认"
    unknown   → 超时 30s，WriteEvent 标注 action_result="unknown"
    duplicate → 幂等 key 已存在，跳过（不重复 materialize）
    out_of_order → causal_parent 未 apply，等待 10s，超时则 orphan
```

### EP7：Memory domain isolation（source_field_id 域隔离）

```
问题：
  Memory Bleed（记忆污染）：agent 在不同场中产生的记忆（如私人电话、工作会议）
  因缺乏来源标注，在 assemble 时被语义相关性检索混入其他场的上下文，
  导致认知污染。这是一个慢性致命缺陷，不会立即崩溃，但 3-6 个月后出现不可逆的认知错乱。

为何不是理论必要：
  理想 LLM 能完美区分上下文来源，不会发生跨场混淆。

实现方案：
  所有 MemoryEntry 强制携带 source_field_id（由系统在 commitWriteEvent 时自动注入，LLM 不得覆盖）
  Memory 生命周期参数（L3 默认值，与 S3 稳定性条件配合）：
    working_ttl_hours        = 24    // working tier → 24h 后降级为 long
    long_ttl_days            = 90    // long tier    → 90d 后降级为 persistent 或遗忘
    persistent_max_entries   = 500   // persistent tier 最多保留条目数（超出则按 LRU 淘汰）
    CROSS_CONTEXT_RELEVANCE_THRESHOLD = 0.85  // 跨场记忆注入的严格语义相关性阈值
    cross_context_ttl_multiplier = 0.5        // 跨场记忆有效 TTL = 正常 × 0.5（加速降级）
    // cross_context_ttl_multiplier 的意义：跨场产生的记忆（source_field_id ≠ 当前场）
    // 在跨场访问时老化更快，减少 Memory Bleed 的持久化窗口

  assembleMemory 域隔离过滤：
    第一优先：同源记忆（source_field_id 与当前前景场匹配，优先注入）
    第二优先：跨场记忆，但 cross_context_relevance >= CROSS_CONTEXT_RELEVANCE_THRESHOLD，且最多 5 条
    第三优先：pinned 记忆（用户显式钉住，跨场有效，最优先注入）
  source_field_id=null 的记忆视为"主体内部洞察"（休息/散步时产生），独立处理（上限 10 条）
```

### EP8：sleep_debt 跟踪

```
问题：
  agent 的 energy 模型是当前状态的，但"睡眠债务"是历史欠账的累积，
  无法从当前 energy 值推断。频繁被打断的 agent 会积累大量债务但无法体现。

为何不是理论必要：
  理想的动力学只需要当前 energy 值。sleep_debt 是对人类睡眠生理学的模拟，
  是行为建模需求，非系统公理。

实现方案：
  control.sleep_debt_hours 跟踪历史欠债（初始 0，上限 24h）
  onExitSleeping 时：deficit = max(0, target_hours - actual_hours)，累加到 sleep_debt_hours
  sleep_debt 影响 next_wakeup 的默认计算（有债务时延长下次睡眠时长，最多 +4h）
```

### EP9：onEnterSleeping 智能目标设置

```
问题：
  旧逻辑：每次进入 sleeping 都设定 target=8h。
  被打断再入睡又设 target=8h → 第一次被打断欠 7h，第二次被打断又欠 7h，
  总债务 = 14h（但实际上只欠 6h）。sleep_debt 错误叠加。

为何不是理论必要：
  理想系统中，agent 不会被频繁打断，sleep target 设定是精确的。

实现方案：
  target = max(1, BASE_SLEEP_HOURS - alreadySleptInWindow) + min(sleep_debt_hours, 4)
  target = min(target, MAX_SLEEP_TARGET = 12h)
  alreadySleptInWindow = 查询 sleep_log:* 条目，累计 duration_hours（24h 窗口）
  onExitSleeping 写入 sleep_log 条目（working tier，key=sleep_log:日期）
```

### EP10：MAX_DELTA_T / MIN_WAKEUP_INTERVAL / 时钟保护

```
问题：
  计算机系统中 Δt 可能为 0（同一时刻触发多次）或极大（服务器离线 80h）或为负（时钟回退）。

为何不是理论必要：
  理想的动力学假设时间连续演化，Δt 的极端值是计算机离散实现的副作用。

实现方案：
  Δt = clamp(max(0, now - last_evolved_at_hours), 0, MAX_DELTA_T_HOURS)  // T1 + T2
  next_wakeup = max(computed, now + MIN_WAKEUP_INTERVAL_SEC)              // T3
  使用 monotonic clock 计算 Δt（防时钟回退）                              // T1
  wall clock 只用于人类可读时间戳和 TTL 计算                             // T4
  CLOCK_SKEW_TOLERANCE = 5s（多节点部署的时钟偏差容忍）                   // T5
```

### EP11：overload_mode / 系统降载

```
问题：
  大量 agent 同时演化时，系统算力可能不足，导致整体性能下降或崩溃。

为何不是理论必要：
  理想系统有无限计算资源。降载是有限计算机资源的工程约束。

实现方案：
  overload_mode = "normal" | "degraded" | "shed"（在 RuntimeEnvelope 中携带）
    normal:   正常运行
    degraded: 延长默认 next_wakeup，降低演化频率，建议减少复杂输出
    shed:     只处理 urgent 级别事件，其余放弃本轮
```

### EP12：ResourceLease（排他性资源锁）

```
问题：
  多个 agent 同时争抢同一资源（如发言权、主持权、排他性工作区），产生并发冲突。

为何不是理论必要：
  理想动力学中，演化是原子且顺序的，不存在并发竞争。

实现方案：
  /coupling/resource_leases/ 管理排他锁（ResourceLease 对象）
  操作：acquire / renew / release / expire（Sweeper 每 60s 扫描）
  token fence：任何 ResourceLease 相关的 WriteEvent 必须携带 token 验证
  使用场景：发言权（3min）/ 主持权（会议全程）/ 排他编辑权（30min）/ 任务认领（obligation due_at）
```

### EP13：ResourceLease 掉线重连校验

```
问题：
  agent 掉线期间，其持有的 Lease 在 World/C 端已过期并被他人获取。
  agent 重连后，本地 AgentState 仍认为自己持有 Lease，持续提交携带旧 token 的 WriteEvent，
  被 World 拒绝后陷入重试活锁。

为何不是理论必要：
  理想系统中，agent 不会掉线，租约不会在持有者不知情的情况下过期。

实现方案：
  在 onSystemRestart 和首次 assemble 前，调用 validateAndCleanLeases：
    检查本地 active_lease_refs 中每个 resource_id 是否在 World 端仍有效
    无效的（已过期或被他人获取）→ 从 active_lease_refs 中移除 + 发系统通知
```

### EP14：ForegroundStack 冷却（防场切换抖动）

```
问题：
  LLM 可能在两个场之间快速反复切换，产生抖动（每轮切换一次），浪费计算资源。

为何不是理论必要：
  理想 LLM 不会无意义地反复切换场。

实现方案：
  ForegroundStack.cooldown_until 字段
  push 后设置 FOREGROUND_SWITCH_COOLDOWN_SECONDS = 10s 的冷却期
  冷却期内拒绝非 P0 的 push 操作
```

### EP15：ContextClosedEvent 级联清除

```
问题：
  context 关闭时，已关闭场的 ForegroundStack 帧、active_key、pending_notifications
  如果不清除，会产生悬空引用，导致系统状态不一致。

为何不是理论必要：
  理想系统中，context 关闭的信号总是能及时被所有相关 agent 感知和处理。

实现方案：
  见 §10.7 ContextClosedEvent 级联处理
  C 通过发系统通知触发 Bᵢ 自己 pop 栈（不直接写 V_B）
  assemble 时：validateActiveKey 检查 active_key 指向的 context 是否已关闭
```

### EP16：AgendaItem 队列化分配（防乐观锁饥饿活锁）

```
问题：
  乐观锁（AgendaItem.version CAS）对少量并发（< 5 agents）够用。
  但 100+ agents 同时争抢同一任务时，99% 的 CAS 失败，大量 agent 陷入反复重试的活锁。

为何不是理论必要：
  理想系统中，不存在 100+ agent 同时争抢同一任务的极端情况。

实现方案：
  task_binding_mode = "queued" 时，切换为 FIFO 队列化认领
  系统检测到连续 3 次 CAS 失败时，自动升级为 "queued" 模式
  Host 可显式标记 task_binding_mode = "queued"
```

### EP17：pushForeground context 状态二次校验

```
问题：
  ContextClosedEvent 清理与 Agent Tick push 之间存在竞态：
  C 正在发出 ContextClosedEvent，同时 agent 正在执行 push 到该场，
  可能导致 agent push 到一个已经关闭的场。

为何不是理论必要：
  理想系统中，事件处理是原子串行的，不存在竞态。

实现方案：
  pushForeground 执行时，实时检查 world.contexts[newFieldId].status
  若 status ∈ {"closed", "expired"} → 拒绝 push，返回 false
```

### EP18：popForeground Stale Frame + 物理位置一致性校验

```
问题：
  漏洞1（Stale Frame）：栈底部帧在长时间等待后，其对应 context 已关闭。
    pop 后恢复到一个死任务，造成"僵尸活锁"（不断恢复已结束的工作）。
  漏洞2（Location-Stack 精神分裂）：pop 后恢复到一个 co_located 场，
    但 agent 的物理位置已经离开了该场的 place_id，
    导致"认知在会议室，身体在广场"的分裂状态。

为何不是理论必要：
  理想系统中，context 不会在 agent 不知情的情况下关闭；
  agent 的物理位置与认知前景场总是一致的。

实现方案：
  pop 时：
    Stale Frame 校验：若弹出帧的 context 已关闭/不存在，递归弹出，寻找有效帧
    Location-Stack 一致性校验：若恢复帧是 co_located 场且 agent 物理位置不符，
      发系统通知告知不匹配，跳过该帧，继续递归
```

### EP19：Life Threat Override（生命威胁绕过迟滞锁）

```
问题：
  理论上，Hysteresis Lock 应在 energy_recovery_lock=true 时完全禁止消耗型活动。
  但在真正的生命威胁面前（如火灾逃跑），这条规则必须让位于物理生存。

为何不是理论必要（但接近 P0 生存规则的边界）：
  理想世界中，agent 不会遭遇生命威胁。
  此机制是生命威胁场景的特殊处理，属于工程补丁而非第一性原理。

实现方案：
  当 incoming_event.semantic_class ∈ {"hazard", "evacuation", "emergency"} 时：
    临时解除 energy_recovery_lock（仅本轮有效）
    允许任何活动（含 consuming/highly_consuming）
    透支惩罚：sleep_debt_hours += 8，mood → { valence:-1, activation:4, label:"adrenaline_override" }
```

### EP20：Notification 幂等投递（去重）

```
问题：
  系统重启或网络重试时，相同通知可能被重复投递到 agent 的 notification_queue，
  导致 agent 多次处理同一事件。

为何不是理论必要：
  理想系统中，每条通知只投递一次，不存在重复。

实现方案：
  每条 Notification 有全局唯一 id
  投递时检查：若 notification_queue 中已存在相同 id 的通知，跳过（幂等）
  使用 at-least-once + 幂等去重 实现 effectively-once 语义
```

---

### EP21：Notification collapse_key 去重合并（防通知爆炸）

```
问题：
  同一场的大量通知涌入（如 100 人的群聊爆发讨论），可能产生通知爆炸，
  导致 agent 的通知队列无限增长和被重复打断。

为何不是理论必要：
  理想系统中，通知密度有自然限制，不存在极端并发场景。

实现方案：
  Notification.collapse_key：相同 key 的 pending/surfaced 通知合并
    新通知到达时：旧通知 status → merged，新通知继承旧通知的 surfaced_count

  public_many 场的特殊处理（防 O(n²) 通知爆炸）：
    只对直接点名（direct_mentions）的 agent 发个人通知
    其他人：不发实时通知，等下次 time-driven wakeup 通过 visibleSlice 主动查询
    令牌桶限流：每个 agent×context 维度，每分钟最多被唤醒 MAX_PER_MINUTE=3 次 [L3]
```

---

### EP22：ResumeToken TTL + Compaction 检测

```
问题：
  resume_token 的有效性随时间流逝而降低（session 被 Compaction、context 已关闭、token TTL 过期）。
  注入过期的 resume_token 可能导致 LLM 产生幻觉式"恢复"（认为恢复了实际已过期的上下文）。

为何不是理论必要：
  理想系统中，中断恢复是完美的，不存在 token 失效问题。
  这是计算机系统时间流逝和 session 压缩的工程问题。

实现方案：
  ResumeToken 校验（popForeground 中调用 validateResumeToken）：
    "valid"             → 完整恢复：注入 resumption_context 到下次 assemble
    "ttl_expired"       → 不注入，LLM 从当前状态重新开始
    "context_closed"    → 不注入（目标 context 已关闭）
    "session_compacted" → 注入软提示："你的工作记录已归档，请基于 memory[active_key] 恢复"
  TTL 推荐值：24h [L3]
  context_snapshot_hash 用于检测 Compaction 是否发生
```

---

### EP23：系统重启恢复 + jitter 重置

```
问题：
  系统崩溃重启后：
  1. 大量 agent 的 next_wakeup 同时过期，瞬间产生"唤醒风暴"
  2. energy 需要按实际离线时间补算，不能用内存快照
  3. 已关闭的 context 对应的 foreground_stack 帧和 active_key 成为悬空引用
  4. 未物化的 WriteEvent 需要 replay
  5. 未完成的 PendingIntent 需要恢复
  6. 掉线期间过期的 ResourceLease 需要清理

为何不是理论必要：
  理想系统中，系统永不崩溃，不存在重启恢复问题。
  这是计算机系统的工程健壮性需求。

实现方案（onSystemRestart 检查清单，按顺序执行）：

  步骤 1：next_wakeup jitter 重置
    对所有过期的 next_wakeup（next_wakeup < now）：
    在接下来 5 分钟内随机分散唤醒时间（jitter）
    避免所有 agent 同时被唤醒导致瞬间过载

  步骤 2：按实际时间流逝补算 energy
    使用 last_evolved_at（而非内存快照）计算真实 Δt
    energy_delta = activityEnergyModifier(current_activity.profile) × clamp(Δt, 0, 72h)
    重启期间无前景场，fieldEnergyModifier 视为 0（保守估计）

  步骤 3：清除悬空引用
    ForegroundStack.frames 中已 closed 的 field_id → 清除对应帧
    若栈变空，active_key → null

  步骤 4：从 WritebackLedger replay 未物化的 WriteEvent
    getUnapplied(agentId) → topologicalSort（Kahn 算法）→ applyEffectPatch
    保证因果顺序（不依赖 wall clock）

  步骤 5：恢复未完成的 PendingIntent（来自 StagingLedger）
    TTL 内：重新入队到 pending_receipt_queue，继续等待 ActionReceipt
    TTL 过期：status → abandoned，向 agent 发 mention 级告警

  步骤 6：ResourceLease 掉线重连校验（validateAndCleanLeases）
    参见 EP13
```

---

---

# 附录

## 附录 A：变量类型分类表

| 类型 | 定义 | 文件系统类比 | 写入者 | 例子 |
|------|------|------------|--------|------|
| **本体变量**（Ontological Variable）| 粒子的独立真状态，只有粒子自身或授权外力可写 | 粒子目录下的读写文件 | 粒子自身 | `V_A.clock`, `V_B.memory` |
| **参数维度**（Parameter Dimension）| 描述粒子/context 性质的参数字段，是 V_P 的内部结构 | 配置文件（影响系统函数行为）| 随所属粒子 | `FieldProfile`（V_A 的参数）, `ActivityRuntimeProfile`（V_B 的参数）|
| **派生变量**（Derived Variable）| 由本体变量完全决定，不独立存储，实时计算 | 从文件动态计算的值（无存储）| 无（只读）| `physicalPresenceMembers(ctx)`, `time_of_day`, `foreground_field_id` |
| **可见视图**（Visible View）| 本体变量经访问规则后的投影函数 | 只读挂载点（不存储）| 无（只读）| `View_A(Bᵢ)`, `View_C(Bᵢ)` |
| **耦合缓冲变量**（Coupling Buffer Variable）| 粒子间的中间容器，属于 V_C，不属于 V_A 或 V_B | /coupling/ 目录下的文件 | C 自身 | `notifications/agent_i.queue.json`, `resource_leases/` |
| **工程辅助变量**（Engineering Auxiliary Variable）| 工程补丁引入的额外追踪字段，理想连续动力学中不必要 | 监控/临时状态文件 | 系统外力（非 LLM）| `sleep_debt_hours`, `energy_recovery_lock`, `consecutive_above_critical_hours` |
| **审计投影**（Audit Projection）| Global Event Log 的派生查询视图，非独立本体，不独立存储 | 无（按需从 event_log 重建）| 无（只读）| `event_log.filter(actor==Bᵢ, date==today)` + aggregate |

**注意**：`system_trace` 不是独立本体对象，属于审计投影。不存在"向 system_trace 写入"的操作，只有"向 event_log 追加结构化事件"。

---

## 附录 B：完整文件系统结构图

```
/
├── world/                                ← V_A（世界粒子）
│   ├── clock.json                        ← Timestamp，系统唯一时间真源
│   ├── natural_state/                    ← 占位待扩展
│   └── contexts/
│       ├── ctx_001/
│       │   ├── field_profile.json        ← FieldProfile（8维参数维度）[L2]
│       │   ├── participants.json         ← Set<participant_id>
│       │   ├── roles.json                ← Map<AgentId, string>
│       │   ├── messages/                 ← append-only（分层：hot/warm/cold）
│       │   ├── obligations/              ← List<Obligation>（7态权威真源）
│       │   ├── agenda.json               ← List<AgendaItem>（含 version 乐观锁）
│       │   ├── org_memory.md             ← group 类型的组织记忆
│       │   ├── files/                    ← 共享文件
│       │   └── meta.json                 ← status/place_id/last_message_at/deadline
│       └── ctx_002/ ...
│
├── coupling/                             ← V_C（耦合介质场粒子）
│   ├── notifications/
│   │   ├── agent_1.queue.json            ← NotificationQueue（List<Notification>）
│   │   └── agent_2.queue.json ...
│   ├── resource_leases/                  ← List<ResourceLease> 【EP12】
│   │   └── lease_XXX.json
│   ├── writeback_ledger/                 ← WritebackLedger（WriteEvent 序列）【EP4】
│   │   └── pending_events.jsonl
│   ├── staging_ledger/                   ← StagingLedger（PendingIntent）【EP5】
│   │   └── intents.json
│   ├── active_obligations/               ← Obligation 活跃子集索引（非权威真源）
│   └── scheduler_queue.json              ← 调度协调状态
│
├── agents/                               ← V_B₁...V_Bₙ（智能体粒子）
│   ├── agent_1/
│   │   ├── memory/
│   │   │   ├── self.json                 ← 自我模型（persistent, pinned）
│   │   │   ├── entity_<id>.json          ← 实体建模（long, retrievable）
│   │   │   ├── date_YYYY-MM-DD.json      ← 每日工作记忆（working, pinned）
│   │   │   ├── context_<id>.json         ← 工作上下文（working, pinned when active）
│   │   │   └── topic_<label>.json        ← 语义长期记忆（long, retrievable）
│   │   ├── control.json                  ← ControlState（含 ActivityInstance 参数维度）
│   │   └── capability.json               ← tools + skills
│   └── agent_2/ ...
│
└── event_log/                            ← 全局事件日志（唯一真源，append-only）
    ├── 2026-03-12/
    │   ├── evt_001.json
    │   └── ...
    └── ...
```

---

## 附录 C：Obligation 7态状态机

```
状态转移图：

  → [proposed]
      ↓ 被接受
    [accepted]
      ↓ 开始执行
    [active]  ←→  [blocked]（被依赖阻塞）
      ↓ 完成              ↓ 依赖解除
    [done]            [active]
      
  [proposed/accepted/active/blocked] → [cancelled]（主动取消）
  [proposed/accepted/active/blocked] → [expired]（TTL 到期或 context 关闭）

字段说明：
  blocking_on：阻塞该 obligation 的其他 obligation id 列表
  agenda_item_ref：关联的 AgendaItem（所有关联 obligation done 时，agenda item 自动 completed）
  satisfied_by：满足该 obligation 的 action id

权威真源声明：
  Obligation 权威存储在 InteractionContext.obligations 内
  C 的 /coupling/active_obligations/ 是活跃子集索引（active/accepted/proposed），
  用于调度器快速查询，不是独立真源
```

---

## 附录 D：符号表

| 符号 | 含义 |
|------|------|
| A | 世界粒子（唯一实例）|
| C | 耦合介质场粒子（coupling medium field particle，v6 新增）|
| Bᵢ | 第 i 个智能体粒子 |
| V_P | 粒子 P 的本体变量集（真状态）|
| View_P(Q) | P 对观察者 Q 的可见投影（函数，非集合）|
| R_P | 粒子 P 的演化规则集（assemble/evolve/emit/apply）|
| S_global(t) | t 时刻粒子本体真状态（V_A × V_C × V_B₁ × ... × V_Bₙ 的笛卡尔积）|
| S_total(t) | 完整前向演化状态。三粒子架构下 S_total = S_global（V_C 已涵盖原 M_meta^persist，无需附加项）|
| C(t) | 一次演化的上下文（R_P^assemble 的输出）|
| Output | 一次演化的输出（动作提议+控制更新+记忆更新+调度更新+可选响应）|
| notification_buffer | A→B 耦合缓冲（在 v6 中归入 V_C）|
| attention_policy | `baseline_attention_policy` 的向后兼容别名 |
| baseline_attention_policy | agent 的绝对注意力基线，不可被场/活动自动覆盖 |
| effective_threshold | 瞬时计算的有效门槛（基线+场+活动的 maxPriority），不写回状态 |
| FieldProfile | 场的 9 维运行时参数维度（arity/co_presence/synchrony/lifecycle/task_binding/visibility/attention_impact/normative_force/cognitive_demand）|
| ActivityInstance | 活动的完整运行时对象（preset + 7维 profile + object_ref + resume_token 等）|
| ActivityRuntimeProfile | 活动 7 维门控参数（FieldProfile 的活动侧对应）|
| ForegroundStack | 认知前景场的栈结构（替代单值 foreground_field_id）|
| physical_location | 物理所在地（替代单值 location 的物理部分）|
| MoodState | 情感的二维结构（valence × activation）|
| ResumeToken | 中断恢复令牌（系统生成，LLM 不感知内容）|
| WriteEvent | 字段级写回事件（WritebackLedger 的组成元素）|
| PendingIntent | 推理意图暂存（StagingLedger 的组成元素）|
| Obligation | 7态义务状态机（配合 normative_force=binding 的场）|
| ResourceLease | 排他性资源租约（工程补丁 EP12）|
| energy_delta_system | 系统物理规则决定的 energy 变化量（场×活动×时间模型）|
| L0/L1/L2/L3/EP | 层级标注（公理/本体/运行时细化/策略配置/工程补丁）|

---

## 附录 E：v5 → v6 内容映射表

| v5 章节 | v6 章节 | 主要变化 |
|---------|---------|---------|
| §1 四原语 | 第一章 §1.1-§1.2 | 无变化，标注 L0 |
| §2.1 粒子三元组 | 第一章 §1.2 | 无变化 |
| §2.2 S_global/S_total | 第五章 §5.1-§5.3 | 三粒子架构下 S_global 已含 V_C，S_total 简化 |
| §2.3 View_P observer 域 | 第四章 §4.1-§4.3 | 无变化 |
| §2.4 写隔离原则 | 第一章 §1.3 | 明确 C 也不能直接写 V_A/V_B |
| §3 变量类型学 | 第六章 §6 | 4分法扩展为7分法（新增"参数维度""耦合缓冲变量""审计投影"三种类型）|
| FieldProfile 8维 | 第二章 §2.1.1 | 升级为 9维，新增 cognitive_demand（统一 fieldEnergyModifier 的二义性）|
| §4.1 四阶段 | 第八章 §8.1 | 无变化 |
| §4.2 assemble 槽位 | 第十一章 §11.1 + 第四章 §4.4 | 无变化，增加文件读取清单 |
| §4.3 R_B^apply | 第十一章 §11.4 | 无变化，增加函数签名 |
| §4.4 energy/mood 动力学 | 第十一章 §11.5 | 无变化；Hysteresis Lock 标注 EP1 |
| §4.5 时间结构 | 第八章 §8.2 | 无变化 |
| §5.1 V_A | 第二章 §2.1 | 无变化，增加文件系统映射 |
| §5.2 View_A | 第四章 §4.2 | 无变化 |
| §5.3 R_A | 第九章 §9 | 大部分规则移至 C（§10），A 只保留世界状态变更 |
| §6.1 V_B | 第二章 §2.3 | 无变化 |
| §6.2 Memory Store | 第二章 §2.3.1 | source_field_id 标注 EP7（但仍保留在结构定义中）|
| §6.3 ControlState | 第二章 §2.3.2 | 无变化，增加工程辅助变量标注 |
| §6.4 Capability | 第二章 §2.3.4 | 无变化 |
| §6.5 View_B | 第四章 §4.1 | 无变化 |
| §6.6 Output | 第十一章 §11.2 | 无变化 |
| §7 调度层 | 第十二章 §12 | canWakeup 保留在 C（§10.3），next_wakeup 在 §12.4 |
| §8.1 B→A | 第十一章 §11.3 | 无变化 |
| §8.2 A→B canInject | 第十章 §10.2 | 从 B 的逻辑移至 C 的路由逻辑 |
| §8.3 通知结构 | 第二章 §2.2.1 | 无变化，终局迁移规则在 §10.1 |
| §9 并发语义 | 第十三章 §13 汇总函数 + 第三部分约束 | 分散到各相关章节 |
| §10 不变量 I1-I10 | 第十四章 §14 | 无变化，增加层级标注 |
| §11 涌现行为 | 第十九章 §19 | 无变化 |
| 附录 B 符号表 | 附录 D | 扩展（增加 C/L0-EP 等新符号）|
| 附录 C Obligation | 附录 C | 无变化 |
| 附录 D 开放问题 | 附录 F | 增加新的开放问题 |

---

## 附录 F：FieldProfile 9 维语义速查【L2】

| 维度 | 取值 | 驱动的核心函数/机制 |
|------|------|-------------------|
| `arity` | solo/dyadic/group/public_many | 消息路由 fan-out 范围、assemble 压缩力度、allowedActions 范围 |
| `co_presence` | co_located/remote/hybrid | physicalPresenceMembers、visibleSlice 同场判断、place_id 必要性 |
| `synchrony` | async/stream/live | **getRecentMessages 窗口取法**（hot/warm 切换）、assemble recent_window、wakeup 频率 |
| `lifecycle` | ephemeral/bounded/persistent | closeCondition、agenda/conclusion 需求、消息持久化策略 |
| `task_binding` | none/weak/strong/mission_locked | **defaultActiveKey**、pending_obligations 注入条件、N-tick 逃生触发条件 |
| `visibility` | public/members_only | View_A 投影规则、非成员可读性 |
| `attention_impact` | background/engaged/focused/locked | **fieldExternalThreshold / fieldLocalThreshold / wakeupPolicyFromField**（见下表，单调递增）|
| `normative_force` | casual/conventional/formal/binding | obligation 必要性、ack_required、closeCondition 检查、roles 必要性 |
| `cognitive_demand` | minimal/moderate/high/intensive | **fieldEnergyModifier**（见下表，唯一 energy 驱动，v6 新增）|

**attention_impact 三函数映射**（门控专用，单调递增）：

| attention_impact | fieldExternalThreshold | fieldLocalThreshold | wakeupPolicyFromField |
|---|---|---|---|
| background | ambient | ambient | mention |
| engaged | mention | ambient | mention |
| focused | direct | mention | direct |
| locked | urgent | direct | urgent |

**cognitive_demand → fieldEnergyModifier**（能量专用，与 attention_impact 完全独立）：

| cognitive_demand | fieldEnergyModifier | 典型场景 |
|---|---|---|
| minimal | -0.5/h | 论坛、休闲群聊、社交浏览 |
| moderate | -1.0/h | 普通群聊、日常 chat、例行会议 |
| high | -2.5/h | 专题讨论、焦点任务场、项目协作 |
| intensive | -4.0/h | 全员大会、危机应对、高强度攻关 |

**task_binding → active_key 和 obligations 注入策略**：

| task_binding | defaultActiveKey | pending_obligations 全量注入条件 |
|---|---|---|
| none | null | 否 |
| weak | 可选 context:\<id\> | 否 |
| strong | 默认 context:\<id\> | 是（assemble 强制注入）|
| mission_locked | 强制 context:\<id\> | 是（assemble 强制注入，不可被 window 截断）|

---

## 附录 G：ActivityRuntimeProfile 7 维语义速查【L2】

| 维度 | 取值 | 对系统的直接影响 |
|------|------|----------------|
| `mode` | thinking/reading/writing/.../sleeping/recovering | energy 演化方向、next_wakeup 默认策略、writeback 类型 |
| `medium` | none/phone/computer/face_to_face/voice_call/mixed | actionsAffordedByMedium、输入源类别 |
| `input_openness` | open/filtered/narrow/closed_except_urgent | activityInputThreshold（场外通知注入门槛贡献）|
| `output_bandwidth` | minimal/light/normal/high | 输出动作量级约束（受 energy 状态进一步限制）|
| `interrupt_tolerance` | high/medium/low/urgent_only | activityInterruptThreshold（可中断性贡献）|
| `energy_profile` | recovering/neutral/consuming/highly_consuming | activityEnergyModifier（±/h）|
| `mobility` | free/limited/anchored | 是否允许 PhysicalLocationChanged、场切换容忍度 |

**activityEnergyModifier 数值**：

| energy_profile | 每小时增量 |
|---|---|
| recovering | +5/h |
| neutral | +0.5/h |
| consuming | -3/h |
| highly_consuming | -6/h |

---

## 附录 H：开放问题（尚未收敛的设计决策）

| 问题 | 影响范围 | 层级归属 |
|------|---------|---------|
| Memory Store 的检索机制（关键词/向量/时间/混合？）| R_B^assemble | L3 |
| ~~`fieldEnergyModifier` 的精确公式~~ | ~~energy 动力学~~ | **已解决**：v6 新增 cognitive_demand 维度，fieldEnergyModifier 由其唯一驱动（见矛盾记录 C-006）|
| 近 N 日记忆的 N 值和摘要化策略 | 工作记忆注入 | L3 |
| View_P(observer) 中访问规则的完整定义（每类 observer 的矩阵）| 安全与隐私 | L2 |
| notification_buffer 的 exactly-once 消费保证机制 | 通知一致性 | EP |
| SAFE_ENERGY / CRITICAL_THRESHOLD 的具体数值（Hysteresis Lock）| 能量动力学 | L3 |
| ephemeral 场的 EPHEMERAL_TIMEOUT 具体值 | 生命周期关闭 | L3 |
| N-tick 超时逃生的 N 值（NO_PROGRESS_TICKS_THRESHOLD）| 调度稳定性 | L3 |
| resume_token 在 Compaction 后的失效降级处理 | 中断恢复 | EP22 |
| causal_parent_ids 的生成规则（顺序依赖 vs 并行检测）| 写回顺序 | EP4 |
| interaction_contexts 写入的并发语义（强一致 vs 最终一致）| 一致性模型 | L2 |
| natural_state 的具体内容（environment/map 的变量化时机）| 未来世界层扩展 | L1 |
| capability.skills 的获取事件链（谁授权，what event）| 技能生态 | L1 |
| Obligation 与 AgendaItem 的联动规则（已有基本定义，待完整形式化）| L2 桥接层 | L2 |
| fieldSocialValence / activityAffect 函数（mood 更新的弱闭环补全）| mood 动力学 | L2 |
| overload_mode 触发矩阵（何时进入 degraded/shed 模式）| 系统降载 | L3/EP11 |
| mobility 拆分为 physical_mobility + context_switchability | 活动 v2 议题 | L2 v2 |
| attention_impact 双重语义拆分（cognitive_load vs external_shield）| 场 v2 议题 | L2 v2 |
| 多前景场并行（v1 只有单前景场假设）| v2 范围 | L2 v2 |
| memory reconciliation（agent 记忆 vs world 真源冲突解决）| v2 范围 | L2 v2 |
| v1 物理世界建模范围（PW1/PW2）：v1 不建模自然灾害/物理强制事件/生命安全撤离的本体表示 | 已知 v1 范围边界 | 已知限制 |
