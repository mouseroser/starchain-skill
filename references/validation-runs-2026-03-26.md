# 星链 v2.9 验证运行记录 — 2026-03-26

## 目的

记录 StarChain v2.9 插件套件的首批**真实验证运行**。

这份文件记录的是**真实使用**，不是假想示例。

---

## 运行 1 — 路径 A：L2 功能规划

### 任务
助贷 CRM 系统规划

### 路由
StarChain Lite

### 使用插件
- `founder-office-hours`
- `autoplan-lite`

### 为什么走这条路
这是一个真实的 L2 规划任务：
- 太大，不能用一次性回答糊过去
- 还没重到必须启用 Full Starchain
- 在实现前确实需要先做产品打磨

### 哪些地方实质改变了结果
#### `founder-office-hours`
把问题从“做一个通用 CRM”重塑成更锋利的 切入点：
- 助贷线索流转
- 跟进推进
- 转化可视

这一步很早就砍掉了不必要的平台化 范围。

#### `autoplan-lite`
把重新 framing 后的任务，转成了可直接使用的构建产物：
- 目标
- 范围内 / 范围外
- 分阶段里程碑
- 数据模型
- 页面结构
- API 草案
- coding prompt

### Verdict
Pass

### 关键发现
`founder-office-hours + autoplan-lite` 这条 Lite 路径，对中等复杂度产品 / 系统规划是真正有价值的。

### 评分
- 路由选择质量：5/5
- 输出有用度：5/5
- 减少不必要工作：5/5
- 交接清晰度：5/5

---

## 运行 2 — 路径 B：浏览器可见工作流质量

### 任务
小红书标签故障 / 发布链路修复

### 验证插件
- `review-gate`
- `qa-browser-check`

### 为什么这条任务符合验证条件
这不是普通代码 bug，而是浏览器可见工作流失效：
- 表面上“发布成功”
- 但 `#标签` 被发成了正文文本
- 平台话题选择器并没有被正确驱动

### `review-gate` 判定
**QA 前需先修复**

原因：
- 执行成功不等于业务正确
- 问题出在正确性 / 完整性层，不只是“脚本有没有跑完”
- “能发出去”不够，平台上的最终形态也必须对

### `qa-browser-check` 判定
**Fail on real workflow**

观察到的失败：
- 标签被发成正文文本
- 预期的话题选择行为没有在真实平台流程中发生

### 关键发现
`review-gate` 很适合抓住“看起来做完了，其实业务上没做对”的问题。  
`qa-browser-check` 很适合验证平台可见工作流，这类问题单靠代码 review 很容易漏掉。

### 评分
- `review-gate` 有用度：5/5
- `qa-browser-check` 有用度：5/5
- 正确性改进：5/5

---

## 运行 3 — 路径 C：incident / 根因调查

### 任务
本地 rerank sidecar 健康异常

### 使用插件
- `investigate-root-cause`
- `guard-mode`

### 使用的 护栏配置
本次采用任务局部受限调查：
- 先观察
- 不要盲目大修系统
- 不要改无关 config
- 只检查服务健康、launchd 状态、日志和运行时环境

### 发生了什么
症状：
- `http://127.0.0.1:8765/health` 没有响应
- launchctl job 存在
- 但服务实际上没有在监听

### 确认根因
本地 sidecar 的虚拟环境坏了。

精确机制：
- `.venv/bin/python3` 启动即崩
- 缺失动态库：`libpython3.12.dylib`
- launchd 因此反复启动失败

### 执行的修复
- 删除损坏的 `.venv`
- 重新执行 `uv sync`
- 重启 `com.openclaw.local-rerank-sidecar`
- 再次验证 `/health`

### 结果
Pass

服务恢复正常：
- 模型成功加载
- `/health` 返回 `200`

### 关键发现
`investigate-root-cause` 已被真实诊断场景验证，不是分析表演。  
`guard-mode` 也被证明有效，它阻止了过早的大范围盲修。

