# multi-agent-collab

WorkBuddy skill：**多 agent 并行协作协议**。把"主 agent 只拆解派发与集成验证 + 多个子 agent 各自在隔离目录并行干活"的流程固化下来，一句话触发整套协作。

## 它解决什么问题

单 agent 串行做多模块任务又慢又容易自己跟自己打架。这个 skill 定义了一套可复用的编排协议：

- **任务分工**：契约先行（目录边界 + 接口约定）→ 低耦合拆包 → 自包含任务书派发
- **沟通方式**：主 agent → worker 一次性任务书；worker → 主 agent 三段式回报（文件清单 / 验证原文 / 总结）；worker 间默认零通信
- **验收标准**：文件落盘复查、验证命令真跑（不信自报）、边界检查（无越界写入）、整体集成复验
- **进度反馈**：派发即报分工、过程不刷屏、失败如实打回、完成后统一汇报

## 安装

克隆本仓库后，将内容放入 WorkBuddy 的用户级 skill 目录：

```
~/.workbuddy/skills/multi-agent-collab/
├── SKILL.md
└── references/
```

（Windows 路径：`C:\Users\Administrator\.workbuddy\skills\multi-agent-collab\`）

新会话中自动生效。

## 使用

触发语（任选其一，<任务> 换成实际内容）：

```
并行做：<任务>
多agent协作：<任务>
按协作协议把这个任务拆成3路并行：<任务>
```

可选参数（不写用默认）：并行度（默认 3）、输出目录、隔离方式（子目录 / git worktree）、额外验收命令。

## 流程概览

```
拆解定契约 → 准备隔离目录 → 生成任务书并并行派发 → 监控 → 集成验证 → 统一汇报
```

详见 [SKILL.md](./SKILL.md)、[任务书模板](./references/task-brief-template.md)、[验收清单](./references/acceptance-checklist.md)。

## License

MIT
