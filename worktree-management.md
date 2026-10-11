# Worktree 管理

状态：首版本地布局和操作约定，面向手动 Git worktree。Codex app 管理的 worktree 遵循其附件/归档接口，见末节。

## 目录布局

```text
F:/mc-harness-bridge/
  cli/                  # 根 clone：main 跟踪基线
  mod/
  design/
  convention/
  agent-hooks/
  mc-harness-bridge/
  .worktrees/
    cli/<task>/         # topic 工作目录
    mod/<task>/
    design/<task>/
    convention/<task>/
  WORKSPACE.md           # 本地导航；不是产品文档唯一来源
```

`.worktrees` 位于组织工作区根，不放进某个仓库源目录。任务名用小写英文/数字/连字符，避免路径过长和 Windows 保留设备名。盘符/布局是本次 Windows 环境约定，可在其他机器使用不同根；不要把 F 盘写成产品运行依赖。

根 clone 留在 main 便于查看/更新；任务分支在其独立 worktree 工作。六仓库不是六个同一 Git repo 的 worktree，而是六个独立 clone，各自的 `.worktrees/<repo>/<task>` 才与根 clone 共享 Git 元数据。

## 创建前检查

```powershell
git -C F:/mc-harness-bridge/cli status --short --branch
git -C F:/mc-harness-bridge/cli worktree list --porcelain
git -C F:/mc-harness-bridge/cli fetch origin
git -C F:/mc-harness-bridge/cli branch --list
```

依次检查结果与退出码；确认目标路径不存在、分支未被其他 worktree 使用、当前任务没有可复用目录。fetch 完成后确认基准 commit。目标目录已存在时先识别其内容，不能用创建命令/递归删除清场。

空仓库没有 commit 时不能创建正常 topic worktree；先按 Git 工作流完成最小初始化。本轮 cli/mod/agent-hooks/产品入口仍为空，开始代码或入口文档时应先处理该基线。

## 创建与续作

下面是未来 CLI 任务的示例，路径与分支名应替换为实际任务，命令不是要求现在执行：

```powershell
New-Item -ItemType Directory -Path F:/mc-harness-bridge/.worktrees/cli -Force
git -C F:/mc-harness-bridge/cli worktree add -b feat/player-context F:/mc-harness-bridge/.worktrees/cli/player-context origin/main
git -C F:/mc-harness-bridge/.worktrees/cli/player-context status --short --branch
```

续作现有分支时先 `worktree list` 查路径；没有已有 checkout 才用 `worktree add <path> <existing-branch>`。不要用 `-B` 重置同名分支，也不要因任务名称类似就推定可接管他人的脏工作。

## 隔离的不只是源文件

worktree 共享 Git refs/objects/config；它不是安全沙箱、不是独立用户环境。不要在一个任务里修改会影响所有 worktree 的 repo config、hooks 或公共 refs 而不说明。

| 资源 | 隔离要求 |
| --- | --- |
| 游戏实例 | 每任务明确测试 gameDir、world、instance/run；禁止复用玩家正在游玩的世界作为破坏性测试材料 |
| daemon | 独立状态目录、IPC 地址/锁、target、日志与身份材料，不能通过启动器版本目录串线 |
| 端口 | 登记真实游戏端口；产品控制共享该实例游戏端口，测试不额外捏造必需控制端口 |
| 输出/下载 | 每任务独立 artifact/temp 目录；标明来源，避免“同名快照”覆盖 |
| 缓存/工具链 | 可共享可验证缓存；共享 cache 的清理与版本修改不能影响正在运行的任务 |
| 环境变量 | 用任务专用配置；不覆盖 HOME/CODEX_HOME 等系统/工具变量来伪装隔离 |
| 凭证 | 本机受保护配置；不进 Git、不打印到日志或验证报告 |

两 worktree 同时联调前登记进程/端口/实例。测试已停止必须实际确认，不能因终端关闭就假定游戏/daemon 已退出。

## 登记与交接

建议本地 registry 或 WORKSPACE 记录：repo、branch、worktree 绝对路径、base commit、task/owner、状态、PR、游戏/daemon资源、最近验证和未提交/ignored 资料位置。记录保持简短；正式任务结论仍进 PR/issue。

本次两个任务目录为 `design/product-foundation` 与 `convention/workflow-foundation`。它们是手动 Git worktree，不冒称 Codex app 管理附件。

## 回收

1. 确认任务已交付/明确放弃，目录不是当前活跃任务，也没有其他终端/Agent 使用。
2. 检查 status、未 push commit、untracked 与 ignored 文件；需要的日志/产物另存，确认恢复来源。
3. 停止该任务启动的测试进程，检查端口/锁；不能停止别人的实例。
4. 从根 clone 用 `git worktree remove <absolute-path>` 移除**干净**任务目录；失败就检查原因，默认不加 `--force`。
5. 更新 registry。分支仅在已合并/明确放弃且保存必要工作后用 `git branch -d` 删除；不盲用 `-D`。

Windows 任何递归删除/移动之前都要解析最终绝对路径，确认处于明确目标工作区，并检查不等于组织根/盘根/根 clone。使用单一 PowerShell 的 `-LiteralPath`；不把枚举路径拼进 cmd 或第二个 shell。正常回收优先 Git 命令，不直接递归删除 `.git`/worktree。

`worktree prune` 只清理失效元数据，不是保存工作或删除目录的替代品。移动用 `git worktree move`；元数据失配再检查 `repair`，不要编辑 `.git` 指针碰运气。

## Codex app 管理的 worktree

如果任务由 app 的 create_worktree 创建，应通过附件列表识别并使用 archive_worktree 归档，保留可恢复 snapshot；需要的 ignored 文件先单独保存。不能对 app 管理目录套用手动删除流程，也不能以 Git 路径存在就假定拥有该附件。

本次采用手动 worktree，是为了建立明确的 F 盘多仓库布局。它与 app 自有工作区可以共存，但登记 owner、资源和回收机制必须分清。
