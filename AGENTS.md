# 项目名称

BrainAgent-fMRI

---

# 1. 项目目标（Project Purpose）

本项目旨在构建一个面向 fMRI 科研任务的自动化科研智能体工作流。

目标：

给定 BIDS 格式 fMRI 数据、任务描述和冻结的脑基础模型，
Agent 能够自动完成：

1. 数据结构解析
2. 数据质量检查
3. 预处理路径选择
4. 空间与图谱一致性检查
5. ROI时间序列提取
6. 基础模型输入适配
7. 冻结模型推理
8. 表征评估
9. 鲁棒性分析
10. 科研报告生成


本项目重点不是提出新的fMRI模型，
而是验证：

“Agent是否能够自动建立可靠、可复现、可验证的fMRI分析流程。”

## 当前实施阶段与第 1—5 天优先级

当前优先目标是先跑通最小垂直切片，不提前构建完整 Harness。

第 1—5 天按以下顺序推进：

1. 第 1 天：明确任务边界、建立仓库、维护风险清单和每日更新。
2. 第 2 天：完成最小 TaskSpec、1 个 derivative visible case、required outputs 和关键 Validator 列表。
3. 第 3 天：冻结主要库、基础模型、测试数据、图谱和 MVP 架构；确认模型与数据可访问。
4. 第 4 天：使 `make build` 通过，并实现至少 2 个可独立运行和验证的领域 Tool。
5. 第 5 天：通过 `make smoke` 跑通 derivative-first 最小端到端路径，并至少执行 1 个确定性科学 Validator。

第 6—7 天补齐核心 Tools 和领域 Validators，使核心路径可重复运行。

第 8 天开始实现完整 Planner、持久 Memory、自动 Recovery 和进程中断恢复。

第 9 天开始执行 Baseline 与 Harness 对比和故障注入。

在 MVP 完成前，不优先实现多模型支持、复杂鲁棒性实验、自动多策略重规划、完整经验 Memory、前端或复杂可视化。
---

# 2. 项目边界（Scope）

## 本项目包含：

- BIDS-fMRI数据检查
- fMRIPrep derivative检测
- ROI extraction
- foundation model inference
- frozen embedding evaluation
- quality control
- provenance记录


## 本项目不包含：

- 从零实现fMRI preprocessing算法
- 训练大型foundation model
- 微调基础模型
- 宣称新的脑科学机制发现


所有科学结论必须基于已有证据。

## MVP 的 derivative-first 最小路径

首个 visible case 必须优先使用一份有效、可访问的小型 fMRIPrep derivative，按以下顺序形成最小闭环：

1. 检查 BIDS、derivative 文件和必要元数据是否存在。
2. 验证空间、affine、TR、时间点数和图像维度。
3. 读取最小必要 confounds，并记录所使用的列和被剔除时间点。
4. 使用冻结的固定图谱提取 ROI 时间序列。
5. 检查 ROI 时间序列的 shape、标签顺序及 NaN/Inf。
6. 适配一个冻结基础模型要求的脑区数、TR 和窗口长度。
7. 完成一次冻结推理、embedding 提取或允许的冻结评估。
8. 至少运行一个确定性科学 Validator。
9. 生成最小 metrics、provenance 和 report。

MVP 完成后再接入 raw BIDS 路径。raw 路径应调用 fMRIPrep 官方容器或约定的成熟最小流程；不得从零实现配准、运动校正或标准空间转换。

若 derivative 缺失或无效，应记录诊断并转入 raw 路径；若关键 T1w/BOLD 缺失或资源不足，应优雅失败并保留可复现的未完成记录。

## 初始风险清单

以下风险必须在第 3 天前关闭，或记录明确的降级方案：

- 候选基础模型的代码、权重和许可证是否可访问。
- 模型要求的 ROI 数量、ROI 顺序、TR、窗口长度和归一化方式是否明确。
- 首个 visible case 的 fMRIPrep derivative 是否完整。
- derivative 空间是否与所选图谱严格一致。
- confounds 文件及最小必要列是否存在、命名是否兼容。
- 高运动或 censoring 后的有效时间点是否足够。
- CPU、GPU、内存和运行时间是否满足最小推理要求。
- fMRIPrep 容器、TemplateFlow 资源及必要许可证文件是否可用。
- 小型测试数据是否允许使用、缓存和复现。
- raw BIDS 路径是否会超出第一周的时间或资源预算。

