# 星链流水线 v3.0 合约

**版本**: v3.0  
**生效日期**: 2026-03-29  
**上一版本**: v2.9（回滚参考）

## 核心变更

### v3.0 主要改进（相比 v2.9）

1. **新增 pipeline agent**：实现段落（Step 2 coding → Step 3 review-gate → Step 4.5 修复循环 → Step 4 test → Step 5 docs）下放给专用 `pipeline` agent 自治执行，main 不再逐步等待每个子步骤。
2. **main 减负**：星链运行期间 main 主会话解放，可并发处理其他任务（自媒体 Step 7 确认、临时请求等）。main 只在三个点介入：启动前（分级 + Constitution-First）、BLOCKED 上报、收尾汇总。
3. **修复循环内化**：R1/R2/R3 修复迭代在 pipeline agent 内部自治，不再打扰 main。只有 R3 仍 NEEDS_FIX 或 REJECT 时才 BLOCKED 上报。
4. **review 独立性保障**：pipeline 通过 sessions_send 与 review 通信，review 仍是独立质量门，不受 pipeline 编排干预。既出题又批卷的利益冲突被彻底消除。
5. **浏览器 QA 正式入链**：涉及 UI / workflow / 浏览器可见路径时，`qa-browser-check` 成为 步骤 4 的正式分支，而不是临时补测。
6. **交付后短复盘入链**：`release-retro` 作为 步骤 8 按需执行，用于沉淀 哪些做法有效 / 哪些地方受伤 / 重复出现的模式。
7. **通知链路收口**：可靠通知 owner 统一为 `main`；职能群承接步骤级通知，监控群承接完整审计链；晨星 DM 只用于最终结果、需介入失败、L3 / 长任务阶段摘要。

### v2.8 主要改进（相比 v2.7）

1. **NotebookLM 提前介入**：从 步骤 1.5S 移到 步骤 1.5B，在制定宪法之前提供历史经验
2. **证据驱动规则**：宪法基于 NotebookLM 的历史教训制定，避免重复踩坑
3. **Brainstorming 动态模型**：根据任务级别和轮次动态选择 sonnet / opus
4. **与星鉴统一**：NotebookLM 深度参与模式与星鉴流水线一致

### 优化收益预期

- **效率提升**: 25-35%（L2 任务可走 Lite 快车道，减少不必要的重链开销）
- **降本**: 30-40%（轻任务不再被完整研究链拖重）
- **成功率**: 质量门前移，显性回归与漏测风险降低
- **质量提升**: 仍保留 v2.8 的证据驱动宪法 + NotebookLM 深度参与
- **用户体验**: 主私聊内只做编排与汇总，不再被脚本模式和重写文档双重漂移拉偏

---

## 架构：main 直接编排模式（Lite + Full）

```text
main（小光）
└── 步骤 1：任务分级 + 类型分析 + 路由选择
    ├── L1：不进入星链；main 直做或单 agent 解决
    ├── L2：优先评估是否适合 Lite 快车道
    │   ├── 步骤 1A：founder-office-hours（按需）
    │   ├── 步骤 1B：autoplan-lite
    │   ├── 步骤 2-5：**pipeline agent**（coding → review-gate → 修复循环 → test → docs）
    │   │   └── pipeline sessions_send 回 main（PASS 或 BLOCKED）
    │   ├── 步骤 6：main 汇总交付
    │   ├── 步骤 7：main 可靠通知
    │   └── 步骤 8：release-retro（按需）
    └── L3：进入 Full 主链
        ├── 步骤 1A：founder-office-hours（默认开启）
        ├── 步骤 1.5：Constitution-First 打磨层
        │   ├── 步骤 1.5A：gemini 扫描
        │   ├── 步骤 1.5B：notebooklm 深度研究（提前介入）
        │   ├── 步骤 1.5C：openai 宪法（基于证据）
        │   ├── 步骤 1.5D：claude 主计划（基于最佳实践）
        │   ├── 步骤 1.5E：gemini 一致性复核
        │   ├── 步骤 1.5F：openai / claude 仲裁（按需）
        │   └── 步骤 1.5G：brainstorming 落地 Spec-Kit
        ├── 步骤 2-5：**pipeline agent**（coding → review-gate → 修复循环 → test → docs）
        │   └── pipeline sessions_send 回 main（PASS 或 BLOCKED）
        ├── 步骤 6：main 汇总交付
        ├── 步骤 7：main 可靠通知
        └── 步骤 8：release-retro（按需）
```

