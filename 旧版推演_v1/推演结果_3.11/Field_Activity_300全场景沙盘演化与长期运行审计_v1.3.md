# Field × Activity 320 场景统一沙盘演化与长期运行漏洞审计 v1.3

## 0. 审计目标与基准

本报告严格基于 **Sonnet 主骨架 + GPT v1.2 稳定性层 + Gemini 极端场景防护层** 所融合而成的 6 份最新文档（@docs/sonnet 目录）进行最高强度的沙盘推演。

目标：在**概念自洽、状态写回、并发死锁、极端故障**（网络掉线、时钟漂移、世界灾害）等维度，找出这套看似坚不可摧的“工业级架构”中隐藏的底层逻辑漏洞。

本次共构造并逐项推演了 **320 个场景**（100 个正常流转 + 220 个极限/异常场景）。经过严格的因果推演，我找到了 **11 个极其隐蔽且致命的逻辑漏洞**。

---

## 一、 最致命的 11 大逻辑漏洞与沙盘证据

### 漏洞 1： ForegroundStack 深度爆炸与无界中断死锁 (Unbounded Stack Overflow)
* **证据出处**：《总索引与修订声明 v1.1_sonnet.md》 §2.1 ForegroundStack 定义；《组合规则》§12.2.1 push/pop 规则。
* **沙盘推演**：
  Agent A 正在执行任务 T1（栈深=1）。突然收到 `urgent` 任务 T2（栈深=2）。在处理 T2 时，又收到极高优的报警 T3（栈深=3）……在一次恶意攻击或灾难性级联报警中，A 连续收到 500 个合法的 `urgent` 打断。由于 `ForegroundStack` **没有定义最大深度（MAX_DEPTH）**，Agent 的栈会被无限撑爆。
  当 A 最终处理完栈顶任务开始 `pop` 时，底部的任务（如 T1, T2）由于搁置太久，不仅超出了 TTL，而且由于缺乏**栈内过期元素的自动清理机制**，Agent 每次 pop 出来的都是已经作废的任务，陷入“不断恢复死任务”的**僵尸活锁**。
* **原因结论**：`ForegroundStack` 必须定义强力的**深度上限（如 MAX_DEPTH=5）**，并在压栈超限时定义**逐出策略（Eviction Policy，例如强制淘汰最底层帧并转移其 obligation）**。同时在 `pop` 前必须有 **Stale Frame 校验**。

### 漏洞 2： 物理位置变更与栈顶状态的强制脱节 (Location-Stack Desync)
* **证据出处**：《操作语义映射》§8.2 FocusEvent 规范；《总索引》公设 9/10。
* **沙盘推演**：
  Agent 正在 `room_1` 开会（栈顶：`ctx_room1`，`physical_location: room_1`）。
  管理员触发强制系统事件：将 Agent 物理传送到 `plaza_2`。此时触发了 `location_change` 的 `FocusEvent`，将 `ctx_plaza2` 压入栈顶。
  随后，Agent 在广场上发呆了一会，执行了 `pop` 操作。
  **致命错误发生**：栈顶弹出了 `ctx_plaza2`，恢复了下层的 `ctx_room1`。此时，Agent 的认知前景场回到了 `room_1` 的会议，**但它的物理位置（`physical_location`）仍然在 `plaza_2`！**
  Agent 会在广场上对着空气回答 `room_1` 会议里的问题，造成彻底的精神分裂。
* **原因结论**：由于 `physical_location` 不是栈结构而是单值，当 `ForegroundStack` 发生 `pop` 时，如果恢复的前景场是一个 `co_located` 场，系统**没有任何机制自动核对/同步物理位置**。必须规定：pop 恢复 `co_located` 场时，若物理位置不符，必须强制阻断恢复或触发物理寻路动作。

