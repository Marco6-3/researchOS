# 协议草案 v0.1

状态：语义设计，无已实现 CLI 或可执行 schema。字段可在实现阶段调整，但应记录迁移。

## 核心对象

| 对象 | 最小信息 |
|---|---|
| Project | schema_version、project_id、仓库、能力与环境声明、执行入口、评价声明 |
| Task | task_id、project_id、问题、假设或探索模式、证据区分标准、约束、预算、状态 |
| Run | run_id、task_id、尝试编号、版本清单、backend、job_id、状态、时间、产物 |
| Evidence | evidence_id、run_id 或外部来源、URI、摘要校验、采集时间、方法、局限 |
| Claim | claim_id、陈述、支持/反对证据 ID、适用范围、不确定性 |
| Decision | decision_id、关联任务、采用行动、理由、证据引用、提出者与决定者 |
| Handoff | 任务状态版本、未完成事项、正在运行的作业、下一步、阻塞条件 |

Run 的版本清单包含研究代码 commit 和 dirty patch 摘要、配置摘要、数据版本/摘要、环境锁文件、researchOS commit、策略/模型标识、评价器版本及可控随机种子。模型服务不提供精确快照时显式登记此限制，不声称完全可复现。

代码有未提交修改时必须保存 patch 产物或拒绝启动要求可复现的运行；只记录 HEAD 不足以描述真实代码。

## 状态机

任务：draft → ready → active → completed；允许从活动状态进入 blocked、paused、cancelled。任务 completed 需要对应的交付与证据检查，不等于科学假设得到支持；负结果也能完成研究任务。

运行：queued → running → succeeded / failed / cancelled；状态无法确认时为 unknown。恢复 unknown 先协调 backend，不直接复制启动。checkpoint 恢复新建 attempt，并引用上次尝试。

任务和运行分开：某个实验失败不必使整个任务失败；进程 succeeded 不代表结论有效。

## 通用操作

| 操作 | 输入 | 输出/规则 |
|---|---|---|
| context | project_id、task_id、上下文预算 | 带来源和状态版本的必要上下文 |
| start_run | task_id、spec、幂等键 | run_id、job_id、启动状态；重复请求返回同一运行 |
| run_status | run_id | backend 实际状态、最后观察时间 |
| record_evidence | 来源、产物引用、摘要、局限 | evidence_id；验证产物可读及来源关联 |
| record_decision | 任务、行动、依据、预期状态版本 | decision_id；冲突时不覆盖旧决策 |
| handoff | task_id、预期状态版本、未完事项 | 持久化交接快照与可恢复入口 |

每项操作返回协议版本、请求 ID、状态、结果或结构化错误；错误包括 unsupported_version、invalid_input、conflict、permission_denied、missing_artifact、backend_unavailable。对网络重试与启动请求使用幂等键。状态更改以事务或等效原子机制执行；模型不能直接编辑数据库代替操作。

预算区分模型费用、GPU 时间、墙钟时间和最大尝试数。记录预估与实际消耗；不支持的计量项标为 unknown。运行前检查剩余额度，运行期间按 backend 能力检查；不可中断作业声明这一限制。

## 证据与存储

原始证据保留；修正创建新记录并指向被替代记录，不覆盖历史。对小规模证据可在项目 Git 中保存可分享索引，运行数据库为程序状态，二者同步必须明确定义，避免成为互相冲突的两个真相来源。

缓存可重建；运行状态与无法重建的产物必须备份。不同机器使用逻辑 artifact ID 与本机存储映射，不依赖临时绝对路径。

成功指标缺失时先完成评估设计，允许标记 awaiting_evaluation。定性研究使用明确 rubric 和人类判断，不能为了统一格式伪造一个精确分数。

## Agent 调用协议

输入 TaskEnvelope：项目与 task_id、工作目录、上下文引用、可用操作、预算、能力边界、预期输出及协议版本。

输出 AgentResult：任务/运行 ID、执行状态、证据/产物/决策引用、阻塞原因、后续建议和可获取的成本。

适配器验证结构与真实产物，然后更新核心状态。结构化输出格式正确不证明内容真实。能力协商列出是否支持工具调用、结构化输出、事件、恢复和取消；缺失能力使用有记录的降级方式或返回不支持。
