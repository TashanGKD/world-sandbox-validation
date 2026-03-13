# 沙盘推演 480 任务测试执行记录 · SYS（50 条）

> 自动生成 | 2026-03-13

---

### 111. SYS-001 — Context 创建—参与—关闭全生命周期
- **类型** SYS | **EXEC** EXEC-1 | G G1/G3/G6 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 112. SYS-002 — bounded session 截止后级联清栈
- **类型** SYS | **EXEC** EXEC-1 | G G1/G3/G6 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 113. SYS-003 — persistent context 在长期运行中的归档
- **类型** SYS | **EXEC** EXEC-1 | G G3/G7 | PA PA2/PA6 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 114. SYS-004 — ForegroundStack push/pop/repair
- **类型** SYS | **EXEC** EXEC-1 | G G2/G6 | PA PA4/PA5 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 115. SYS-005 — 前景场关闭后 resume_token 恢复原任务
- **类型** SYS | **EXEC** EXEC-1 | G G3/G4/G6 | PA PA4/PA8 | T T3/T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 116. SYS-006 — 物理场与前景场动作并集判定
- **类型** SYS | **EXEC** EXEC-1 | G G5 | PA PA7/PA9 | T T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 117. SYS-007 — 低头族被点名的最小本地响应
- **类型** SYS | **EXEC** EXEC-1 | G G5 | PA PA7/PA9 | T T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 118. SYS-008 — energy 归零进入 recovering 并带迟滞锁
- **类型** SYS | **EXEC** EXEC-1 | G G3/G4 | PA PA3/PA8 | T T3/T4
- **推演结论** 完整场景（场景一）
- **判定** P1 | **证据** E1

### 119. SYS-009 — recovering 期间屏蔽非紧急唤醒
- **类型** SYS | **EXEC** EXEC-1 | G G3/G4 | PA PA3/PA8 | T T3/T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 120. SYS-010 — sleeping 状态下 hazard 唤醒优先级
- **类型** SYS | **EXEC** EXEC-1 | G G5/G8 | PA PA4/PA7 | T T3/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 121. SYS-011 — sleep_debt 累积与偿还闭环
- **类型** SYS | **EXEC** EXEC-1 | G G3 | PA PA3 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 122. SYS-012 — Notification surfaced/acked/expired/merged
- **类型** SYS | **EXEC** EXEC-1 | G G3/G5 | PA PA6 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 123. SYS-013 — Notification fanout bounded + backpressure
- **类型** SYS | **EXEC** EXEC-1 | G G3/G5/G8 | PA PA2/PA7 | T T3/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 124. SYS-014 — 重复通知 collapse/merge
- **类型** SYS | **EXEC** EXEC-1 | G G3/G8 | PA PA2/PA6 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 125. SYS-015 — Obligation proposed→expired 全状态机
- **类型** SYS | **EXEC** EXEC-1 | G G4/G6 | PA PA6/PA8 | T T3/T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 126. SYS-016 — Obligation blocked 后重新获得推进机会
- **类型** SYS | **EXEC** EXEC-1 | G G4/G6 | PA PA4/PA8 | T T3/T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 127. SYS-017 — Identity-based obligation filtering
- **类型** SYS | **EXEC** EXEC-1 | G G4/G6 | PA PA7/PA8 | T T4/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 128. SYS-018 — 循环阻塞义务的 deadlock breaker
- **类型** SYS | **EXEC** EXEC-1 | G G4 | PA PA8 | T T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 129. SYS-019 — WriteEvent-first durable 写回
- **类型** SYS | **EXEC** EXEC-1 | G G1/G7 | PA PA5/PA6 | T T2/T3
- **推演结论** 完整场景（场景二）
- **判定** P2 | **证据** E1

