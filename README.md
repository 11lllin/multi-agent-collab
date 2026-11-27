# multi-agent-collab

Agent Skills 开放标准 skill：**多 agent 并行协作协议**。把"主 agent 只拆解派发与集成验证 + 多个子 agent 各自在隔离目录并行干活"的流程固化下来，一句话触发整套协作。

兼容 [Agent Skills 开放标准](https://agentskills.io)（Linux 基金会托管）的各类 agent：WorkBuddy、Claude Code、Codex、Gemini CLI 等。

## 它解决什么问题

单 agent 串行做多模块任务又慢又容易自己跟自己打架。这个 skill 定义了一套可复用的编排协议：

- **任务分工**：契约先行（目录边界 + 接口约定）→ 低耦合拆包 → 自包含任务书派发
- **沟通方式**：主 agent → worker 一次性任务书；worker → 主 agent 三段式回报（文件清单 / 验证原文 / 总结）；worker 间默认零通信
- **验收标准**：文件落盘复查、验证命令真跑（不信自报）、边界检查（无越界写入）、整体集成复验
- **进度反馈**：派发即报分工、过程不刷屏、失败如实打回、完成后统一汇报

## 安装

克隆本仓库后，把整个 `multi-agent-collab/` 文件夹（含 SKILL.md 与 references/）放进你的 agent 的 skills 目录：

| Agent | 用户级安装路径 |
|---|---|
| WorkBuddy | `~/.workbuddy/skills/multi-agent-collab/` |
| Claude Code | `~/.claude/skills/multi-agent-collab/` |
| Codex | `~/.agents/skills/multi-agent-collab/` |
| Gemini CLI | `~/.gemini/skills/multi-agent-collab/`（实验性） |

目录结构：

```
multi-agent-collab/
├── SKILL.md
└── references/
    ├── task-brief-template.md
    └── acceptance-checklist.md
```

新会话中自动生效（按 description 语义匹配触发）。

没有 skill 机制的 agent：可将 SKILL.md 协议全文作为 AGENTS.md / 系统提示词使用，references/ 下的模板照常引用。

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
