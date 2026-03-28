# 星链插件 API v2.9（轻量文档合约）

## 目标

为星链定义一份**轻量插件合约**，让插件能够被一致地理解、调用和编排，而不必过早引入过重的正式运行时系统。

这是一份**文档层 API 合约**，不是代码运行时 API。

## 插件对象模型

每个插件都应在文档或编排使用中声明这些概念字段：

- `name`
- `role`
- `phase`
- `defaultMode`
- `triggers`
- `inputs`
- `outputs`
- `escalatesWhen`
- `handsOffTo`

## 字段含义

### `name`
插件标识符。

### `role`
插件负责解决什么问题。

例如：
- 产品打磨
- 轻量规划
- 质量门
- 浏览器 QA
- 交付后复盘
- 局部护栏
- 轻量设计审查
- 根因调查

### `phase`
插件属于星链的哪个阶段。

建议值：
- `pre-plan`
- `lite-plan`
- `pre-build`
- `post-build`
- `qa`
- `post-delivery`
- `incident`

### `defaultMode`
插件默认如何工作。

建议值：
- `core`
- `optional`
- `conditional`
- `escalation-only`

### `triggers`
什么条件下应该启用该插件。

要使用可执行、可判断的语言，不要写成空泛产品术语。

### `inputs`
插件期望收到什么上下文。

例如：
- 任务描述
- 计划草案
- 范围变化说明
- 故障工作流说明
- 已审过的实现结果

### `outputs`
插件必须返回什么结构化结果。

例如：
- 切入点决策
- 执行计划
- 质量门判定
- QA 记录
- 复盘摘要
- 护栏配置
- 候选根因列表

### `escalatesWhen`
插件在什么情况下应要求升级到更重路线、更多研究或更大范围。

### `handsOffTo`
插件完成后通常应交给哪个下一个星链节点或插件。

## 规范插件映射

### `founder-office-hours`
- `role`：产品打磨
- `phase`：`pre-plan`
- `defaultMode`：L2 条件启用，L3 核心启用
- `handsOffTo`：`autoplan-lite` 或 Constitution-First 打磨层

### `autoplan-lite`
- `role`：轻量规划核心
- `phase`：`lite-plan`
- `defaultMode`：L2 核心
- `handsOffTo`：`coding` 或升级到 Full 主链

### `review-gate`
- `role`：质量门
- `phase`：`post-build`
- `defaultMode`：`core`
- `handsOffTo`：`test`、`qa-browser-check` 或回到 `coding`

### `qa-browser-check`
- `role`：浏览器 QA
- `phase`：`qa`
- `defaultMode`：`conditional`
- `handsOffTo`：`docs` 或回到 `coding`

### `release-retro`
- `role`：交付后学习沉淀
- `phase`：`post-delivery`
- `defaultMode`：`conditional`
- `handsOffTo`：memory、`.learnings`、checklist、plugin improvement

### `guard-mode`
- `role`：范围受限执行护栏
- `phase`：`pre-build` / `incident`
- `defaultMode`：`conditional`
- `handsOffTo`：`coding`、`review`、`investigate-root-cause`

### `design-review-lite`
- `role`：产品 / 设计轻量审查
- `phase`：`pre-build`
- `defaultMode`：`conditional`
- `handsOffTo`：`autoplan-lite`、`coding`、`qa-browser-check`

### `investigate-root-cause`
- `role`：根因诊断
- `phase`：`incident` / `pre-build`
- `defaultMode`：`conditional`
- `handsOffTo`：`guard-mode`、`coding`、`review-gate`

## 设计规则

- 在真实使用证明有必要之前，优先保持“轻量文档合约”，不要过早做成正式插件运行时。
- 插件输出要足够结构化，让 `main` 能顺利决定下一步。
- 不要保留那些不会改变路线、范围、判定或沉淀质量的装饰性插件。
- 插件应被视为编排原语，而不是好看的标签。

## 未来演进触发条件

只有在以下情况同时出现时，才考虑构建更强的 runtime / plugin manifest 系统：

- 插件数量已经超出 `SKILL.md + references` 可干净维护的范围
- 缺少机器可读元数据时，路由选择已经开始明显出错
- 多个插件需要共享字段，并且需要被系统强制校验
