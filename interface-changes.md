# 接口与兼容性变更

接口包括 wire frame、请求/响应 schema、CLI JSON/退出码、事件/游标、snapshot manifest、capability、Webhook/profile、安装配置和工具 Skill。不能只把 Java/Go 函数签名变化当作接口变化。

## 单一来源

schema 与跨语言 fixture 的唯一维护位置由 design Q-002 决定。该问题未定版前，接口 PR 必须标明候选来源与消费方式，不将两端各自写出的 JSON 例子称为正式规范。

每个接口需有 owner、版本、支持边界、错误/unknown/partial、大小与时序限制。生成文件标注来源/生成方式；手写副本必须移除或明确仅为示意，避免第三份真相。

## 变更说明

PR 必须回答：

1. 哪个需求触发变化，调用者观察到什么新行为？
2. 哪些 producer/consumer/Skill/安装配置受影响？
3. 新旧组合的行为是什么：兼容、明确拒绝还是需迁移？
4. 字段缺失、额外字段、null、空值、零值、unknown 和错误有何语义？
5. 如何测试真实游戏/接收方，而不是仅验证自己生成的 fixture？

不改变字段名也可能破坏语义。例如把 `completed` 从“受理完成”改成“游戏动作成功”，或把 wait 的时钟从 server loop 改成 game time，都是行为契约变化。数值范围、默认权限、排序、过滤 cursor、快照覆盖与重试窗口也属于兼容判断。

## 版本维度

| 维度 | 表达 |
| --- | --- |
| 产品版本 | 用户安装的 CLI/Mod/Skill 组合 |
| wire 版本 | 双端可否建立控制会话与解释 frame |
| response schema | Agent 能否理解 CLI 结果 |
| event/manifest schema | 消费者能否解码与正确使用产物 |
| harness profile | 与具体 Hermes/接收方版本的互操作 |

版本字段/编号方案待设计定版；不要用一个 CLI semver 代替所有兼容事实。首版不承诺无限向后兼容，但不支持的组合要尽早明确拒绝，不能先执行写再告诉调用者版本错配。

## 能力发现

capability 反映当前目标、权限和实际支持，而不是产品愿望清单。客户端功能在专服上不可用、快照覆盖不完整、Webhook 未配置都应有机器可判断状态/原因。

新增功能不能让默认观察带来写、区块加载或流程编排。需要副作用时显式请求并说明权限、冲突和后置状态。原始未知不能通过 CLI 包装成成功。

## 验证组合

至少有 golden fixture/边界反例、跨语言消费和涉及行为的真实联调。旧 producer + 新 consumer、新 producer + 旧 consumer 的处理均写清；没有兼容目标的组合也测试明确拒绝。

新 schema、help 和工具 Skill 在同一交付内更新；agent-hooks fixture/profile 对应精确版本，不独立发明一套通用协议。实验方法的变动不应驱动核心工具 schema 每次随任务重写。
