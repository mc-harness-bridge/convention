# Git 工作流

## 分支模型

采用 `main + topic branches`。`main` 是已审阅的集成基线；发行通过 tag、artifact 和兼容矩阵识别。

日常集成使用短期 topic 分支。需要并行维护稳定发行线、集中 RC 验证或多人长周期集成时，先记录触发需求和维护成本，通过 convention PR 调整流程。

| 分支 | 使用 |
| --- | --- |
| `feat/<task>` | 新能力 |
| `fix/<task>` | 缺陷修复 |
| `docs/<task>` | 文档/设计 |
| `refactor/<task>` | 保持公开行为的结构调整 |
| `chore/<task>` | 工具链、依赖、仓库维护 |
| `spike/<task>` | 有退出条件的探索 |

名称用小写英文、数字和连字符；可加 issue 号，如 `feat/12-player-context`。同一任务跨仓库可用相同后缀，每个 repo 分别维护分支，同一分支使用一个 worktree。

## 日常任务

1. 阅读任务范围、适用 `AGENTS.md`、设计需求与已有未提交工作。
2. fetch，确认基于哪一个 `origin/main` commit 创建 topic worktree；已有任务续作复用原分支。
3. 实现并进行相称验证；同步改动涉及的 help、schema、Skill、fixture 和文档。
4. 查看完整 diff 和 status，只 stage 本任务文件；保存 commit 后 push topic 分支。
5. 创建 PR，先草稿还是 ready 由是否达到可审阅/可合并条件决定；跨仓库依赖清楚列出。
6. 处理反馈与检查失败，按当前授权合并或交付 PR。

已有未提交修改时先识别归属，与当前任务分别处理。

## 提交

建议格式 `type: concrete change`，type 为 feat/fix/docs/refactor/test/chore/build/ci 等。标题说明具体改动行为。需要 breaking change 时在正文列公开接口影响与迁移路径。

提交按有意义的改动边界组织。格式化、批量改名与行为变化能分开就分开，避免 reviewer 在无关 diff 中寻找问题。Git 保存源码和必要 fixture，凭证、本地世界、缓存与运行产物保存在任务环境。

stage 前检查 `git status` 和 diff，选择本任务文件提交。

## PR 描述

先写具体问题与改后行为，必要时给触发条件和前后例子。列需求 ID、实际验证及限制、依赖 PR/精确版本、迁移影响。简单文档修改几句即可；复杂协议需附 schema/行为矩阵与证据。

- 局部 PR 用 `Refs org/repo#N` 关联产品 issue。
- 只有完成整体验收的最终交付才使用 `Closes`，且要确认跨仓库 merge/release 条件已满足。
- PR 记录 CI、相关联调结果和待完成检查。
- PR 范围变化时改写标题和描述，反映最终方案，删去无关对话史。

通过 gh 创建/修改多行描述时，将正文写到临时文件并用 `--body-file`，保留换行和字面字符。

## 同步、合并与撤销

独占 topic 分支可以 rebase 更新；共享分支优先 merge。冲突解决保留双方相关改动与已确认需求。

独占分支需要更新已 push 历史时使用 `--force-with-lease`；日常开发通过 topic PR 进入 `main`。

建议合并策略为 squash 保留单个可追踪任务 commit；需要保留重要实现阶段时可采用 merge commit。具体策略由仓库设置与审阅决定。已合并行为通过 revert PR 撤销，并说明依赖与兼容影响。

## 初始化与紧急修复

空 repo 先提交最小初始化文件到 main，形成基线；后续设计和代码使用 topic PR。

紧急修复使用 `fix/` 分支，提供相关验证、影响与发行说明，按当前授权交付。
