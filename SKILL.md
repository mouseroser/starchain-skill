---
name: starchain
description: 星链（StarChain）多 agent 开发流水线 v3.0。适用于“用星链实现 XXX”这类中高复杂度开发/修复任务。v3.0 新增 织梭 agent（weaver），实现段落（coding → review → 修复循环 → test → docs）完全下放织梭自治，main 只在分级/Constitution-First/收尾三点介入，支持 Lite 快车道、Full 主链、质量门和交付后短复盘。
---

# 星链 v3.0

把这项 skill 视为 **多 agent 开发编排 skill**，不是脚本启动器。

## 必读

先读：
1. `references/pipeline-v2-9-contract.md` — 当前正式执行合约
2. `references/PIPELINE_FLOWCHART_V2_9_EMOJI.md` — 当前流程图

按需再读：
- `references/pipeline-v2-8-contract.md` — 上一正式版本，供比对/回滚参考
- `references/PIPELINE_FLOWCHART_V2_8_EMOJI.md` — 旧版流程图，仅作比对参考
- `references/pipeline-v2-7-contract.md` — 更旧版本，仅回看参考

## 当前边界

- 当前正式版本：**v3.0**
- 上一版本：**v2.9**
- 执行模式：**main 在主会话逐步 spawn 各 agent**
- 默认入口：用户说 **“用星链实现 XXX”**
- 适用任务：中高复杂度开发、修复、跨模块改动、架构决策、需要多 agent 交叉审查的实现任务
- 新增能力：**Lite 快车道 + 前置产品打磨层 + 更硬的质量门 + 交付后短复盘层**
- 这些新增能力是**吸收进 v2.8 主骨架的插层能力**，不是平行新流水线
- 官方插件层：`founder-office-hours`、`autoplan-lite`、`review-gate`、`qa-browser-check`、`release-retro` 视为星链官方插件集；现已收编到 `starchain/plugins/`

## 触发说明

在这些场景触发：
- “用星链实现 XXX”
- “星链开发 / 修复 XXX”
- 需要先做产品打磨，再决定是否进入实现
- 需要中等复杂度任务的轻量规划链
- 需要 Constitution-First 打磨层
- 需要 Spec-Kit 落地后再开发
- 需要 coding / review / test / docs 多 agent 接力
- 需要高风险实现的质量门 / 浏览器 QA / 仲裁 / 交付后复盘

不适用：
- 单行小修、单文件低风险改动
- 纯读代码 / 纯读文档
- 与开发实现无关的普通问答
- 已经明确是 L1 且无需编排的请求

## 路由选择

### L1
- main 直接处理，或单 agent 处理
- 不进入星链

### L2
- 默认优先走 **StarChain Lite**
- 如发现跨模块架构、高风险、深度研究依赖、方案不收敛，再升级到 **StarChain Full**

### L3
- 默认直接走 **StarChain Full**
- `founder-office-hours` 默认开启

## 核心路径（在 v2.8 骨架上吸收新增能力）

## 路径 A — 星链 Lite

适用于：
- L2
- 中等复杂度功能
- 需要规划但不值得 完整主链
- 需要先收范围，再决定是否开发

默认顺序：
1. **步骤 1** — main 分级
2. **步骤 1A** — `founder-office-hours`（按需；方向不清 / 范围过大时优先）
3. **步骤 1B** — `autoplan-lite`
4. **步骤 2** — `coding`
5. **步骤 3** — `review-gate`
6. **步骤 4** — `test` 或 `qa-browser-check`（UI / 工作流任务优先插入浏览器 QA）
7. **步骤 5** — `docs`（按需）
8. **步骤 6** — main 汇总交付
9. **步骤 7** — main 可靠通知
10. **步骤 8** — `release-retro`（按需）

## 路径 B — 星链 Full

适用于：
- L3
- 跨模块 / 高风险 / 高不确定性
- 需要 Constitution-First 深打磨
- 需要 Spec-Kit 四件套
- 需要多 agent 深协作

默认顺序：
1. **步骤 1** — main 分级
2. **步骤 1A** — `founder-office-hours`（L3 默认）
3. **步骤 1.5** — Constitution-First 打磨层
4. **步骤 1.5G** — Spec-Kit 落地
5. **步骤 2** — `coding`
6. **步骤 3** — `review-gate`
7. **步骤 4** — `test` / `qa-browser-check`
8. **步骤 5** — `docs`
9. **步骤 6** — main 汇总交付
10. **步骤 7** — main 可靠通知
11. **步骤 8** — `release-retro`（按需）

