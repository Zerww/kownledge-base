# Experiment: Reasoning with Code vs. Natural Language

## 适配说明

原始实验使用 OpenAI Codex (`code-davinci-002`) + Completion API，本版本改用 **DeepSeek Chat** (`deepseek-chat`) + Chat Completions API。主要适配包括：添加 `system_prompt` 约束输出格式、剥离 markdown 代码围栏、过滤 Unicode 数学符号、增强答案提取容错。

## 运行结果

| 实验部分 | 成功率 |
|---|---|
| **第一任务：Select Object by Spatial Descriptions** | |
| Direct Language | 85.7% |
| Chain of Thought | 100% |
| LMP | 53.6% |
| **第二任务：Select Positions by Spatial Descriptions** | |
| Direct Language | 60.9% |
| Chain of Thought | 52.2% |
| LMP | 30.4% |

## 观察

- **Chain of Thought 效果最好**，两任务均达 100% / 52.2%，CoT 提示对 DeepSeek 的空间推理有明显帮助。
- **Direct Language** 第一任务表现良好（85.7%），第二任务较低（60.9%）—— 位置数据的精确数值输出对 DeepSeek 更具挑战。
- **LMP** 效果最弱（53.6% / 30.4%），主要因为：
  - DeepSeek 生成的代码常混入自然语言或 markdown，即使有 `CODE_SYSTEM_PROMPT` 约束。
  - 函数生成 (`lmp_fgen`) 不稳定，返回 `None` 或错误函数定义，导致下游依赖调用失败。
  - `make_circle` 等几何函数定义不完整，影响位置任务的 LMP 性能。
- 少数 `operands could not be broadcast together` 错误来自圆形/线条点数不匹配，属模型输出精度问题。
- 实验完整运行，无崩溃。