风险状态、关闭条件、当前证据、下一动作和降级方案统一维护在 `docs/RISKS.md`，不得只保存在聊天上下文。

每次风险状态变化时，必须同步更新当天的 `docs/daily/YYYY-MM-DD.md`。风险只有在关闭证据指向仓库文件、测试结果或运行 artifact 时才能标记为关闭。
---

# 3. 仓库结构（Repository Structure）


agent/
    Agent核心逻辑

    planner.py
    executor.py
    validator.py
    memory.py
    recovery.py
    loop.py


tools/

    specs/
        工具接口定义

    implementations/
        工具具体实现


validators/

    确定性科学检查


tasks/

    TaskSpec任务定义


cases/

    visible测试案例


runs/

    每次运行产生的记录


docker/

    Docker环境


docs/

    ARCHITECTURE.md
    SCIENTIFIC_ASSUMPTIONS.md
    COLLABORATION_LOG.md


---

# 4. 必须支持命令（Commands）

所有功能必须通过以下命令运行。


## 构建环境

make build


## 单元测试

make test


## 最小流程测试

make smoke


## 完整任务运行

make run TASK=<task.yaml> OUTPUT=<output_dir>


## 重放历史运行

make replay RUN=<run_dir>


## 清理临时文件

make clean



禁止依赖：

- 本地Python环境
- 个人路径
- 未记录缓存

---

# 5. Agent工作流程（Agent Loop）


MVP 阶段允许使用确定性的顺序式 workflow，不要求在第 8 天前完成智能调度、持久 Memory 或自动 Recovery；但必须保留显式配置、可审计日志和确定性 Validator。

第 8 天起，完整 Harness 的所有科研任务必须遵循：


## Step 1 Observe

读取：

- TaskSpec
- 数据路径
- 模型信息
- 当前state
- 历史trace


---

## Step 2 Normalize

将自然语言任务转换为：

task_resolved.yaml


明确：

- 输入
- 输出
- 约束
- 假设
- 风险


---

## Step 3 Plan

Planner生成：

plan.json


每一步必须包含：

- 使用工具
- 输入artifact
- 输出artifact
- Validator


---

## Step 4 Preflight

正式运行前检查：

- 文件是否存在
- 数据格式
- shape
- 空间信息
- 模型要求
- 资源限制


---

## Step 5 Act

执行单个科学动作。


禁止：

run_everything()

类型的大型黑盒函数。


每个Tool必须具有明确科学意义。


---

## Step 6 Verify

执行Validator。


Validator优先级：

确定性检查 > LLM判断


---

## Step 7 Diagnose

失败必须分类：


input_error

tool_error

parameter_error

resource_error

scientific_assumption_error


---

## Step 8 Recover

允许：

1. 参数调整

2. 工具替代

3. artifact回退

4. 局部重新规划

5. 优雅失败


禁止无限重试。


---

## Step 9 Report

生成：

- 实验结果
- 证据
- 局限
- provenance



---

# 6. 科学约束（Scientific Rules）


## 数据

必须记录：

- 数据来源
- 数据版本
- 数据hash


禁止：

- 数据泄漏
- subject/session/run混合泄漏


---

## fMRI空间

必须明确：

- native space
- MNI space
- atlas space


禁止：

不同空间数据直接比较。


---

## 基础模型


模型必须：

- checkpoint固定
- 参数冻结


运行前后检查：

parameter hash


禁止：

隐式fine-tuning。


---

## 结果解释


必须区分：

1. pipeline成功

2. 模型指标提升

3. 科学结论成立


禁止：

仅因为代码运行成功就认为实验成功。


---

# 7. Tool设计规范


每个Tool必须包含：

name

purpose

inputs

outputs

runtime

possible_errors

validators


例如：

inspect_bids_fmri


功能：

检查BIDS结构。


输入：

dataset_path


输出：

dataset_summary.json


Validator：

validate_bids_structure


---

禁止：

创建：

run_full_fmri_pipeline()


这种万能工具。


---

# 8. Validator规范


每个关键artifact必须验证。


必须包含：


数据检查：

- 文件存在
- shape正确
- NaN检测


fMRI检查：

