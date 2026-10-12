# MC Harness Bridge — Convention

本仓库描述 MC Harness Bridge 的开发与协作方式：如何拆任务、使用 Git 和 worktree、跨仓库演进接口、保存验证证据并交付给用户。

**状态：2026-10-12 首版草案，待审阅采纳。** Git 工作流采用 `main + topic branches`。

## 阅读顺序

| 文档 | 用途 |
| --- | --- |
| [Git 工作流](git-workflow.md) | 基线、分支、提交、PR、合并与紧急修复 |
| [Worktree 管理](worktree-management.md) | Windows 本地布局、创建、隔离、跟踪与回收 |
| [跨仓库协作](cross-repository.md) | 产品任务分解、依赖关系、兼容组合与关闭条件 |
| [接口变更](interface-changes.md) | wire/schema/Skill/事件/快照的单一来源和兼容演进 |
| [验证与证据](verification.md) | 与改动相称的测试、真实游戏联调和非侵入门槛 |
| [文档约定](documentation.md) | 需求 ID、来源、决策状态与维护 |
| [Agent 协作](agent-workflow.md) | 持续推进、共享现场与交接 |
| [发行与迁移](release-and-migration.md) | 多仓库版本、消费者验证、旧产品迁移 |

复用模板：[任务记录](templates/task-record.md)、[决策记录](templates/decision-record.md)、[验证记录](templates/evidence-record.md)、[PR 模板](.github/pull_request_template.md)。模板按任务复杂度保留相关字段。

## 基本原则

1. 产品需求与开发流程分开。需求和设计归 [design](https://github.com/mc-harness-bridge/design)，各实现仓库负责代码与对应证据。
2. 每个任务有可识别分支和独立工作目录。根 clone 用于跟踪 `main`，具体修改在任务 worktree 完成。
3. 每个实现仓库一个可审阅 PR；一个产品任务可涉及多个 PR，但整体完成条件必须一致。
4. 按改动选择验证，记录联调、发行与消费者验证结果。
5. 使用独立任务目录与测试资源，保留已有工作和玩家现场。
6. 按当前授权持续推进，在交付中说明结果与剩余工作。

## 仓库职责

| 仓库 | 协作 owner |
| --- | --- |
| [mc-harness-bridge](https://github.com/mc-harness-bridge/mc-harness-bridge) | 产品入口、跨仓库 issue、兼容组合与最终交付导航 |
| [design](https://github.com/mc-harness-bridge/design) | 哲学、稳定需求 ID、设计、决策和验收设计 |
| [convention](https://github.com/mc-harness-bridge/convention) | 开发政策来源、通用模板和流程变更 |
| [cli](https://github.com/mc-harness-bridge/cli) | Go CLI/daemon、安装发行、工具 Skill 与运行 Webhook |
| [mod](https://github.com/mc-harness-bridge/mod) | 游戏线程、权威数据、控制路径、客户端 adapter 与 capture |
| [agent-hooks](https://github.com/mc-harness-bridge/agent-hooks) | 接收方配置/profile、fixture、样例与原生互操作证据 |

## 流程维护

首版规则待 PR 审阅后成为基线；变更通过 convention PR 说明触发原因与受影响任务。提案、局部实验和已生效规则标明状态。局部 `AGENTS.md` 应保持简短，引用流程和仓库特有命令，避免复制多份规则后漂移。

用户明确指令优先于默认流程。任务中需要调整约定时，记录原因和适用范围。
