# 项目总控 Agent

版本：V3.1。

保留 1 Core + 1 项目状态模板 + 5 Skills 架构，附 Codex 项目级模型配置。这是供 Agent 读取的工作规范；总控只做分析、调度与验收，全部执行交给真实子 Agent。这是行为约定，不是宿主强制的权限隔离，也不含自动调度或上下文压缩程序。

## 结构与使用

- AGENTS.md：共同授权、路由、委派、失败处理与完成标准。
- PROJECT_STATE.md：带核验来源和验证证据的当前状态模板。
- .agents/skills/：development、testing、migration、release、maintenance，按任务加载。
- [.codex/config.toml](.codex/config.toml)：总控和普通执行子 Agent 的默认模型与推理强度。

把目录内容合并到目标项目根目录，保留 .agents 和 .codex 隐藏目录。已有 AGENTS.md、PROJECT_STATE.md、同名 Skill 或 .codex/config.toml 时先检查冲突并合并，不直接覆盖用户内容。模型配置只合并本包提供的模型字段，保留目标项目其他设置。实际加载和发现方式取决于宿主平台；未自动发现 Skill 时按 AGENTS.md 的路径读取相关说明。

首次启用先只读了解项目。用户要求实施、修复或初始化状态后，由总控在授权范围委派子 Agent 完成工作和必要状态写入。没有正式需求系统时，使用用户请求摘要与验收条件即可。

## 平台与模型

模型分工及升级条件统一见 [AGENTS.md 的模型分工](AGENTS.md#模型分工)：总控和困难执行子 Agent 使用高档，普通执行子 Agent 使用中档；困难实现也不由总控接手。无需为五个 Skill 分别创建 Agent 配置。

Codex 信任目标项目并加载 .codex/config.toml 后，项目模型默认值才会生效；显式调用参数、会话选择或自定义 Agent 配置可能覆盖默认值。安装后在目标项目新建任务，核对总控模型与推理强度，并在首次委派时核验子 Agent 配置。已有运行任务是否应用新配置由宿主决定，不能只凭配置文件已写入就宣布当前任务已切换。Markdown 和 Skill 本身不切换模型；其他宿主需使用其实际支持的配置或调用参数。

子 Agent 需要宿主提供真实委派工具。执行任务至少委派一个子 Agent；可独立拆分、可隔离且收益合理时并行，其余按序执行。同一执行子 Agent 可连续完成实现、测试和修复。无真实委派能力或无法启动执行者时，执行受阻，总控仅继续只读分析。Skill 加载不产生独立 Agent，也不自动卸载上下文；本方案通过按需读取、简短交接和状态快照减少后续重复信息，实际上下文或用量变化需测量。

模型分工以降低适合任务的执行成本为目标，不承诺减少 token 数量或按固定比例节省额度。用实际任务的验收结果、返工次数和总额度或费用评估效果。

配置依据：[官方子 Agent 文档](https://learn.chatgpt.com/docs/agent-configuration/subagents#choosing-models-and-reasoning)、[配置优先级](https://learn.chatgpt.com/docs/config-file/config-basic#configuration-precedence)。

## V3.1 调整

- 精简 Core，专项步骤放回对应 Skill。
- 总控只读分析、调度与验收；小任务也委派执行，简单任务采用简短交接。
- 用新增证据和阻塞条件管理返工，替代固定两轮后停工。
- 状态记录补充核验时间、来源、代码标识和授权核对线索。
- 区分 Skill 与 Agent，去除 Markdown 能强制模型或自动压缩的暗示。
- 测试失败回到实际负责角色，明确测试源码的修改边界。
- 补全初始化、无 Git、并行集成与任务状态语义。
- 增加项目级模型默认值、按难度升级规则及模型配置核验说明。
- 明确真实子 Agent 执行、按依赖并行、无执行者时阻塞，以及由子 Agent 完成集成和状态写入。

本包是模板，未安装到实际项目；适配项目后仍需结合真实任务观察其行为。
