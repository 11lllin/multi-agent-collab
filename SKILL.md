---
name: multi-agent-collab
description: 多 agent 并行协作协议。当用户要求"并行做/多agent协作/分工/拆开做/并行处理/按协作协议"，或需要把一个任务拆成多个低耦合部分由多个子 agent 并行完成时使用。固化"主 agent 只拆解派发与集成验证 + 多个 worker 子 agent 各自在隔离目录独立干活"的流程：任务拆解、契约先行、任务书派发、验收复跑、进度汇报。
agent_created: true
---

# 多 agent 并行协作协议

## 目的

将"主 agent 编排 + 多 worker 并行"的协作方式标准化：主 agent 只做拆解、派发、集成验证、汇报，不写业务代码；每个 worker 子 agent 领自包含任务书，在自己的隔离目录独立干活并自测。目标是并行提速且零冲突。

## 触发条件

- 用户说"并行做 / 多 agent / 分工 / 拆开做 / 并行处理 / 协作协议"
- 任务可拆成 ≥2 个低耦合部分，且用户希望并行完成
- 用户明确引用本 skill（multi-agent-collab）

## 核心原则

1. **契约先行**：派发前先定目录边界 + 接口约定，写入契约（可放共享 CONTRACT.md 或直接写进各任务书）。
2. **目录隔离**：每个 worker 只许写自己的目录；正式项目用 git worktree（`git worktree add ../xxx -b feat/xxx`），演示/小任务用独立子目录即可。
3. **自包含任务书**：worker 看不到主对话历史，任务书必须独立成立（见 references/task-brief-template.md）。
4. **验收不信自报**：所有测试/脚本必须真实运行；主 agent 复查文件落盘与边界（见 references/acceptance-checklist.md）。
5. **过程不插手**：worker 干活期间主 agent 不干预；worker 间默认零通信（确需协调时走共享契约文件或互发消息）。

## 六步流程

### 1. 拆解与契约

将目标拆成低耦合任务包（典型 2-4 路）。为每包明确：交付物、目录归属、禁止触碰的文件/目录、与其它包的接口约定。耦合拆不干净时优先改拆法，而不是指望 worker 之间商量。

### 2. 准备隔离目录

- 小任务：`mkdir -p <root>/task-a task-b ...` 各包一个子目录。
- 正式项目：git worktree 一分支一 worker，从干净 main 切出。

### 3. 生成任务书并派发

按 references/task-brief-template.md 为每个 worker 生成任务书，用 Agent 工具派发（subagent_type: general-purpose），每个 worker 取唯一名字（如 worker-a/worker-b/...）。同一消息内并行发出多路派发以实现真正并发。任务书必须包含四要素：目标、边界、验收标准、回报格式。

### 4. 监控与进度反馈

- 派发后立即向用户报：已派 N 路 + 各路任务一句话。
- worker 过程不刷屏。用户要求"过程可见"时用后台运行 + 任务清单（TaskCreate/TaskUpdate）开看板。
- worker 间需要协调时：通过共享契约文件同步接口变更，或要求 worker 用 SendMessage 互发消息。

### 5. 集成验证

全部 worker 返回后，主 agent 按 references/acceptance-checklist.md 逐项执行：文件落盘复查、验证命令真跑、边界检查（无越界写入）、整体集成复验。任何一项失败即打回对应 worker 重修（可用 resume 续跑同一 agent），修不到绿不得汇报完成。

### 6. 统一汇报

向用户汇报：成果文件清单（路径）、各项验证结果原文、并行度与分工回顾、遗留问题如实标注。有交付物文件时用 present_files 呈上。

## 参数槽位

用户可在触发语中覆盖以下参数，缺省用默认值：

| 参数 | 默认 | 说明 |
|---|---|---|
| 并行度 | 3 | worker 路数 |
| 输出目录 | 任务给定或当前工作区 | 所有 worker 的根目录 |
| 隔离方式 | 子目录 | 正式项目改用 git worktree |
| 额外验收命令 | 无 | 例如指定 lint/测试命令，所有 worker 完成后统一执行 |

## 边界与禁止事项

- 主 agent 不写业务代码、不替 worker 干活。
- worker 不越界写文件、不合并到 main、不 force-push、不改共享依赖版本。
- 失败、卡住、未达验收一律如实报告，不掩盖、不谎报完成。
- 遵守用户的文件变更授权规则：创建/修改文件前如用户未授权，先说明要写什么再动手。

## 模板引用

- 任务书模板：references/task-brief-template.md（派发 worker 前加载，逐项填写）
- 验收清单：references/acceptance-checklist.md（集成验证时加载，逐项勾检）