## 步骤定义

### 步骤 1 — main 分级
main 自己完成：
- 任务分级：L1 / L2 / L3
- 类型分析：A 类 / B 类
- 是否走 Lite 或 Full
- 是否需要前置产品打磨层

### 步骤 1A — 产品打磨层
由 `founder-office-hours` 执行，目标是先回答：
- 值不值得做
- 最小切入点是什么
- 哪些范围应该先砍
- 构建 / 缩小后构建 / 延后 / 拒绝

默认触发：
- L3 默认开启
- L2 在方向不清、范围明显过大、存在“可能做错问题”风险时开启

### 步骤 1B — 轻量规划层
由 `autoplan-lite` 执行，目标是形成轻量可执行计划：
- 目标
- 范围内 / 范围外
- 关键假设
- 主要风险
- 执行路径
- 有顺序的任务清单
- 开放决策

默认触发：
- L2 默认开启
- Full 路径可跳过，或作为预判层使用

### 步骤 1.5 — Constitution-First 打磨层
默认顺序：
1. `gemini` — 扫描歧义 / 风险 / 边界
2. `notebooklm` — 提供历史经验 / 模板 / 常见坑点
3. `openai` — 定宪法（约束，不写方案）
4. `claude` — 出实施计划（主方案）
5. `gemini` — 一致性复核
6. `openai` / `claude` — 按需仲裁

### 步骤 1.5G — Spec-Kit 落地
由 `brainstorming` 产出：
- `spec.md`
- `plan.md`
- `research.md`
- `tasks.md`
- `analyze` 一致性检查

### 步骤 2 — 开发
- `coding` — 开发执行

### 步骤 3 — 质量门
默认用 `review-gate` 代替松散审查。目标不是给建议，而是做质量门：
- 正确性
- 回归风险
- 完整性缺口
- 必需验证
- 推荐下一步

允许判定：
- 通过
- 通过，但有后续项
- QA 前需先修复
- 阻断

### 步骤 4 — QA / 测试
- 代码 / 逻辑型任务 → `test`
- UI / 工作流 / 浏览器可见任务 → `qa-browser-check`
- 交互结构还不稳的 UI / 流程任务，可在编码前按需插入 `design-review-lite`
- 两者都需要时 → 先 `test` 再 `qa-browser-check`

### 故障 / 受限执行叠加层
按需叠加：
- `guard-mode`：当任务需要目录边界、只读模式、config freeze、危险命令提醒时
- `investigate-root-cause`：当任务本质上是根因调查，不应直接拍脑袋修时

### 步骤 5 — 文档 / 交付准备
- `docs` 按需触发

### 步骤 6 — main 汇总交付
- main 汇总关键结果、风险、下一步动作

### 步骤 7 — main 可靠通知
- main 统一承担监控群 + 晨星 DM 的可靠通知
- 职能群承接步骤级通知，监控群承接完整审计链
- 晨星 DM 仅用于阻断 / 失败需介入、最终结果、L3/长任务阶段摘要
- 如果任务本身就在晨星 DM 中进行，默认直接在当前对话回复，不额外重复主动 DM

### 步骤 8 — 交付后短复盘
由 `release-retro` 按需执行，提炼：
- 哪些做法有效
- 哪些地方受伤
- 重复出现的模式
- 哪些值得记录 / 提升
- 立刻要跟进的事项

## 升级 / 降级规则

### Lite 升级到 Full 的条件
满足任一条就升级：
- 出现跨模块架构问题
- 需要深度研究
- 涉及安全 / 权限 / 数据迁移
- 方案冲突明显
- `autoplan-lite` 无法稳定收敛
- 晨星明确要求 完整主链

### 降级条件
- 单文件修改
- 普通 bug
- 小功能补丁
- 已有成熟路径的重复性实现
- 明显属于 L1

## 角色 / 插件套件

### 核心插件
- **founder-office-hours**：产品打磨、切入点判断、范围收缩、构建 / 缩小后构建 / 延后建议
- **autoplan-lite**：L2 轻量规划、风险收敛、执行路径产出
- **review-gate**：质量门判定

