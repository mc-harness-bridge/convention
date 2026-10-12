# Worktree 管理

使用手动 Git worktree 管理任务目录。

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
  WORKSPACE.md           # 本地导航和任务登记
```

`.worktrees` 位于组织工作区根，不放进某个仓库源目录。任务名用小写英文/数字/连字符，避免路径过长和 Windows 保留设备名。其他机器可使用不同工作区根。

根 clone 留在 main 便于查看/更新；任务分支在其独立 worktree 工作。六仓库不是六个同一 Git repo 的 worktree，而是六个独立 clone，各自的 `.worktrees/<repo>/<task>` 才与根 clone 共享 Git 元数据。

## 创建前检查

```powershell
git -C F:/mc-harness-bridge/cli status --short --branch
git -C F:/mc-harness-bridge/cli worktree list --porcelain
git -C F:/mc-harness-bridge/cli fetch origin
git -C F:/mc-harness-bridge/cli branch --list
```

检查结果与退出码，确认基准 commit、目标路径和分支占用情况；已有任务目录优先续作。

空仓库先按 Git 工作流完成最小初始化，再创建 topic worktree。

## 创建与续作

创建 CLI 任务示例，路径和分支名替换为实际任务：

```powershell
New-Item -ItemType Directory -Path F:/mc-harness-bridge/.worktrees/cli -Force
git -C F:/mc-harness-bridge/cli worktree add -b feat/player-context F:/mc-harness-bridge/.worktrees/cli/player-context origin/main
git -C F:/mc-harness-bridge/.worktrees/cli/player-context status --short --branch
```

续作先用 `worktree list` 查路径；尚无 checkout 时用 `worktree add <path> <existing-branch>`。

## 隔离的不只是源文件

worktree 共享 Git refs/objects/config。修改公共 config、hooks 或 refs 时记录对其他任务的影响；测试运行资源单独隔离。

| 资源 | 隔离要求 |
| --- | --- |
| 游戏实例 | 每任务使用独立测试世界，记录 gameDir、world、instance/run |
| daemon | 独立状态目录、IPC 地址/锁、target、日志与身份材料 |
| 端口 | 登记真实游戏端口；产品控制共享该实例游戏端口 |
| 输出/下载 | 每任务使用独立 artifact/temp 目录，标明来源 |
| 缓存/工具链 | 可共享缓存；清理或版本修改前协调使用中的任务 |
| 环境变量 | 使用任务专用变量和配置路径 |
| 凭证 | 保存在本机受保护配置中；Git、日志和验证报告使用脱敏材料 |

并行联调登记进程、端口与实例，结束时确认进程和锁的状态。

## 登记与交接

建议本地 registry 或 WORKSPACE 记录：repo、branch、worktree 绝对路径、base commit、task/owner、状态、PR、游戏/daemon资源、最近验证和未提交/ignored 资料位置。记录保持简短；正式任务结论仍进 PR/issue。

## 回收

1. 确认任务已交付/明确放弃，目录不是当前活跃任务，也没有其他终端/Agent 使用。
2. 检查 status、未 push commit、untracked 与 ignored 文件；需要的日志/产物另存，确认恢复来源。
3. 停止该任务启动的测试进程，检查端口和锁。
4. 从根 clone 用 `git worktree remove <absolute-path>` 移除干净任务目录；失败时检查原因。
5. 更新 registry。分支在已合并/明确放弃且保存必要工作后用 `git branch -d` 删除。

`worktree prune` 清理失效元数据；移动目录用 `git worktree move`，元数据失配用 `git worktree repair`。工具管理的工作目录按该工具的归档机制回收。
