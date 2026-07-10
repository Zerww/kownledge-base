# LMPC Demo 运行结果

## 1. 训练数据指标（微调前）

### 全局
| 指标 | 值 |
|------|-----|
| 成功率 | 94% |
| 平均对话轮数 | 2.0 |

### 按用户
| 用户 | 成功率 | 平均对话轮数 |
|------|--------|------------|
| user-noise-0.0 | 100% | 2.0 |
| user-noise-0.3 | 100% | 2.2 |
| user-noise-0.6 | 88% | 2.1 |
| user-noise-0.8 | 88% | 1.8 |

### 按数据集划分
| 划分 | 成功率 | 平均对话轮数 |
|------|--------|------------|
| train | 94% | 2.1 |
| test | 93% | 1.6 |

### 按用户和划分

| 用户 | 划分 | 成功率 | 平均对话轮数 |
|------|------|--------|------------|
| user-noise-0.0 | train | 100% | 2.0 |
| user-noise-0.0 | test | 100% | 1.0 |
| user-noise-0.3 | train | 100% | 2.2 |
| user-noise-0.3 | test | 100% | 1.8 |
| user-noise-0.6 | train | 85% | 2.2 |
| user-noise-0.6 | test | 100% | 1.8 |
| user-noise-0.8 | train | 90% | 1.8 |
| user-noise-0.8 | test | 75% | 1.3 |

## 2. 微调数据准备

| 数据集 | 样本数 |
|--------|--------|
| Train | 85 |
| Test | 15 |

## 3. 模型微调

| 字段 | 值 |
|------|-----|
| 任务 ID | `ft-202607082033-28df` |
| 基础模型 | `qwen3.6-flash-2026-04-16` |
| 超参数 | n_epochs=3, batch_size=64, max_length=8192, lr=1.6e-5 |
| 耗时 | 约 61 分钟（20:33 → 21:35） |
| 最终状态 | **SUCCEEDED** ✅ |
| 微调后模型名 | `qwen3.6-flash-2026-04-16-lmpcdemo-ft-202607082033-28df` |

### 轮询过程
- 共轮询 123 次（间隔 30 秒）
- 前 122 次状态为 `RUNNING`
- 第 123 次（21:35:15）状态变为 `SUCCEEDED`

## 4. 微调模型评估

**状态：❌ 失败**

### 错误信息
```
openai.NotFoundError: Error code: 404
The model `qwen3.6-flash-2026-04-16-lmpcdemo-ft-202607082033-28df` does not exist or
you do not have access to it.
```

### 原因
微调后的模型需先**部署**（在百炼控制台开通模型单元服务）才能用于推理。当前账号未开通 `sfm_modelunits_public_cn` 服务，因此模型无法在 OpenAI 兼容接口中被调用。

### 后续步骤
1. 前往阿里云百炼控制台开通模型单元（MU）后付费服务
2. 通过 `POST /api/v1/deployments` 创建部署
3. 等待部署就绪后，用返回的 `deployed_model` 名替换模型名重新运行 Step 