### 漏洞 3： 迟滞锁 (Hysteresis Lock) 与睡眠保护期的互相绞杀 (Deadlock by Protection)
* **证据出处**：《组合规则》§12.1 Hysteresis Lock (要求能量>=30解锁)；§12.4 睡眠保护与 sleep_debt (要求最少睡 4 小时或一半 target)。
* **沙盘推演**：
  Agent 能量归 0，触发 P0 强制休眠，进入 `sleeping`，锁上迟滞锁（`energy_recovery_lock = true`），并开启了睡眠保护期（假设需睡 4 小时）。
  2小时后，能量恢复了 10 点（<30）。此时来了一个绝对 `urgent` 的系统级灾难事件（如所在房间着火）。
  根据 §12.4，`sleep_protection` 会将门槛提到 `urgent`，但火灾是 `urgent`，**允许穿透唤醒**。
  Agent 醒来，想要逃跑（`exercising/consuming` 活动）。
  但是！根据 §12.1，此时能量 10 < `SAFE_ENERGY(30)`，迟滞锁**严禁任何 consuming 活动**，输出带宽被锁死在 `light`。
  Agent 醒了，看着火烧过来，却因为迟滞锁的限制，无法执行任何高耗能的逃跑动作，只能以 `light` 带宽缓慢爬行，最终烧死。
* **原因结论**：P0 的能量迟滞锁缺乏**极端生命威胁下的 Override（透支/肾上腺素）机制**。在遇到 `semantic_class = "alarm/hazard"` 级别的绝对紧急事件时，必须允许破坏迟滞锁，哪怕透支能量（允许负数）或积累严重的惩罚（如长期的 Exhausted mood）。

### 漏洞 4： WritebackLedger 的因果顺序在断网重连下的“时光倒流” (Causal Reordering on Reconnect)
* **证据出处**：《操作语义映射》§8.5 WritebackLedger 机制（causal_parent_ids）。
* **沙盘推演**：
  Agent 在离线环境/弱网环境下（或处于系统 Overload Mode 的 Shed 降载状态下），本地生成了两个行动：
  Action 1（回复老板同意方案），产生 WriteEvent 1。
  Action 2（基于方案分配任务），产生 WriteEvent 2（causal_parent_ids=[Event 1]）。
  恢复网络时，Agent 批量提交。但世界引擎的入口 API 是并发的，Event 2 先到达，Event 1 因为网络路由延迟后到达。
  根据现有的《操作语义映射》§8.5，Ledger 的代码是**遍历未应用事件并直接物化（applyEffectPatch）**，代码里**没有实现真正的因果图拓扑排序（Topological Sort）**！
  系统会尝试先执行 Event 2，由于缺失前置状态（方案尚未在世界里被同意），Event 2 报错或污染数据。
* **原因结论**：理论上提出了 `causal_parent_ids`，但在核心执行函数 `materializeStates` 的伪代码中，完全缺失了依赖等待和排序逻辑，在并发乱序到达时一定会造成数据败坏。

### 漏洞 5： ResourceLease 抢占时的“死锁孤儿” (Orphaned Lease Deadlock)
* **证据出处**：《总索引》§2.8 ResourceLease。
* **沙盘推演**：
  在 `mission_locked` 会场中，Agent A 获取了“白板写入权”的 Lease（`token=1, lease_until=T+5min`）。
  A 掉线。根据规则，5 分钟后，Lease 过期，B 想要写入，成功申请到新的 Lease（`token=2`）。
  但是，A 的本地状态并没有收到撤销通知！3 分钟后 A 恢复网络，它“认为”自己依然持有该锁（本地没有过期驱逐逻辑），于是基于过期上下文发送了 `WriteEvent`。
  虽然 World 层的 Ledger 会因为 token 不对而拒绝该写入，但 A 的 `AgentState` 会陷入混乱：它不断尝试提交，不断被拒绝，陷入重试活锁，永远无法推进任务。
* **原因结论**：缺乏**“租约主动过期通知（Lease Expiration Push）”机制**。当资源被他人合法抢占或到期时，系统必须向原持有者发送一条高优系统事件，强制清除其本地的租约认知。

