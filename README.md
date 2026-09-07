# researchOS

面向多种研究任务的、由人指导的科研执行与改进框架。

**当前状态：设计基线 v0.1。只有文档与示例，尚无可运行 CLI、MCP 服务、调度器或 Codex 适配器。** 文档中的操作名是待实现协议，不是安装后即可使用的命令。ACT 示例不代表已接入该仓库或已完成任何实验。

## 项目目标

将新的研究项目接入统一流程，保留问题、假设、实验、证据和决策；使模型与 Agent 工作方式可以升级，并通过独立评估检验系统改进。研究代码保留在各自仓库，researchOS 提供通用流程和执行基础设施。

人和 Agent 都可以提出问题、设计实验与解释结果。人决定研究目标、投入边界和重大方向调整；Agent 在已约定范围内自主执行。系统应报告证据不足，不能把运行成功等同于科学结论成立。

## 阅读顺序

1. [架构与职责](docs/architecture.md)
2. [独立仓库与 Codex 接入](docs/workspace-and-codex.md)
3. [任务、运行和证据协议](docs/contracts.md)
4. [自我改进与模型升级](docs/evolution.md)
5. [研究依据及局限](docs/research-basis.md)
6. [实施路线与验收](ROADMAP.md)
7. [已确认决策与待决问题](docs/decisions.md)

## 第一版方向

- 先支持用户在研究项目中直接使用 Codex，由 Codex 调用 researchOS 工具。
- 通用核心处理持久化、作业生命周期、预算、版本和证据关联。
- 研究策略、模型、Agent 数量和分工可替换。
- 通过两个不同领域的轻量任务检验接入通用性。
- 在可重复运行与评估之后，再增加经验复用和自动策略改进。

## 示例

[项目声明](examples/act/project.yaml)、[本机路径绑定](examples/act/local.example.yaml)、[假设任务](examples/act/task.example.json)和[项目接入说明片段](examples/act/AGENTS.snippet.md)均为设计样例。路径、实验入口和评价器需在真实接入时验证。

研究产物归研究项目或外部产物存储管理，不放入 researchOS 源码仓库。数据、模型权重和凭据不应提交到本仓库。
