# HumanEval Benchmark 运行结果汇总

对应 `code_as_policies/Experiment_ HumanEval Benchmark.py` 的 6 组实验，结果保存在 `./human_eval_results/`。

## 文件清单

每组实验包含原始 `jsonl` 与评测脚本回写的 `*_results.jsonl`（带 `passed` 字段）。

| 文件 | 题数 | 通过 |
|------|------|------|
| `results_hier_greedy.jsonl` | 164 | **0** |
| `results_flat_with_prompt_greedy.jsonl` | 164 | **0** |
| `results_flat_without_prompt_greedy.jsonl` | 164 | **0** |
| `results_hier_samples.jsonl` | 16400（100×164） | **0** |
| `results_flat_with_prompt_samples.jsonl` | — | 未找到对应 `_results.jsonl`（评测未跑完） |
| `results_flat_without_prompt_samples.jsonl` | — | 未找到对应 `_results.jsonl`（评测未跑完） |

> 注：`evaluate_functional_correctness.py` 的 `pass@1/pass@10/pass@100` 汇总只打印到终端，未落盘；上表通过统计 `_results.jsonl` 中 `"passed": true` 得出。

## 结论：全部 0 通过，结果不可用

所有已评测组的通过率均为 **0%**。

查看具体 `completion` 可见，模型（`deepseek-chat`）未按要求输出纯代码，而是返回聊天式回复，例如：

```
Looking at this problem, I need to...
Here's the implementation:
```python
```

随后被 `stop_tokens=['def', ...]` 截断在三引号围栏处，`exec` 失败。典型错误：

- `failed: unterminated string literal` —— 输出 Markdown ```python 代码块而非裸代码，被 stop token 截断；
- `failed: invalid syntax` —— 同上，结构不完整；
- `failed: invalid character '→' / '≥' / '²'` —— 大量 Unicode 字符导致语法错误；
- prompt 示例中的 `get_mean` 等函数未定义，模型在补全里反复"诊断"却未写出实现。

## 原因分析

该 notebook 原本面向论文中使用的 **Codex 类模型**（直接续写函数体），而当前配置的 `deepseek-chat` 走对话格式，不兼容"裸代码续写"设定，因此几乎所有样本都生成了带解释与 Markdown 围栏的回复并被截断。

## 修复建议

- 改用 `deepseek-coder`（代码模型，更适合续写）；
- 移除 prompt 中的示例函数（`get_mean` 等未定义符号）；
- 用 system 指令强制模型**只输出可执行的 Python 代码**、禁止 Markdown 围栏与解释文字；
- 调整 `stop_tokens` 以正确截断函数定义（如 `# end of function`）。
