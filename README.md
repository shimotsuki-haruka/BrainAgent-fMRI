# BrainAgent-fMRI

面向 AgentTask Demo 3 的自动化 fMRI 预处理与冻结基础模型任务评估工作流。

## 当前目标

第一周优先完成 derivative-first 最小垂直路径：

```text
fMRIPrep derivatives
→ 数据与空间检查
→ confounds 处理
→ ROI 时间序列提取
→ 基础模型输入适配
→ 冻结推理
→ 确定性 Validator
→ 最小报告
```

第 1—5 天先完成可执行、可验证的最小系统。完整 Planner、持久 Memory 和自动 Recovery 从第 8 天开始实现。

## 项目范围

包含：

- BIDS-fMRI 和 fMRIPrep derivative 检查。
- 空间、affine、orientation、TR、时间长度和图谱一致性验证。
- confounds、scrubbing/censoring 和高运动情况处理。
- 固定图谱 ROI 时间序列提取。
- 冻结基础模型输入适配、推理和任务评估。
- 确定性科学 Validator、运行记录和 provenance。
- 后续 raw BIDS 最小预处理路径。

不包含：

- 从零实现配准、运动校正或标准空间转换算法。
- 训练或微调基础模型。
- 第一阶段支持多个基础模型或大规模队列。
- 未经证据支持的脑科学或临床结论。
- MVP 阶段的复杂前端、完整 Harness 或多策略自动重规划。

## MVP 执行路径

首个 visible case 使用有效的小型 fMRIPrep derivative：

1. 检查 BIDS、derivative 文件及必要元数据。
2. 验证空间、affine、TR、时间点数和图像维度。
3. 读取最小必要 confounds，记录使用列和剔除时间点。
4. 使用冻结的固定图谱提取 ROI 时间序列。
5. 验证 ROI shape、标签顺序和有限数值。
6. 适配一个冻结基础模型的 ROI、TR 和窗口要求。
7. 完成一次冻结推理或 embedding 提取。
8. 执行关键确定性 Validator。
9. 生成最小 metrics、provenance 和 report。

MVP 跑通后再接入 raw BIDS/fMRIPrep 路径。

## 当前阶段

当前处于第 1 天收尾阶段：任务边界、项目指令、风险管理和每日记录已经建立。

第 2 天的最小目标是完成 TaskSpec 与验收条件：

- 确定一个可访问的 derivative visible case。
- 创建最小 TaskSpec。
- 明确 required outputs。
- 确定关键 Validator 列表。

## 计划命令

```bash
make build
make test
make smoke
make run TASK=<path> OUTPUT=<path>
make replay RUN=<path>
make clean
```

这些命令是最终统一运行合同，当前尚未全部实现，将按 `AGENTS.md` 的阶段计划逐步完成。

## 文档

- `AGENTS.md`：仓库规则、阶段计划、科学约束和完成标准。
- `docs/RISKS.md`：可执行风险表及关闭证据。
- `docs/daily/`：每日一个独立进度文件。

## 当前仓库状态

当前已完成项目规范与第 1 天文档，MVP 代码尚未开始实现。