### 130. SYS-020 — ActionReceipt 到达前禁止 durable 成功写回
- **类型** SYS | **EXEC** EXEC-1 | G G1/G7 | PA PA5 | T T2
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 131. SYS-021 — WritebackLedger replay 幂等
- **类型** SYS | **EXEC** EXEC-1 | G G1/G7 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 132. SYS-022 — StagingLedger 长滞留清理
- **类型** SYS | **EXEC** EXEC-1 | G G3/G7 | PA PA2/PA6 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 133. SYS-023 — resource lease 获取/续租/过期/释放
- **类型** SYS | **EXEC** EXEC-1 | G G6/G7 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 134. SYS-024 — lease 过期后旧写入被 fencing token 拒绝
- **类型** SYS | **EXEC** EXEC-1 | G G7 | PA PA5 | T T2
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 135. SYS-025 — emit→route→apply 四阶段闭环
- **类型** SYS | **EXEC** EXEC-1 | G G1/G5 | PA PA1/PA9 | T T2/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 136. SYS-026 — 乱序消息不破坏真源
- **类型** SYS | **EXEC** EXEC-1 | G G1/G7 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 137. SYS-027 — 网络分区后的 split-brain 修复
- **类型** SYS | **EXEC** EXEC-1 | G G7 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 138. SYS-028 — 掉线重连后的状态重建
- **类型** SYS | **EXEC** EXEC-1 | G G7 | PA PA3/PA4 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 139. SYS-029 — 系统重启后的 replay + jitter
- **类型** SYS | **EXEC** EXEC-1 | G G7/G8 | PA PA3/PA6 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 140. SYS-030 — 时钟漂移下 TTL/截止语义保持一致
- **类型** SYS | **EXEC** EXEC-1 | G G7 | PA PA1/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 141. SYS-031 — 大规模群聊 mention 风暴降载
- **类型** SYS | **EXEC** EXEC-1 | G G8 | PA PA2/PA7 | T T3/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 142. SYS-032 — 全局 overload_mode 切换与退出
- **类型** SYS | **EXEC** EXEC-1 | G G8 | PA PA2/PA4 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 143. SYS-033 — 日志 compaction / archive / snapshot
- **类型** SYS | **EXEC** EXEC-1 | G G7/G8 | PA PA2/PA6 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 144. SYS-034 — 继续运行链 continue-as-new / rollover
- **类型** SYS | **EXEC** EXEC-1 | G G7/G8 | PA PA2/PA6 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 145. SYS-035 — 世界级地震触发 hazard broadcast
- **类型** SYS | **EXEC** EXEC-2 | G G5/G8 | PA PA4/PA7/PA9 | T T3/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 146. SYS-036 — 火山/火灾/停电导致场强制关闭迁移
- **类型** SYS | **EXEC** EXEC-2 | G G5/G8 | PA PA4/PA6/PA9 | T T3/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 147. SYS-037 — 物理道路封锁改变可达 field 与 obligation
- **类型** SYS | **EXEC** EXEC-2 | G G5/G8 | PA PA7/PA9 | T T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 148. SYS-038 — 医院超载/资源稀缺场景的抢占调度
- **类型** SYS | **EXEC** EXEC-2 | G G3/G4/G8 | PA PA3/PA4/PA8 | T T3/T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 149. SYS-039 — 多 agent 协作任务的 participant/role 一致性
- **类型** SYS | **EXEC** EXEC-2 | G G5/G6 | PA PA5/PA7/PA9 | T T2/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 150. SYS-040 — 多人同时 claim 同一 deliverable 的冲突仲裁
- **类型** SYS | **EXEC** EXEC-2 | G G6/G7 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 151. SYS-041 — 长期无净进展但消息活跃的伪活性识别
- **类型** SYS | **EXEC** EXEC-2 | G G4 | PA PA8 | T T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 152. SYS-042 — 合法静息期不被误判为空转
- **类型** SYS | **EXEC** EXEC-2 | G G4 | PA PA8 | T T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 153. SYS-043 — 从 rest→work→rest 的日周期长期稳定
- **类型** SYS | **EXEC** EXEC-2 | G G3/G4 | PA PA2/PA3/PA8 | T T3/T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 154. SYS-044 — 跨日、跨周累计任务与精力债务平衡
- **类型** SYS | **EXEC** EXEC-2 | G G3/G4 | PA PA2/PA3/PA8 | T T3/T4
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 155. SYS-045 — 海量 context 共存下路由局部化
- **类型** SYS | **EXEC** EXEC-2 | G G3/G5/G8 | PA PA2/PA7/PA9 | T T3/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 156. SYS-046 — 海量 agent 并发下不全局扫描
- **类型** SYS | **EXEC** EXEC-2 | G G8 | PA PA2 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 157. SYS-047 — 模型调用失败不阻塞热路径
- **类型** SYS | **EXEC** EXEC-2 | G G8 | PA PA4 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 158. SYS-048 — 慢存储不阻塞热路径
- **类型** SYS | **EXEC** EXEC-2 | G G8 | PA PA4 | T T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 159. SYS-049 — 人工强制介入与系统自动规则的边界
- **类型** SYS | **EXEC** EXEC-2 | G G6/G7 | PA PA5/PA9 | T T2/T5
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

### 160. SYS-050 — 规则热更新不破坏已有 durable 真源
- **类型** SYS | **EXEC** EXEC-2 | G G7 | PA PA5/PA6 | T T2/T3
- **推演结论** 文档符合性检查通过
- **判定** P1 | **证据** E0

