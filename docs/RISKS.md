# 可执行风险表

更新时间：2026-07-20

状态取值：

- `OPEN`：尚未核实或尚无处置方案。
- `IN_PROGRESS`：正在收集证据或执行验证。
- `MITIGATED`：已有可执行降级方案，但风险尚未完全关闭。
- `CLOSED`：已有可复核证据证明风险关闭。

风险只有在“关闭证据”指向仓库文件、测试结果或运行 artifact 时才能标记为 `CLOSED`。聊天记录不能作为唯一关闭证据。

| ID | 风险 | 状态 | 第 3 天前的关闭条件 | 当前证据 | 下一动作 | 降级方案 |
|---|---|---|---|---|---|---|
| R-01 | 候选基础模型代码、权重或许可证不可访问 | OPEN | 记录仓库/权重来源、许可证、版本与 checkpoint 哈希，并完成最小加载 | 暂无 | 调研并选择一个主模型，验证权重下载与加载 | 使用更小的公开冻结模型；仍不可用时先用固定接口 stub 验证数据管线，但不得将 stub 结果作为模型结论 |
| R-02 | 模型 ROI 数量、顺序、TR、窗口或归一化要求不明确 | OPEN | 形成结构化模型输入契约并完成一组合法/非法 shape 检查 | 暂无 | 阅读候选模型官方实现，记录输入字段和约束 | 将首个 MVP 限制在已明确的输入子集；无法确认时停止模型评估分支 |
| R-03 | 首个 fMRIPrep derivative case 不完整或不可复现 | OPEN | 固定 case 路径/来源，生成文件清单并验证预处理 BOLD、confounds 和必要 metadata | 暂无 | 选择一个小型 derivative case 并执行完整性检查 | 构造不含敏感数据的小型合规 fixture，用于接口和 Validator 开发 |
| R-04 | derivative 空间与所选图谱不一致 | OPEN | 固定 derivative space 和 atlas 版本，验证空间、affine、orientation、维度并记录图谱哈希 | 暂无 | 选择与 derivative 相同空间的固定图谱 | 更换匹配空间的图谱；禁止直接混用或静默重采样 |
| R-05 | confounds 列缺失、命名变化或高运动导致有效时间不足 | OPEN | 确定最小 confounds 策略、列别名、FD 阈值和最小有效时间点要求，并用 case 验证 | 暂无 | 检查首个 case 的 confounds TSV 和 JSON | 使用有记录的简化 confounds 策略；有效长度不足时优雅失败 |
| R-06 | CPU/GPU、内存或运行时间不足 | OPEN | 记录硬件摘要，完成最小推理 dry-run 和资源统计 | 暂无 | 检查本机资源并运行候选模型最小输入 | 回退 CPU、减小 batch/window 或使用更小模型；不得静默改变评估协议 |
| R-07 | fMRIPrep 容器、TemplateFlow 或许可证配置不可用 | OPEN | 固定容器版本，证明镜像和必要资源可获得，记录许可证配置方式 | 暂无 | 确认 Docker、fMRIPrep image 和 TemplateFlow 缓存策略 | 第一周只交付 derivative-first 路径，将 raw 路径标记为受控未完成 |
| R-08 | 测试数据存在隐私、授权或再分发限制 | OPEN | 记录数据来源、许可证、允许用途和仓库提交策略 | 暂无 | 审核首个 case 的数据使用条款 | 只提交生成脚本、哈希和下载说明，或使用公开/合成 fixture |
| R-09 | raw BIDS 路径超出第一周时间预算 | MITIGATED | derivative-first MVP 在第 5 天前跑通，raw 路径具有独立计划和资源上限 | `AGENTS.md` 已明确 derivative-first 和阶段顺序 | 第 5 天前不让 raw 路径阻塞 MVP | 将 raw 路径延后至 MVP 后；输入或资源不足时输出可复现诊断 |

## 更新规则

每次风险状态变化时必须：

1. 更新状态、当前证据和下一动作。
2. 在对应的 `docs/daily/YYYY-MM-DD.md` 中记录变化。
3. 若风险关闭，链接到测试、运行目录、哈希清单或决策文档。
4. 若采用降级方案，明确它对科学结论和最终交付的影响。