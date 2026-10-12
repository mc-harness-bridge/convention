# 接口与兼容性变更

接口包括 wire frame、请求/响应 schema、CLI JSON/退出码、事件/游标、snapshot manifest、capability、Webhook/profile、安装配置和工具 Skill。

## 单一来源

schema 与跨语言 fixture 的唯一维护位置由 design Q-002 决定。该问题未定版前，接口 PR 必须标明候选来源与消费方式，两端消费同一来源。

每个接口需有 owner、版本、支持边界、错误/unknown/partial、大小与时序限制。生成文件标注来源/生成方式；示意样例标明用途。

## 变更说明

PR 必须回答：

1. 哪个需求触发变化，调用者观察到什么新行为？
2. 哪些 producer/consumer/Skill/安装配置受影响？
3. 新旧组合的行为是什么：兼容、明确拒绝还是需迁移？
4. 字段缺失、额外字段、null、空值、零值、unknown 和错误有何语义？
5. 哪些 fixture 与真实游戏/接收方联调覆盖改动？

字段名保持原样时也要检查语义兼容性。例如把 `completed` 从“受理完成”改成“游戏动作成功”，或把 wait 的时钟从 server loop 改成 game time，都是行为契约变化。数值范围、默认权限、排序、过滤 cursor、快照覆盖与重试窗口也属于兼容判断。

## 版本维度

| 维度 | 表达 |
| --- | --- |
| 产品版本 | 用户安装的 CLI/Mod/Skill 组合 |
| wire 版本 | 双端可否建立控制会话与解释 frame |
| response schema | Agent 能否理解 CLI 结果 |
| event/manifest schema | 消费者能否解码与正确使用产物 |
| harness profile | 与具体 Hermes/接收方版本的互操作 |

版本字段与编号方案待设计定版，版本冲突在操作执行前明确拒绝。

## 能力发现

capability 反映当前目标、权限和实际支持。客户端专有功能、快照覆盖范围与 Webhook 配置都应有机器可判断状态/原因。

观察保持只读。带副作用的操作显式请求并说明权限、冲突和后置状态；CLI 保留源端 unknown 等结果语义。

## 验证组合

至少有 golden fixture/边界反例、跨语言消费和涉及行为的真实联调。旧 producer + 新 consumer、新 producer + 旧 consumer 的处理均写清；没有兼容目标的组合也测试明确拒绝。

新 schema、help 和工具 Skill 在同一交付内更新；agent-hooks fixture/profile 对应确定版本和接收端契约。工具 schema 按产品接口演进，实验方法由 Agent 按任务调整。