**优势**：
- 保留 v2.8 的主干，不另起平行体系
- 新能力以“插层”形式进入原流水线，而不是重写掉旧骨架
- Full 主链仍保留 NotebookLM 提前介入 + 证据驱动宪法
- Lite 快车道只解决“该轻的轻”，不是替代所有 Constitution-First 任务
- 质量门、浏览器 QA、复盘 被正式吸收入链，职责更清晰

### 产出目录规范

**所有星链流水线产物统一存放于 `intel/collaboration/starchain/`**，不再散落在各 agent 目录。

| 目录 | 内容 |
|------|------|
| `specs/` | 宪法、计划、Spec-Kit、Lite Plan、代码 |
| `reviews/` | 扫描报告、一致性复核、review-gate 结果 |
| `arbitration/` | 仲裁结论 |
| `research/` | NotebookLM 历史经验调研 |
| `test/` | 测试报告 |
| `qa/` | 浏览器 QA 结果 |
| `docs/` | 交付文档 |
| `retro/` | release-retro 复盘沉淀 |

---

## 官方插件层（吸收到星链内，而非另起炉灶）

### `founder-office-hours`
用途：先把“做什么 / 为什么 / 做多大”说清楚，避免直接冲进实现。

### `autoplan-lite`
用途：为 L2 任务提供轻量规划；当任务还不值得拉满完整研究链时，优先使用。

### `review-gate`
用途：coding 后的强制质量门。没有 gate 判定，不得直接宣称完成。

### `qa-browser-check`
用途：UI / workflow / 浏览器可见路径的关键链路 QA。

### `release-retro`
用途：交付后短复盘，沉淀复用经验、错误模式、应提炼的 memory / checklist / skill。

---

## 流程详解

### 步骤 1: 任务分级与类型分析 + 路由选择

**执行者**: main（小光）  
**输入**: 用户需求  
**输出**:
- 级别判定（L1 / L2 / L3）
- 类型判定（Type A / B）
- 路由选择（直做 / Lite / Full）
- 是否需要 浏览器 QA / 仲裁 / 复盘

**分级标准**：
- **L1**（低风险）：单文件修改、普通 bug、小功能补丁
- **L2**（中风险）：跨模块改动、中等复杂度功能、需要规划但不一定值得 完整研究链
- **L3**（高风险）：安全 / 权限 / 数据迁移 / 支付 / 交易 / 复杂架构决策 / 高影响线上修复

**类型判定**：
- **Type A**（业务 / 架构）：业务逻辑、架构设计、API 设计
- **Type B**（算法 / 性能）：算法优化、性能调优、数据结构

**路由规则**：
- **L1**：不进入星链
- **L2**：默认优先评估 Lite；若出现架构 / 安全 / 数据迁移 / 深研究需求 / 规划无法收敛，则升级 Full
- **L3**：默认 Full

---

### 步骤 1A: 产品打磨（按需 / L3 默认）

**执行者**: founder-office-hours  
**输入**: 用户需求 + 当前上下文  
**输出**:
- 核心问题
- 谁受益
- 为什么现在做
- 最小有价值切入点
- 应砍掉的范围
- 关键取舍
- 构建 / 缩小后构建 / 延后 / 拒绝 建议

**保存位置**: `intel/collaboration/starchain/reviews/founder-office-hours-YYYYMMDD_HHMMSS.md`

**使用规则**：
- **L3 默认开启**
- **L2 按需开启**：当方向、边界、价值主张不清晰时再用
- **不要**为了形式感机械开启

---

### 步骤 1B: 轻量规划（L2 默认快车道）

**执行者**: autoplan-lite  
**输入**: 任务描述 + 步骤 1 / 1A 结果  
**输出**:
- 目标
- 范围内 / 范围外
- 关键假设
- 主要风险
- 推荐执行路径
- 有顺序的任务清单
- 开放决策

**保存位置**: `intel/collaboration/starchain/specs/lite-plan-YYYYMMDD_HHMMSS.md`

**升级条件**：
满足任一条，立即从 Lite 升级到 Full：
- 出现跨模块架构问题
- 出现安全 / 权限 / 数据迁移问题
- 需要 NotebookLM 深研究才能定方向
- 方案冲突明显
- `autoplan-lite` 无法稳定收敛
- 晨星明确要求完整主链

---

### 步骤 1.5: Constitution-First 打磨层（Full 主链）

#### 步骤 1.5A: Gemini 扫描

**执行者**: Gemini（织梦）  
**模型**: `gemini/gemini-3.1-pro-preview`  
**Thinking**: medium (L2) / high (L3)  
**输入**: 任务描述  
**输出**:
- 问题清单（需求歧义点）
- 盲点清单（容易忽略的地方）
- 待验证假设（依赖的前提条件）