- TR一致
- affine一致
- atlas匹配
- ROI数量一致


模型检查：

- checkpoint hash
- 参数冻结


实验检查：

- 无数据泄漏
- 可重复运行


---

# 9. Memory规范


Memory包含：


## state.json

保存：

- 当前任务状态
- 已完成步骤
- 下一步


## trace.jsonl

记录：

- Tool调用
- 参数
- 错误
- Validator结果


## lessons.yaml

记录：

- 已知失败
- 修复方式


---

# 10. 输出要求（Run Artifact）


每次运行必须产生：


runs/<run_id>/


    task_resolved.yaml

    plan.json

    state.json

    trace.jsonl

    metrics.json

    provenance.json

    report.md

    artifacts/

    validators/

    errors/


---

# 11. Docker要求


环境必须：

- 可重新build
- 依赖固定
- 无个人路径
- 输入只读
- 输出可写


禁止：

依赖：

~/xxx

个人服务器缓存

未声明环境变量


---

# 12. AI协作规则


允许AI：

- 架构设计
- 代码生成
- 测试生成
- Debug
- 文档整理


但是：

所有关键决策必须记录：

docs/COLLABORATION_LOG.md


必须记录：

- AI建议
- 人类判断
- 修改动作
- 验证证据


---

# 13. 每日更新格式

每个工作日必须以可审计证据更新以下内容：

每日进度必须单独保存为 `docs/daily/YYYY-MM-DD.md`，一天一个文件，不得把多个工作日合并在同一文件。文件日期使用项目时区 `Asia/Shanghai`。

## Done

- 已完成的可验证内容。
- 对应 commit、测试结果或 `runs/<run_id>/` 路径。

## Blocked

- 当前阻塞及其可观察证据。
- 已经尝试过的解决方案和结果。

## Next

- 下一工作日的一个最小、可验证目标。

## AI collaboration

- 当日一个具有判断价值的 AI 建议。
- 本人如何接受、修改或拒绝该建议，以及验证证据。

---
# 14. 分阶段完成标准（Definition of Done）

## 第 1 天完成标准

- [ ] README 草稿和 AGENTS.md 已建立。
- [ ] 项目边界、MVP 范围和初始风险清单明确。
- [ ] 下一工作日的最小目标明确。

## 第 2—3 天完成标准

- [ ] 最小 TaskSpec、required outputs 和 success criteria 明确。
- [ ] 1 个 derivative visible case 已选定且可访问。
- [ ] 关键 Validator 列表已确定。
- [ ] 主要库、模型、数据、图谱和 MVP 架构已冻结或有降级方案。
- [ ] Tool 清单、Docker 方案和模型/数据可访问性证据已记录。

## 第 4—5 天 MVP 完成标准

- [ ] `make build` 成功。
- [ ] 至少 2 个 Tool 可独立运行并验证。
- [ ] `make smoke` 跑通 derivative-first 最小端到端路径。
- [ ] 至少 1 个确定性科学 Validator 实际执行。
- [ ] 最小 metrics、provenance、report 和可审计运行记录已生成。

## 第 6—7 天核心流程完成标准

- [ ] 主要领域 Tools 和关键科学 Validators 已补齐。
- [ ] 核心路径可在相同配置下重复运行。
- [ ] 空间、TR、ROI、confounds、运动和模型冻结检查可执行。

## 第 8—10 天 Harness 完成标准

- [ ] Planner、Memory、Recovery 和终止机制可见。
- [ ] 至少一种自动恢复动作可验证。
- [ ] 进程中断后可从持久状态恢复。
- [ ] Baseline 与 Harness 对比已完成。
- [ ] 至少一次故障注入有 trace 和结果。

## 最终完成标准

- [ ] `make build`、`make test`、`make smoke`、`make run` 和 `make replay` 通过。
- [ ] 至少 3 个 visible cases，包括正常、变化和故障场景。
- [ ] 有失败恢复案例和完整 trace。
- [ ] provenance 完整，结果可复现。
- [ ] 报告结论符合证据并明确局限。

---

# 15. 禁止事项（Do Not）

禁止：

- 修改测试数据使结果通过
- 隐藏失败
- 使用未经记录的数据
- 使用个人绝对路径
- 无限retry
- 把LLM输出作为科学事实
- 把代理指标称为真实生物意义