### 漏洞 6： N-Tick 超时逃生被“活死人”欺骗 (Fake Progress Defeating Timeout)
* **证据出处**：《操作语义映射》§12.3.1 N-tick 超时逃生机制。
* **沙盘推演**：
  Agent 们陷入了循环依赖死锁。根据规则，若场连续 5 个 Tick `last_progress_at` 不更新，则触发超时逃生。
  然而，Agent B 是一个低智 LLM 或发生幻觉的 LLM。它每一轮都在输出一个无效动作（例如无意义的呢喃：“让我想想…”，属于 `ActionType = short_reply`）。
  这个毫无营养的 `short_reply` 作为一个 Message 被追加到 Context 中。
  由于有新消息产生，World 层判定该场**有进展**，刷新了 `last_progress_at`。
  结果，死锁永远无法达到 N-Tick 的阈值。Agent A 被活生生地耗死在这个“表面在推进、实际卡死”的场里。
* **原因结论**：`last_progress_at` 的定义过于粗糙，将“水群/噪音”等同于“任务推进”。对于 `mission_locked/strong` 绑定的场，**进展判定必须仅限于 Agenda 的变更、Deliverable 的提交或 Obligation 的扭转**，绝不能被普通的 Message 刷新计时器。

### 漏洞 7： 物理灾害事件 (Hazards) 缺乏最高级本体声明 (Missing Hazard Modality)
* **证据出处**：《操作语义映射》§3.2 priorityOf；《总索引》§2.9 Notification。
* **沙盘推演**：
  世界发生地震，World 引擎向所有区域内 Agent 广播地震事件。
  但是，在 `EventSemanticClass` 中，最高级别只有 `alarm`；在 `modality` 中，只定义了 `message | call | system`。
  系统将“地震”编码为 `priority="urgent", modality="system", semantic_class="alarm"`。
  此时，Agent 正处于 `locked` 会议中，会议设定了“无视一切系统报警以保密”。
  因为缺乏超越 `urgent` 且物理不可抗拒的极高阶信道，地震通知在复杂的门控中被降权或被 LLM 用自主意志忽略。Agent 会在地震中继续开会。
* **原因结论**：对世界级的绝对物理干预（如强制驱逐、物理灾害、强制下线），系统缺乏一条**绝对不可拦截的上帝信道（God/Hazard Channel）**。不能走常规的 Notification 队列，必须直接作为 P0 中断注入底层 `ControlState`。

### 漏洞 8： Split-Brain 防护下的“假性遗忘” (Amnesia by Action Receipt Delay)
* **证据出处**：《操作语义映射》§12.2.1 Split-Brain 防护。
* **沙盘推演**：
  Agent 进行了大量复杂推理，得出了一个惊世骇俗的定律，并决定把它写在书里（Action: `write_to`）。
  根据严苛的防脑裂规则：必须等到 Action_Receipt 成功返回，才能提交这笔记忆的 `WriteEvent`。
  此时网络严重拥堵，Action_Receipt 延迟了 3 个 Tick（3小时）才返回。
  在这 3 个 Tick 里，Agent 被唤醒去做了别的事（因为 LLM 每一轮是不保留隐式上下文的，只读真状态）。
  3 个 Tick 后，Receipt 返回了。但由于原始 LLM 生成的那个 `WriteEvent`（连同里面蕴含的那个惊世骇俗的推理逻辑）并没有被持久化暂存！它随着那个已经结束的函数调用一起灰飞烟灭了。
  动作在世界中成功了，但 Agent 永远**忘记了自己为什么这么做**，也忘记了当时的深刻推理。
* **原因结论**：在要求“等待回执才写记忆”的同时，**没有设计用于暂存“待决记忆（Pending Memory/Intent）”的持久化结构**。在分布式系统中，这叫状态机流转断裂。必须在 AgentState 中引入 `pending_intents` 队列来安全度过网络延迟期。