**保存位置**: `intel/collaboration/starchain/reviews/scan-YYYYMMDD_HHMMSS.md`

#### 步骤 1.5B: NotebookLM 深度研究（提前介入）

**执行者**: NotebookLM（珊瑚）  
**模型**: `anthropic/claude-opus-4-6`（内部调用 NotebookLM）  
**Thinking**: high  
**输入**: Gemini 扫描结果  
**查询**: starchain-knowledge notebook  
**输出**: 实施建议文档
- 历史经验
- 常见坑点（作为宪法禁止项证据）
- 推荐路径（作为范围界定依据）
- 参考实现
- 宪法建议（基于历史教训的约束建议）

**保存位置**: `intel/collaboration/starchain/research/implementation-advice-YYYYMMDD_HHMMSS.md`

#### 步骤 1.5C: OpenAI 宪法（基于证据）

**执行者**: OpenAI（GPT）  
**模型**: `openai/gpt-5.4`  
**Thinking**: high  
**输入**: Gemini 扫描 + NotebookLM 研究  
**输出**: 开发宪法（1-2 页核心约束）
- 范围定义
- 判定标准
- 禁止项
- 证据门槛
- 输出格式

**保存位置**: `intel/collaboration/starchain/specs/constitution-YYYYMMDD_HHMMSS.md`

#### 步骤 1.5D: Claude 主计划（基于最佳实践）

**执行者**: Claude（小克）  
**模型**: `anthropic/claude-opus-4-6`  
**Thinking**: medium (L2) / high (L3)  
**输入**: 宪法 + NotebookLM 研究  
**输出**: 实施计划
- 技术路径
- 实施步骤
- 风险控制
- 验证方式

**保存位置**: `intel/collaboration/starchain/specs/plan-YYYYMMDD_HHMMSS.md`

#### 步骤 1.5E: Gemini 一致性复核

**执行者**: Gemini（织梦）  
**模型**: `gemini/gemini-3.1-pro-preview`  
**Thinking**: medium  
**输入**: 宪法 + 计划  
**输出**: 一致 / 漂移 / 严重漂移

**保存位置**: `intel/collaboration/starchain/reviews/consistency-YYYYMMDD_HHMMSS.md`

#### 步骤 1.5F: 仲裁（按需）

**触发条件**：
- Gemini 判定 `严重漂移`
- L3 级任务需要独立仲裁
- 宪法与计划有明显冲突

**执行者**: OpenAI 或 Claude（按独立性规则）  
**输出**: 通过 / 修订 / 阻断

**保存位置**: `intel/collaboration/starchain/arbitration/decision-YYYYMMDD_HHMMSS.md`

#### 步骤 1.5G: Brainstorming 落地 Spec-Kit（动态模型）

**执行者**: Brainstorming  
**模型**: 动态选择
- **L2**: `anthropic/claude-sonnet-4-6`
- **L3**: `anthropic/claude-opus-4-6`

**输入**: 宪法 + 计划 + NotebookLM 研究  
**输出**: Spec-Kit 四件套
- `spec.md`
- `plan.md`
- `tasks.md`
- `research.md`

**保存位置**: `intel/collaboration/starchain/specs/spec-kit-YYYYMMDD_HHMMSS/`

---

### 步骤 2: 开发

**执行者**: Coding  
**模型**: Type A: `anthropic/claude-sonnet-4-6` / Type B: `openai/gpt-5.4`  
**Thinking**: medium  
**输入**:
- Lite 路由：lite plan
- Full 路由：Spec-Kit

**输出**: 代码 + 必要的局部验证结果

**保存位置**: `intel/collaboration/starchain/specs/coding-YYYYMMDD_HHMMSS/`

---

### 步骤 3: 质量门（强制质量门）

**执行者**: review-gate  
**输入**: 代码 + Lite 计划 / Spec-Kit + 必要上下文  
**输出**:
- 判定
- 正确性问题
- 回归风险
- 完整性缺口
- 必需验证
- 推荐下一步

**允许判定**：
- `通过`
- `通过，但有后续项`
- `QA 前需先修复`
- `阻断`

**保存位置**: `intel/collaboration/starchain/reviews/review-gate-YYYYMMDD_HHMMSS.md`

**硬规则**：
- 没有 `review-gate` 判定，不得进入完成态
- coding announce 不能替代 review-gate 判定
- 若判定 = `QA 前需先修复`，回到 步骤 2 修复
- 若判定 = `阻断`，进入告警 / 交付阻塞态

---

### 步骤 4: Test / 浏览器 QA（按任务类型）

