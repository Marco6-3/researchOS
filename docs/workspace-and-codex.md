# 独立仓库与 Codex 接入

状态：接入方案，尚未实现命令。

## 工作区布局

示例以 workspace 为目录，不要求相邻或特定盘符。

| 位置 | 内容 |
|---|---|
| workspace/researchOS/ | 一份框架 checkout，可供多个项目使用 |
| workspace/ACT/ | 研究项目独立 Git 仓库 |
| workspace/other-project/ | 第二个独立项目 |
| workspace/research-data/ | 独立备份的实验产物 |

每个研究项目包含可提交的 research/project.yaml、research/tasks/、research/decisions/ 和证据索引；本机 .researchos/local.yaml、作业状态与缓存排除出 Git。需要跨机器恢复的状态必须由导出或同步机制持久化，git clone 本身不复制运行数据库或外部产物。

本机绑定解析相对路径时，以绑定文件所在目录为基准；绑定 framework_root、artifact_root 等位置。共享配置使用逻辑 ID，不包含个人机器绝对路径。运行记录固定框架提交、策略版本和依赖环境，避免一次 git pull 静默改变正在运行的实验。

## 接入过程：拟实现

1. 检查项目 Git 状态、原有 AGENTS.md、运行入口、解释器和评价器；未知信息登记为待补充。
2. 生成项目声明和本机路径绑定，验证实际目录与所需访问能力。
3. 合并最短接入说明，保留原有项目规则；不覆盖整份 AGENTS.md。
4. 生成版本固定的会话上下文入口，包含任务、证据、作业状态及相关流程。
5. 执行接入自检；重复接入不能重复插入说明、覆盖用户编辑或重复创建任务。

不得把 framework 的开发 AGENTS.md 当成所有研究项目的科研策略。两者目的不同；项目只加载明确的使用协议与被选择的策略。

## 第一种模式：人启动 Codex

用户在研究项目目录打开 Codex，使用项目入口恢复工作。Codex 获取任务上下文，调用 researchOS 操作登记与执行实验、保存证据及交接。

Agent 对研究步骤负责，程序对状态写入和作业管理负责。早期入口可显式读取文件；操作实现后由命令行工具返回结构化上下文。后续 MCP 是同一核心操作的另一种传输方式。

项目目录并列不意味着自动发现指令。Codex 的项目指令发现沿项目根目录到工作目录进行，接入必须显式提供 researchOS 流程入口。[官方 AGENTS.md 文档](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

本地 CLI 可用 --cd 选择工作目录、--add-dir 添加所需可写目录。--add-dir 不是指令导入机制。宿主环境可能限制路径读取；读写能力必须单独检查，不能把路径声明当授权。[官方 CLI 文档](https://learn.chatgpt.com/docs/developer-commands?surface=cli)

上述参数描述本地 CLI，不假定桌面、IDE、云端或 Work Mode 有完全相同的目录挂载与参数。其他宿主通过单独适配器声明能力。

## 第二种模式：系统启动 Codex

后续适配器接收任务包，设置研究项目或隔离 worktree 为工作目录，启动非交互 Codex，消费事件并验证产物。

Codex exec 支持 JSONL 事件和按 JSON Schema 返回最终响应。具体可用参数在实现时核对安装版本。[官方非交互模式文档](https://learn.chatgpt.com/docs/non-interactive-mode)

两种模式共用项目状态。适配器不依赖某个聊天线程永远存在，也不要求内部推理记录可导出；保存可观察的工具操作、结果、明确决策与引用即可。

## 长作业与并发

长时间训练由独立 runner 管理，返回 run_id 与 backend job_id。恢复先查询实际作业和 checkpoint，再决定是否重启；不以 Codex 退出码推断训练成功。

本地后台进程只能承诺其 backend 声明的存活能力，不能承诺机器重启后自动恢复。未知状态先标记 unknown 并协调查询，避免重复运行。

并发改代码使用独立 worktree 或等效隔离；同一任务状态使用事务和版本检查，设备与独占 GPU 等资源使用租约。凭据引用由宿主提供，不写入项目声明、上下文包或日志。

## 研究与系统开发

日常研究在研究仓库执行。系统改进在 researchOS 的候选分支进行，验证通过后明确升级项目绑定的版本。稳定运行中的框架不被候选原地替换。