### 评分
- `investigate-root-cause` 有用度：5/5
- `guard-mode` 有用度：4.5/5
- 减少盲修：5/5
- 稳定性改进：5/5

---

## 运行 4 — 路径 D：release-retro

### 主题
2026-03-26 gstack → StarChain v2.9 适配与插件化周期

### 使用插件
- `release-retro`

### 复盘范围
本轮复盘覆盖了完整周期：
- gstack 适配判断
- 第一批插件选择与验证
- StarChain v2.9 结构更新
- 硬迁移到 `starchain/plugins/`
- 第二批插件补充
- 验证计划与插件 API 文档
- 仓库清理与 push
- rerank sidecar 排障 / 修复所带来的运行学习

### 哪些做法有效
- 先翻译方法论，再做内化吸收
- 先验证 Lite，再把它硬写进套件
- 当 canonical list 确定后，再硬迁移为官方插件集
- 插件 API 先保持轻量、文档化，不急着 runtime 化

### 哪些地方受伤
- 四插件 / 五插件口径曾短暂不一致
- `.DS_Store` 垃圾文件一度混进早期 push
- 有时进度措辞走在了真实落地状态前面

### 哪些做法应长期保留
- 路由变化后，必须重新评估 canonical set
- 迁移 push 之前必须带一次 dirty-file 清理
- 状态汇报必须区分：已落地 / 已验证 / 已提交 / 已推送

### Verdict
Pass

### 关键发现
`release-retro` 在这类非平凡交付周期后很有价值，尤其是当架构、流程和执行纪律同时发生变化时。

### 评分
- 沉淀价值：4.5/5
- 运营有用度：4.5/5

---

## 首个验证日后的套件总状态

### 已被强验证
- `founder-office-hours`
- `autoplan-lite`
- `review-gate`
- `qa-browser-check`
- `release-retro`
- `investigate-root-cause`
- `guard-mode`

### 新补入专项验证
- `design-review-lite`

### 运行 5 — 补充验证：设计 / IA sanity review

#### 任务
助贷 CRM 系统的页面结构 / 信息组织 / 操作流梳理

#### 使用插件
- `design-review-lite`

#### 为什么这条任务符合验证条件
虽然 CRM 大任务最开始属于 Lite planning，但其中有一个明确的子问题更像产品 / 设计问题，而不是实现问题：
- 页面该怎么分
- 线索列表、详情、跟进、转化看板如何组织
- 哪些信息必须留在第一屏
- 哪些能力不该在 v1 暴露太多

这确实是一个真实的 UI / IA / operator-flow 设计问题。

#### `design-review-lite` 实质改变了什么
它把讨论从“要有哪些功能”改成了“操作者到底怎么走这条路径”。

有价值的评审输出包括：
- 先定义核心操作者任务
- 将主工作流页面与次级 admin / config 页面分离
- 识别出哪些过多 CRM 选项会让 v1 失控
- 确保 list → detail → follow-up → conversion 的推进路径足够短、足够清楚
- 比起大而全 dashboard，更偏向更少页面 + 更清晰状态切换

#### 设计 判定
**Build but simplify first**

原因：
- 方向是对的
- 但第一版要更紧的页面层级和更少的选项扩张
- 对操作者的清晰度比功能宽度更重要

#### 关键发现
`design-review-lite` 很适合作为 pre-build shaping layer：当产品 framing 已经完成，但界面结构仍有变宽、变 CRM 化风险时，它能及时收口。

#### 评分
- 清晰度改进：4.5/5
- 降低 UX / IA 膨胀：4.5/5
- 对实现前 shaping 的价值：4.5/5

---

## 总结结论

StarChain v2.9 的插件化已经不只是文档练习。

在首个真实验证日之后：
- Lite 路径已经被验证
- 质量门路径已经被验证
- incident / 根因调查路径已经被验证
- retro 路径已经被验证

这套插件已经从概念，进入可用于生产风格编排的阶段。
