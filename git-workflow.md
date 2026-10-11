# Git 工作流

状态：建议采纳的首版流程。当前仓库初始化只有最小 README；本次实质文档在 topic 分支和草稿 PR 中审阅。

## 分支模型

采用 `main + topic branches`。`main` 是已审阅的集成基线，不自动代表已经发行或在所有目标组合验收。实际 release 由 tag、artifact 和兼容矩阵识别。

暂不建立长期 `develop`、`rc` 分支。需要并行维护稳定发行线、集中 RC 验证或多人长周期集成时，先记录触发需求和维护成本，再通过 convention PR 改变流程；不能把别的项目的 main/develop 语义静默带入本组织。

| 分支 | 使用 |
| --- | --- |
| `feat/<task>` | 新能力 |
| `fix/<task>` | 缺陷修复 |
| `docs/<task>` | 文档/设计 |
| `refactor/<task>` | 不改变公开行为的结构调整 |
| `chore/<task>` | 工具链、依赖、仓库维护 |
| `spike/<task>` | 有退出条件的探索，不直接宣称产品能力 |

名称用小写英文、数字和连字符；可加 issue 号，如 `feat/12-player-context`。同一任务跨仓库可用相同后缀，Git 分支名仍是各 repo 独立对象。禁止在同一 repo 两个 worktree 同时检出同一分支。

## 日常任务

1. 阅读任务范围、适用 `AGENTS.md`、设计需求与已有未提交工作。
2. fetch，确认基于哪一个 `origin/main` commit 创建 topic worktree；已有任务续作则复用其分支，不另造平行版本。
3. 实现并进行相称验证；同步改动涉及的 help、schema、Skill、fixture 和文档。
4. 查看完整 diff 和 status，只 stage 本任务文件；保存 commit 后 push topic 分支。
5. 创建 PR，先草稿还是 ready 由是否达到可审阅/可合并条件决定；跨仓库依赖清楚列出。
6. 处理反馈与失败，再由有授权的操作者合并。没有授权时交付可审阅 PR，不自行合并。

fetch 是读取远程状态，不代表可以覆盖本地修改。已有脏工作树不要 reset/clean；先识别 owner 和是否与当前任务有关。

## 提交

建议格式 `type: concrete change`，type 为 feat/fix/docs/refactor/test/chore/build/ci 等。标题说明改动行为，不用“update/fix stuff”。需要 breaking change 时在正文列公开接口影响与迁移路径。

提交按有意义的改动边界组织，不要求每个文件一个 commit。格式化、批量改名与行为变化能分开就分开，避免 reviewer 在无关 diff 中寻找问题。不要提交凭证、用户世界、依赖缓存、运行日志或本机绝对路径配置。

stage 前使用 `git status` 和 diff；共享或继承 worktree 中不能无判断 `git add .`。不可逆/破坏性 Git 操作（如丢弃他人修改）需要已有明确授权；正常补 commit、建立本任务分支和 push 该分支属于任务推进。

## PR 描述

先写具体问题与改后行为，必要时给触发条件和前后例子。列需求 ID、实际验证及限制、依赖 PR/精确版本、迁移影响。简单文档修改几句即可；复杂协议需附 schema/行为矩阵与证据。

- `Refs org/repo#N` 用于关联；不能把每个局部 PR 都写成 `Closes` 产品 issue。
- 只有完成整体验收的最终交付才使用 `Closes`，且要确认跨仓库 merge/release 条件已满足。
- CI 通过不能代替人工审阅和真实游戏证据。草稿 PR 也应能说明哪些检查完成、哪些还没有。
- PR 范围变化时改写标题和描述，反映最终方案，删去无关对话史。

通过 gh 创建/修改多行描述时，将正文写到临时文件并用 `--body-file`，保留换行和字面字符。不要把 Markdown 拼进可执行 shell 字符串。

## 同步、合并与撤销

自己的独占 topic 分支可以 rebase 更新；共享分支优先 merge，不能未经协调改写他人历史。发生冲突先理解双方意图；禁止通过一律 ours/theirs 丢失已确认需求。

需要更新远程已 push 历史时，只有独占分支且获得适用授权才使用 `--force-with-lease`；普通任务无需为追求漂亮历史而强推。`main` 不直接强推，不用于日常开发。

建议合并策略为 squash 保留单个可追踪任务 commit；需要保留重要实现阶段时可采用 merge commit。最终由仓库设置/审阅决定，当前未配置强制策略。已合并行为撤销通过 revert PR，并说明依赖与兼容影响；不要 reset 公共基线。

## 初始化与紧急修复

空 repo 可以一次性提交最小初始化文件到 main，形成可创建分支的基线；这不授权把实质设计/代码继续直接 push main。本轮 design/convention 已按此方式初始化。

紧急修复仍建立 `fix/` 分支并提供最小证据。若用户已明确要求立即合并/发行，在授权范围内完成；没有这类授权则先交付具体 PR 与影响说明。不能以“紧急”为由隐藏验证缺口或覆盖玩家世界。