### 条件插件
- **qa-browser-check**：浏览器关键路径 QA
- **release-retro**：交付后短复盘与沉淀
- **guard-mode**：任务局部护栏、目录/配置/危险动作边界约束
- **design-review-lite**：轻量产品/交互/页面流设计审查
- **investigate-root-cause**：结构化根因调查与故障域收敛

### 核心编排角色 / 通道
- **main**：顶层编排、分级、补发通知、最终交付、Lite/Full 路由选择
- **gemini**：扫描、反方 review、一致性检查
- **notebooklm**：历史知识 / 模板 / 经验补料
- **openai**：宪法定稿、冲突仲裁
- **claude**：主计划、主审查、复杂实现路径
- **brainstorming**：Spec-Kit 四件套、方案智囊
- **coding**：开发执行
- **test**：测试执行
- **docs**：文档交付

## 模型 / 风险规则

### 何时用 Full 链路
- 跨模块改动
- 安全 / 权限 / 数据迁移
- 高影响线上修复
- 复杂架构决策
- 外部方案吸收并本地化落地
- `autoplan-lite` 不能稳定收敛的任务

### 何时优先 Lite
- L2 功能规划
- 中等复杂度改造
- 先规划后决定是否开发
- 自动化 / 工作流设计
- 需要可执行方案但不值得 完整研究链 的任务

### 独立性规则
- **Gemini 不做最终仲裁**
- **谁主写，谁尽量不终审**
- Claude 主方案 → 优先 GPT 仲裁
- GPT 主方案 → 优先 Claude 仲裁
- `review-gate` 给出质量门判定，不能被 coding announce 替代

## 硬规则

- **不要**用废弃 launcher 脚本当默认入口
- **不要**把 main 再套成 isolated session 去编排 main 自己
- **不要**让 review 反向编排其他 agent
- **不要**把 agent 自推当成可靠通知
- **不要**让 coding 的 announce 直接充当完成证明
- **不要**把 5 个新增 skill 机械地每步全跑一遍
- **不要**为了保持仪式感让 L1 任务进入星链
- **不要**跳过质量门后直接宣称完成

## Spawn 规则

主模式：
```text
main 在主会话中逐步 sessions_spawn(mode="run") 各 agent
```

重试规则：
1. 第一次失败 → 立即重试
2. 第二次失败 → 10 秒后重试
3. 第三次失败 → 告警 + 阻断

## 通知规则

- 各 agent 默认向自己的职能群自推步骤级通知（best-effort）
- main 不再逐步补发职能群消息
- 监控群只承接终态 / 异常，由 main 统一推送
- 步骤 7 仍由 main 统一推送到监控群；晨星DM仅用于最终结果、需介入失败、L3/长任务摘要
- agent 自推永远是 best-effort；**可靠链路 = main 的监控群 + 晨星 DM**
- main 调 `message(action="send")` 时，单发必须用 `target`，不要用 `targets`
- 同时发多个群时，按单目标串行发送多次；`targets` 只允许给 `action="broadcast"`
- 禁止把多个群 id 塞进一次 `send`，否则可能被当成“无显式目标”并回退到当前会话

## 版本策略

- 只保留：**当前版本 + 上一版本**
- 当前：`v2.9`
- 上一版：`v2.8`
- **新版本 contract / flowchart 必须以上一正式版本为底稿复制演进**，先保留章节骨架与稳定锚点，再做 delta 修改；**禁止脱离上一版从空白重写**，否则容易丢失通知、重试、路径、回滚等硬约束
- 更老版本、旧 flowchart、旧 README / CHANGELOG / launcher 文档应清理，避免 prompt drift

## 何时继续深入阅读

- 需要正式执行约束 → 读 `references/pipeline-v2-9-contract.md`
- 需要看当前流程顺序 → 读 `references/PIPELINE_FLOWCHART_V2_9_EMOJI.md`
- 需要回看旧正式链路 → 读 `references/pipeline-v2-8-contract.md`
- 需要看旧流程顺序 → 读 `references/PIPELINE_FLOWCHART_V2_8_EMOJI.md`
- 需要更老版本回滚/核对 → 读 `references/pipeline-v2-7-contract.md`
