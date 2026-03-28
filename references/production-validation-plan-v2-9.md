# 星链 v2.9 生产验证计划

## 目标

验证 StarChain v2.9 插件套件在**真实、接近生产的使用场景**里是否站得住，而不只是打包通过或文档看起来合理。

当前验证对象：
- `founder-office-hours`
- `autoplan-lite`
- `review-gate`
- `qa-browser-check`
- `release-retro`
- `guard-mode`
- `design-review-lite`
- `investigate-root-cause`

## 核心验证问题

1. 正确的插件会不会在正确的任务重量级上被触发？
2. Lite 路径是否真的减少了 L2 任务的过度编排？
3. `review-gate` 的 判定 是否提升了交接质量？
4. `qa-browser-check` 是否能抓住代码 / 测试 review 漏掉的浏览器可见问题？
5. `release-retro` 是否真的能产出可复用改进，而不是仪式感？
6. 第二批插件是否解决了真实重复痛点，而不是“纸面优雅”？

## 验证矩阵

### 路径 A — L2 功能规划
使用一条真实的中等复杂度任务。

预期路径：
- `founder-office-hours`（可选）
- `autoplan-lite`
- `coding`（如果随后真的进入实现）

成功指标：
- 范围 被实质性缩小或澄清
- 有顺序的执行计划可以直接使用
- 除非真的出现高不确定性，否则不需要升级到 Full

### 路径 B — UI / workflow 改动
使用一条真实、浏览器可见的任务。

预期路径：
- `autoplan-lite` 或 Full
- `coding`
- `review-gate`
- `qa-browser-check`

成功指标：
- `review-gate` 给出清晰的 go / no-go 判定
- `qa-browser-check` 能验证主用户路径，并且要么抓出至少一个有意义问题，要么清楚证明可用

### 路径 C — 重复 bug / incident
使用一条真实故障或回归问题。

预期路径：
- `investigate-root-cause`
- `guard-mode`（如需限制范围 / 风险）
- `coding`
- `review-gate`

成功指标：
- 调查在动手改之前就先缩小故障域
- 修复作用于失效机制，而不是表面症状
- 盲修次数减少

### 路径 D — 完成后的交付复盘
使用一条真实 L2 / L3 交付在完成后的案例。

预期路径：
- `release-retro`

成功指标：
- 复盘至少产出一条可复用 memory、learning、checklist 更新或插件改进候选
- 复盘简短、运营化，而不是仪式化

## 评分标准

每次运行从 1-5 打分：
- 路由选择质量
- 插件输出有用度
- 是否减少了不必要工作
- 交接清晰度
- 正确性 / 稳定性改进
- 长期沉淀价值

## 最低通过门槛

只有在以下条件满足时，才算 v2.9 的插件化被生产验证：
- 至少 3 次真实运行，覆盖 2 种以上任务类型
- 至少 1 次浏览器可见任务验证 `qa-browser-check`
- 至少 1 次诊断任务验证 `investigate-root-cause` 或 `guard-mode`
- 至少 1 次 retro 真正产出长期可复用改进

## 每轮需要记录的数据

每次运行后记录：
- 任务类型
- 选择的路径
- 实际使用的插件
- 是否发生升级
- 哪个插件对结果产生了实质变化
- 下次应该收紧什么

## 下一步决策规则

在前 3-5 次真实运行后：
- 保留那些能实质改变结果的插件
- 降级或归档那些只增加仪式感、不增加杠杆的插件
- 根据真实误触发情况，继续细化路由规则
