# MC Harness Bridge — Convention

本仓库描述 MC Harness Bridge 的开发与协作方式：如何拆任务、使用 Git 和 worktree、跨仓库演进接口、保存验证证据并交付给用户。

**状态：2026-10-12 首版草案，待审阅采纳。** 本稿以 `main + topic branches` 为建议基线，不沿用其他组织的整套流程。GitHub 分支保护、必需检查、发布自动化均尚未由本文配置；文档约定不等于服务器已强制执行。

## 阅读顺序

| 文档 | 用途 |
| --- | --- |
| [Git 工作流](git-workflow.md) | 基线、分支、提交、PR、合并与紧急修复 |
| [Worktree 管理](worktree-management.md) | Windows 本地布局、创建、隔离、跟踪与回收 |
| [跨仓库协作](cross-repository.md) | 产品任务分解、依赖关系、兼容组合与关闭条件 |
| [接口变更](interface-changes.md) | wire/schema/Skill/事件/快照的单一来源和兼容演进 |
| [验证与证据](verification.md) | 与改动相称的测试、真实游戏联调和非侵入门槛 |
| [文档约定](documentation.md) | 需求 ID、来源、决策状态、宣传与事实 |
| [Agent 协作](agent-workflow.md) | 授权边界、持续推进、共享现场、交接 |
| [发行与迁移](release-and-migration.md) | 多仓库版本、消费者验证、旧产品迁移 |

复用模板：[任务记录](templates/task-record.md)、[决策记录](templates/decision-record.md)、[验证记录](templates/evidence-record.md)、[PR 模板](.github/pull_request_template.md)。模板只保留与任务相关的字段，不要求每次修改都写完整长报告。

## 基本原则

1. 产品需求与开发流程分开。需求和设计归 [design](https://github.com/mc-harness-bridge/design)，各实现仓库负责代码与对应证据。
2. 每个任务有可识别分支和独立工作目录。根 clone 用于跟踪 `main`，具体修改在任务 worktree 完成。
3. 每个实现仓库一个可审阅 PR；一个产品任务可涉及多个 PR，但整体完成条件必须一致。
4. 合并、联调、发行和干净消费分别记录。历史证据、mock、build 成功不代替新产品真实行为。
5. 保护玩家现场、他人工作和未提交材料。观察默认不改世界；任务隔离不能靠覆盖现有工作。
6. 授权范围内持续推进。常规可逆修改/验证不反复索要确认；合并、发行、世界破坏和对外消息按明确授权处理。

## 仓库职责

| 仓库 | 协作 owner |
| --- | --- |
| [mc-harness-bridge](https://github.com/mc-harness-bridge/mc-harness-bridge) | 产品入口、跨仓库 issue、兼容组合与最终交付导航 |
| [design](https://github.com/mc-harness-bridge/design) | 哲学、稳定需求 ID、设计、决策和验收设计 |
| [convention](https://github.com/mc-harness-bridge/convention) | 开发政策来源、通用模板和流程变更 |
| [cli](https://github.com/mc-harness-bridge/cli) | Go CLI/daemon、安装发行、工具 Skill 与运行 Webhook |
| [mod](https://github.com/mc-harness-bridge/mod) | 游戏线程、权威数据、控制路径、客户端 adapter 与 capture |
| [agent-hooks](https://github.com/mc-harness-bridge/agent-hooks) | 接收方配置/profile、fixture、样例与原生互操作证据 |

## 采纳与例外

首版规则待 PR 审阅后成为基线；变更通过 convention PR 说明触发原因与受影响任务。提案、局部实验和已生效规则标明状态。局部 `AGENTS.md` 应保持简短，引用流程和仓库特有命令，避免复制多份规则后漂移。

用户明确指令优先于文档中的默认流程。遇到例外先检查当前授权和实际适用范围；能安全推进就继续，并在记录中解释取舍。只有确实缺少必要信息或权限时才提出具体问题，不用“规范写了例外”作为重复暂停的理由。