#### 测试分支

**执行者**: Test  
**模型**: Type A: `openai/gpt-5.4` / Type B: `anthropic/claude-sonnet-4-6`  
**Thinking**: medium  
**适用**: 代码 / 逻辑 / 回归风险重  
**输出**: 测试报告

**保存位置**: `intel/collaboration/starchain/test/test-YYYYMMDD_HHMMSS/`

#### 浏览器 QA 分支

**执行者**: qa-browser-check  
**适用**: UI / workflow / 浏览器可见关键路径  
**输出**:
- 路径是否可走通
- 关键交互是否成立
- 页面级回归风险
- 是否需要返回 步骤 2 修复

**保存位置**: `intel/collaboration/starchain/qa/qa-browser-YYYYMMDD_HHMMSS.md`

**选择逻辑**：
- code / logic heavy → 走 Test
- UI / workflow / browser-visible → 走 浏览器 QA
- 两者都有 → 先 Test，再 浏览器 QA

**失败处理**：
- 单次问题 → 返回 步骤 2 修复
- 连续失败 / 结论分歧明显 → 可按需拉起 Gemini / Claude / Brainstorming 做诊断，但这是 步骤 4 内的失败分支，不另起平行主链

---

### 步骤 5: Docs 文档生成

**执行者**: Docs  
**模型**: `minimax/MiniMax-M2.7`  
**Thinking**: medium  
**输入**: 代码 + Lite 计划 / Spec-Kit + review-gate + test / QA 结果  
**输出**: 交付文档 / 发布说明 / 使用说明

**保存位置**: `intel/collaboration/starchain/docs/deliverable-YYYYMMDD_HHMMSS/`

---

### 步骤 6: main 汇总交付

**执行者**: main（小光）  
**输入**: 所有 agent 产物  
**输出**:
- 汇总报告
- 核心结果
- 风险 / 后续项
- 下一步动作

**说明**：
- 步骤 6 是“汇总与交付内容成形”
- 步骤 7 是“可靠通知与对外可见交付”

---

### 步骤 7: main 可靠通知

**执行者**: main（小光）  
**输出**:
- 向相关职能群发送步骤级完成 / 失败摘要
- 向监控群发送完整主链审计结果
- 按条件通知晨星 DM（仅最终结果 / BLOCKED / L3 或长任务阶段摘要）

**硬规则**：
- 如果任务本身就在晨星 DM 中进行，默认直接在当前对话回复，不额外重复主动 DM
- 不能把 agent 自推是否成功当作可靠前提
- 可靠通知 owner 永远是 `main`

---

### 步骤 8: Release 复盘（按需）

**执行者**: release-retro  
**触发条件**：
- L2 / L3 有明显复杂度
- 出现重复失败模式
- 产生新的 checklist / memory / skill candidate
- 任务虽完成但过程痛苦、值得沉淀

**输出**:
- 哪些做法有效
- 哪些地方受伤
- 重复出现的模式
- what to record / promote
- 立刻要跟进的事项

**保存位置**: `intel/collaboration/starchain/retro/release-retro-YYYYMMDD_HHMMSS.md`

---

## 独立性规则（与星鉴统一）

**原则**：谁参与主要决策，谁就不做最终仲裁

- **OpenAI 制定宪法** → Claude 仲裁计划分歧
- **Claude 主计划** → OpenAI 仲裁实施分歧
- **Gemini 始终不做最终仲裁**，只做扫描 / 复核 / 反方意见
- `review-gate` 给 gate 判定，不能被 coding announce 替代

---

## 通知规范

### 核心原则

sub-agent 只返回结果给 main，**不自己推群**。所有群通知由 main 统一发出。

### 根因

`sessions_spawn` 创建的 isolated session 默认没有稳定的 `message` 能力；即使表面可用，也不应把 sub-agent 自推当作可靠送达。

### main 发消息硬规则

- `message(action="send")` 的单发目标必须使用 `target`（底层会映射为 `to`）
- 需要发多个群 / 多个接收者时，必须按单目标串行发送多次
- `targets` 只允许用于 `action="broadcast"`，不能用于普通 `send`
- 禁止把多个群 id 塞进一次 `send`，否则可能被运行时当作“无显式目标”，回退到当前会话

### 通知类型（三类必须覆盖）

| 类型 | 触发时机 | 发往 |
|------|---------|------|
| **START** | agent 开始执行本步骤时 | main 推送职能群 + 监控群；晨星DM 仅按条件触发 |
| **COMPLETION** | agent 完成本步骤时（含结果摘要） | main 推送职能群 + 监控群；晨星DM 仅按条件触发 |
| **FAILURE** | agent 遇到错误 / 卡点时 | main 推送职能群 + 监控群；晨星DM 在需要介入时必发 |

