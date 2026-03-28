# 星链 v2.9 插件迁移蓝图

## 目标

把以下长期技能提升为 StarChain 官方插件：
- `founder-office-hours`
- `autoplan-lite`
- `review-gate`
- `qa-browser-check`
- `release-retro`

目标状态是让 StarChain 包结构更清晰：
- 一个核心编排 skill
- 一个 `plugins/` 目录承载长期增强模块
- 明确的插件 owner 与触发语义
- 迁移期内保留临时兼容策略

## 目标结构

```text
starchain/
├── SKILL.md
├── references/
│   ├── pipeline-v2-9-contract.md
│   ├── PIPELINE_FLOWCHART_V2_9_EMOJI.md
│   ├── plugin-migration-v2-9.md
│   └── ...
└── plugins/
    ├── founder-office-hours/
    │   └── SKILL.md
    ├── autoplan-lite/
    │   └── SKILL.md
    ├── review-gate/
    │   └── SKILL.md
    ├── qa-browser-check/
    │   └── SKILL.md
    └── release-retro/
        └── SKILL.md
```

## 插件分级

### 核心插件
这些模块应视为 StarChain 核心插件：
- `founder-office-hours`
- `autoplan-lite`
- `review-gate`

### 条件插件
这些模块应视为路线相关插件：
- `qa-browser-check`
- `release-retro`

## 目标运行语义

### `founder-office-hours`
- 插件类型：前置规划 / 产品打磨
- 默认使用：L3、部分 L2
- 角色：在深规划或实现前，把问题打磨锋利

### `autoplan-lite`
- 插件类型：轻量规划核心
- 默认使用：L2 默认规划路线
- 角色：输出短小可执行计划，并判断 Lite 是否还能收敛，还是要升级到 Full

### `review-gate`
- 插件类型：质量门
- 默认使用：Lite 与 Full 的 coding 之后
- 角色：判断能否进入 QA 或下一阶段

### `qa-browser-check`
- 插件类型：浏览器 QA 门
- 默认使用：仅用于 UI / workflow / browser-visible 任务
- 角色：在 review-gate 之后验证主浏览器路径

### `release-retro`
- 插件类型：交付后复盘
- 默认使用：有学习价值的 L2 / L3 交付
- 角色：沉淀可复用改进与重复模式

## 迁移策略

### 阶段 1 — 文档先行迁移
在物理移动文件之前：
- 在 `starchain/SKILL.md` 中声明这些模块为官方插件
- 在 v2.9 contract 中加入插件层定义
- 先写清楚迁移目标与兼容策略

状态：已完成

### 阶段 2 — 目录迁移
把五个 skill 目录迁到 `starchain/plugins/` 下。

必做动作：
1. 创建 `starchain/plugins/`
2. 将每个插件目录移动进去
3. 确保每个插件 `SKILL.md` 仍可独立阅读
4. 更新所有假设其仍处于顶层路径的引用

### 阶段 3 — 兼容层清理
在迁移期内保留短暂兼容，但不能长期双写。

推荐兼容策略：
- 短期：如果发现触发或发现链路仍依赖旧位置，可保留 stub 或兼容引用
- 中期：当 StarChain 文档与使用方式稳定后，清掉顶层重复入口

理想终态：
- 只保留 StarChain 自己持有的插件目录
- 核心 StarChain skill 明确记录每个插件何时启用

## 需要同步更新的引用

物理迁移后要同步这些文件：
- `starchain/SKILL.md`
- `references/pipeline-v2-9-contract.md`
- `references/PIPELINE_FLOWCHART_V2_9_EMOJI.md`（如果未来加入路径说明）
- 所有仍把插件写成“独立长期技能”的笔记
- plugin-suite 的语言必须覆盖全部五个模块，并明确 `autoplan-lite` 是 Lite 核心插件

## 命名策略

当前优先保持插件目录名不变：
- `founder-office-hours`
- `review-gate`
- `qa-browser-check`
- `release-retro`

原因：
- 避免无意义语义抖动
- 保持已验证命名稳定
- 降低迁移风险

如果未来确实要重命名，应作为单独清理步骤处理。

## 向后兼容选项

### 方案 A — 立即硬迁移
- 直接把目录移到 `starchain/plugins/`
- 立刻删除顶层副本
- 同次变更完成 StarChain 文档更新

优点：
- 终态最干净

缺点：
- 如果还有链路依赖旧位置，短期断裂风险最高

### 方案 B — 迁移 + 临时兼容副本
- 先把规范目录迁到 `starchain/plugins/`
- 如有需要，短期保留旧入口
- 验证稳定后再删兼容副本

优点：
- 更稳妥

缺点：
- 会产生一段短期双源风险

### 建议
若仍存在兼容不确定性，优先 **方案 B**。  
只有在所有引用与触发链都已被看清后，才使用 **方案 A**。

## 验证清单

迁移后要确认：
- StarChain 核心 skill 仍能正常阅读
- 每个插件 skill 仍可单独验证
- StarChain 包整体验证通过
- 文档中插件 owner 清晰
- 不再残留旧的顶层引用
- 清理完成后，不再存在长期双真相源

## 风险

### 风险 1 — 过时引用
有些文档可能仍把这些插件写成独立长期技能。

缓解：
- 迁移后做全局 grep
- 先更新 StarChain 文档中的 owner 语言

### 风险 2 — 双真相源
如果顶层副本和插件目录长期并存，会自然漂移。

缓解：
- 尽快选定唯一 canonical 位置
- 给清理设 deadline

### 风险 3 — 过早过深耦合
如果插件逻辑仍在快速变化，过深目录嵌套可能拖慢迭代。

缓解：
- 保持插件内容自包含
- 保持命名稳定
- 不要过早引入复杂内部 API 合约

## 推荐执行顺序

1. 先完成文档迁移
2. 全局 grep 所有插件名与旧顶层路径引用
3. 根据真实引用扩散程度，在 方案 A / B 中做选择
4. 执行物理迁移
5. 验证包与引用
6. 稳定后移除临时兼容层

## 当前下一步

做一次引用扫描，判断这些插件是否已经可以直接硬迁移，还是仍需要一个短暂兼容阶段。
