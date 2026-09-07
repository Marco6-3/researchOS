# 研究依据与边界

整理日期：2026-09-07。以下为一手论文和官方材料；结论仅对应其报告的设置，不是 researchOS 的实验结果。本文是架构选材说明，不是完整 RSI 文献综述。

| 来源 | 相关机制 | 借鉴与边界 |
|---|---|---|
| [AlphaEvolve，2025](https://deepmind.google/blog/alphaevolve-a-gemini-powered-coding-agent-for-designing-advanced-algorithms/) | 模型提出程序，自动评价与演化搜索 | 分开产生候选和验证；依赖可执行评价，不能直接外推到所有科研问题 |
| [Darwin Gödel Machine，2025，2026-03 更新](https://arxiv.org/abs/2505.22954) | 修改 Agent 代码、维护候选档案、基准验证 | 保留版本谱系与多条探索路径；编程基准提升不证明无限自我改进 |
| [ACE，2025](https://arxiv.org/abs/2510.04618) | 增量整理上下文经验，无须更新权重 | 有来源的经验与增量纠错；效果依赖反馈与任务 |
| [Hyperagents，2026-03](https://arxiv.org/abs/2603.19461) | 任务 Agent 与可修改的元 Agent | 改进方法本身可替换；多领域实验不等于所有新任务均有效 |
| [Recursive Harness Self-Improvement，2026-07](https://arxiv.org/abs/2607.15524) | 依据修订历史迭代工作流程规格 | 将信息流作为优化对象；合成研究任务、模型裁判的证据不能等同真实科研突破 |
| [On the Fragility of Self-Improving Agents，2026-08](https://arxiv.org/abs/2608.18066) | 对记忆型方法进行重复运行与顺序扰动复评 | 评价必须考虑随机性、任务顺序和描述不充分 |
| [OpenAI Research acceleration，2026-09](https://openai.com/index/research-acceleration-view-inside-openai/) | 人类指导下的研究任务自动执行 | 保留任务范围、人的判断和干预记录；内部使用数据是初步观察，不是全自动科研保证 |

## 本项目的推导

“小核心、可替换策略、独立评价”是基于以上材料作出的工程判断，不是某篇论文已证明的唯一最优架构。

不将 Agent 数量、Token 消耗、代码量或记忆条数直接视为科研进步。不将提示更新、模型权重训练和外部模型升级混为一谈。

## Codex 接入资料

- [AGENTS.md 指令发现](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
- [CLI 工作目录及额外目录](https://learn.chatgpt.com/docs/developer-commands?surface=cli)
- [非交互执行、事件与结构化结果](https://learn.chatgpt.com/docs/non-interactive-mode)

实现接入器时检查安装版本和宿主能力；本地 CLI 的功能不能直接推定其他产品界面也支持。
