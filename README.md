<p align="center">
  <img src="docs/assets/tashan.svg" alt="他山 Logo" width="200" />
</p>

<p align="center">
  <strong>沙盘推演验证框架</strong><br>
  <em>Sandbox Simulation Validation Framework</em>
</p>

<p align="center">
  <a href="#项目简介">简介</a> •
  <a href="#核心内容">核心内容</a> •
  <a href="#六问判准">六问判准</a> •
  <a href="#生态位置">生态位置</a> •
  <a href="#贡献">贡献</a> •
  <a href="README.en.md">English</a>
</p>

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
![状态](https://img.shields.io/badge/状态-待开始-lightgrey)

> 🔲 **本仓库当前为骨架仓库，等待正式开发启动。**

---

## 项目简介

### 在大框架中的位置

「人—智能体混合数字世界」世界底座的第三层：**验证方法**。

公理体系（①）和体系结构（②）定义了"世界应该是什么"，但不能自证成立。本项目提供一套**沙盘推演**方法论，在具体状态与事件序列中验证世界底座是否真正闭环——并能将发现的问题追溯回①②的具体条目。

本项目不是普通的软件测试工具，而是**系统规范验证 + 理论元验证**的双重框架：如果某类问题在系统中稳定出现，但无法被归因到 T–PA–G 任何现有条目，则说明①本身存在遗漏，需要修订。

### 核心设计

每个有效沙盘场景必须包含四要素：
1. **初始状态**：系统在推演开始时的完整状态描述
2. **触发事件序列**：依次施加的事件
3. **演化推理链**：逐步推导系统如何响应（必须引用具体规则条目）
4. **终态判定与证据链**：最终状态是否合法，证据是什么

### 目标读者

- 验证工程师：为数字世界体系结构设计测试场景
- 研究者：研究规则体系的完备性与一致性
- 开发者：在开发过程中发现并定位体系问题

---

## 六问判准

沙盘推演通过以下六个问题将闭环检验操作化：

| # | 问题 | 验证内容 |
|---|------|---------|
| 1 | 事件是否到达主体？ | 路由与通知机制 |
| 2 | 是否被真正摄取？ | 主体感知与接收 |
| 3 | 是否允许打断当前活动？ | 中断优先级规则 |
| 4 | 若响应，如何输出？ | 响应生成路径 |
| 5 | 如何写回主体状态？ | 写隔离与状态更新 |
| 6 | 写回后如何继续调度？ | 调度与下一步规划 |

---

## 核心内容

> 📋 以下为规划内容，随开发进展持续填充。

- `方法论/`：沙盘推演完整方法论文档
- `场景库/`：已归档的有效沙盘场景（按触发源分类）
- `判准/`：六问判准详细规范
- `根因索引/`：G→PA→T 的根因追溯表
- `回归套件/`：标准回归验证场景集合

---

## 生态位置

| 层级 | 项目 | 仓库 | 类型 | 状态 |
|------|------|------|------|:----:|
| 世界底座 | ① 公理体系 | [world-axiom-framework](https://github.com/TashanGKD/world-axiom-framework) | 开源 | 🔲 |
| 世界底座 | ② 体系结构 | [world-three-particle-impl](https://github.com/TashanGKD/world-three-particle-impl) | 开源 | 🔲 |
| 世界底座 | **③ 沙盘验证** ← 本仓库 | [world-sandbox-validation](https://github.com/TashanGKD/world-sandbox-validation) | 开源 | 🔲 |
| 数字分身 | ④ 0→1构建 | [digital-twin-bootstrap](https://github.com/TashanGKD/digital-twin-bootstrap) | 开源 | 🟡 |
| 数字分身 | ⑤ 1→100迭代 | [digital-twin-iteration](https://github.com/TashanGKD/digital-twin-iteration) | 开源 | 🔲 |
| 核心应用 | 数字世界应用 | TashanGKD/tashan-world（私有） | 私有 | 🔲 |
| 商业化 | 数字分身平台 | TashanGKD/tashan-twin-platform（私有） | 私有 | 🔲 |
| 公益 | 他山论坛 | [tashan-forum](https://github.com/TashanGKD/tashan-forum) | 开源公益 | 🔲 |

**直接依赖关系**：
- 本仓库以 ①T–PA–G 为唯一判定基准
- 本仓库验证 ② 的具体实现路径（场景C）
- 发现实现缺口触发**场景D**（通知②修订），发现理论缺口触发**场景E**（通知①修订）

---

## 贡献

欢迎贡献！详见 [CONTRIBUTING.md](CONTRIBUTING.md)（待建）。

---

## 更新日志

见 [CHANGELOG.md](CHANGELOG.md)（待建）。

---

## 许可证

MIT License. See [LICENSE](LICENSE) for details.