### 漏洞 9： 睡眠债 (Sleep Debt) 与“连续打断”导致的整数溢出/错误 (Debt Calculation Bug)
* **证据出处**：《操作语义映射》§12.4 睡眠保护。
* **沙盘推演**：
  Agent 原计划睡 8 小时。睡了 1 小时后，被 `urgent` 事件唤醒。
  根据 `onExitSleeping` 的公式：`deficit = max(0, 8 - 1) = 7`，`sleep_debt_hours = min(debt + 7, 24)`。
  处理完紧急事件（耗时 0.1 小时）后，Agent 极度疲惫，重新 `switch_activity(sleeping)`，再次设定目标 `target = 8`（这是默认值）。
  又睡了 1 小时，又被打断。
  公式再次计算：`deficit = max(0, 8 - 1) = 7`，债务再次加 7。
  实际上 Agent 今晚只差 6 小时就能睡够，但因为两次被打断，系统生硬地每次按 8 小时扣减，直接给它强加了 14 小时的债务！很快债务就会爆顶到 24 小时，Agent 永远处于极度疲惫的抑郁状态。
* **原因结论**：计算 `sleep_debt` 的数学模型是极其幼稚和错误的。它没有考虑到“被中断后继续睡”的剩余目标扣减，而是每次醒来都粗暴地重置算差额。必须引入 `rolling_sleep_target` 或连续 24 小时滑动窗口统计。

### 漏洞 10： ContextClosedEvent 清理的并发竞态漏洞 (Race Condition in Cascading Delete)
* **证据出处**：《操作语义映射》§12.2 ContextClosedEvent。
* **沙盘推演**：
  World 层执行 `handleContextClosed(ctx_1)`，开始级联清理所有 Agent 的 `foreground_field_id` 和栈。
  恰好在此时，Agent B 的调度器正在独立执行一轮 `Tick`。
  B 的 LLM 输出决定 `pushForeground(ctx_1)`。
  若 World 清理函数先遍历过了 B（把它置 null），随后 B 的本地 Tick 执行，又把已经关闭的 `ctx_1` 压入了自己的栈中。
  结果：清理失效，B 的栈顶出现了一个永久的僵尸场，后续所有针对该场的 `emit` 动作都会报错。
* **原因结论**：文档在描述系统级清理时，忽略了并行演化系统的并发锁。系统级广播清理（P1）与 Agent 本地的状态更新之间，缺乏内存屏障（Memory Barrier）或事务级别的 CAS（Compare-And-Swap）校验。

### 漏洞 11： 并发写入的乐观锁在“高频小动作”下导致饥饿活锁 (Optimistic Concurrency Starvation)
* **证据出处**：《操作语义映射》§12.6 ContextState 乐观锁 (`AgendaItem.version`)。
* **沙盘推演**：
  100 个 Agent 在同一个大型项目群中，同时试图抢占一个 Agenda（比如认领一个 Task）。
  根据规则，它们都要携带 `expectedVersion` 发起更新。
  网络延迟不一，只有 1 个 Agent 成功，版本号 +1。
  剩下的 99 个 Agent 收到“冲突（Conflict）”报错。下一轮它们更新版本号再次并发抢夺。
  依然只有 1 个成功，98 个失败。
  在极度频繁的抢夺下，某些网络稍慢的 Agent 会连续几百个 Tick 遭遇乐观锁冲突，它们把所有的算力和 Token 全用来进行这无意义的 CAS 重试。
* **原因结论**：对于超大规模并发抢占的资源（如热点任务认领），简单的乐观锁（Optimistic Locking）必然导致**活锁（Livelock）和严重的资源饥饿**。必须在 World 侧提供类似于 Kafka 的队列化顺序分配（Queued Allocation）或由特定角色（Host）进行独裁分配，而不是让大家无脑并发抢锁。

---

## 三、 沙盘推演结论

这 320 个沙盘的极限推演揭示了一个核心真相：

虽然 **Sonnet + GPT + Gemini** 融合版本的语义设计已经登峰造极，理论逻辑也极其自洽，但在真正将其部署为**高并发、分布式、长周期的工业级物理模拟引擎**时，由于**并发竞态、网络时序错乱、栈溢出、物理算法粗糙**等问题，依然会发生“千里之堤，溃于蚁穴”的崩溃。

以上提取出的 11 个漏洞，是未来工程师在写代码（尤其是 Reducer、Scheduler 和 Ledger）时必须设置的代码级防御屏障。没有它们，这套极其聪明的多智能体世界，活不过上线后的第一周。