### 通知内容要求
- START / COMPLETION 必须包含：步骤名称、本步骤做了什么、下一步是什么
- FAILURE 必须包含：步骤名称、错误原因、已尝试的解决措施
- 不得只发 `done`、`开始` 等空内容

### 晨星DM（条件触发）
晨星DM 只用于：
1. 最终结果
2. 需介入的 FAILURE / BLOCKED
3. L3 / 长任务阶段摘要
4. 如果任务本身就在晨星DM中进行，直接在当前对话回复，不额外重复主动 DM

### main 兜底规则
- 负责补发缺失通知
- 负责最终交付通知
- 负责告警通知

---

## 完成判定

完成信号必须同时满足：
- ✅ 正式文件已写入对应路径
- ✅ 返回结构化回执（不能只写 `done`）
- ✅ 关键阶段通知已发出或由 main 兜底补发
- ✅ `review-gate` 已给出 判定，且 Test / QA 已完成所需验证

**⚠️ 缺一项都不算真正完成**

---

## 模型基线

| Step | 默认模型 / 执行者 | Thinking Level | 说明 |
|------|------------------|----------------|------|
| 步骤 1 | `main / opus` | high | 分级、路由、风险判断 |
| 步骤 1A | `founder-office-hours` | medium / high | 产品打磨 |
| 步骤 1B | `autoplan-lite` | medium | 轻量规划 |
| 步骤 1.5A | `gemini` | medium / high | 扫描问题 |
| 步骤 1.5B | `notebooklm / opus` | high | 深度研究（提前介入） |
| 步骤 1.5C | `openai (gpt)` | high | 宪法制定（基于证据） |
| 步骤 1.5D | `claude / opus` | medium / high | 主计划 |
| 步骤 1.5E | `gemini` | medium | 一致性复核 |
| 步骤 1.5F | `openai / claude` | high | 仲裁 |
| 步骤 1.5G | `brainstorming / sonnet(L2) or opus(L3)` | medium / high | Spec-Kit |
| 步骤 2 | `coding / sonnet(A) or gpt(B)` | medium | 开发 |
| 步骤 3 | `review-gate` | medium / high | 质量门 |
| 步骤 4 | `test` / `qa-browser-check` | medium | 测试 / 浏览器 QA |
| 步骤 5 | `docs / minimax` | medium | 文档生成 |
| 步骤 6 | `main / opus` | high | 汇总交付 |
| 步骤 7 | `main / opus` | high | 可靠通知 |
| 步骤 8 | `release-retro` | medium / high | 交付后复盘 |

---

## Spawn 规范

### main → pipeline



### pipeline → main（回传）



### pipeline → review（审查请求）



### pipeline 内部 spawn 规则

主模式：


### Spawn 重试机制

任何 agent spawn 失败时（包括 timeout、503、网络错误等）：
1. **第一次失败** → 立即重试
2. **第二次失败** → 等待 10 秒后重试
3. **第三次仍失败** → 推送告警到监控群 + 按条件通知晨星 → 标记 BLOCKED

---

## 工具容错与降级

外部工具失败时：
1. 发送 Warning 到监控群
2. 跳过失败的工具调用或切换同职责替代路径
3. 继续推进到下一步
4. 绝不因单个工具失败就假装整条流水线完成

**例外**：质量门 / 正式测试 / 正式交付不可被“Warning 跳过”替代。

---

## 版本管理

- **小更新不升版本号**：通知细化、措辞修正、轻量步骤调整、同框架内补丁
- **大变动才升新版本**：流程主干、阶段职责、核心门控、模型分工、回滚逻辑、交付结构发生实质变化
- **只保留最近两个版本**：当前版本 + 上一个版本
- **默认入口始终指向最新版本**
- **新版本 contract / flowchart 必须以上一正式版本为底稿复制演进**，保留章节骨架与稳定锚点后再做 delta 修改；**禁止脱离上一版从空白重写**

---

## 回滚策略

如果 v2.9 出现问题，可以回滚到 v2.8：
1. 恢复 `references/pipeline-v2-8-contract.md` 为默认合约
2. 恢复 `references/PIPELINE_FLOWCHART_V2_8_EMOJI.md` 为默认流程图
3. 暂停 Lite 快车道与新增插件层，只保留 v2.8 主链

---

**合约生效日期**: 2026-03-27  
**合约版本**: v2.9  
**上一版本**: v2.8（回滚参考）
