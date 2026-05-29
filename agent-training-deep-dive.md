---
name: smart-data-usage
description: 智能用数项目 - 项目路径规范和技术架构
type: project
originSessionId: eddc1338-35ee-49a2-b51d-55be8874a02d
---
# 项目信息

## 当前项目名：智能用数

## 必读：文件存储规范

| 类型 | 路径 | 说明 |
|------|------|------|
| **技术文档** | `/Users/damon.lin/Documents/governor/governor/02-产品思考库/技术文档` | 架构方案、设计文档等 |
| **项目代码** | `/Users/damon.lin/Downloads/smart-workbench-master/project/ChatData` | 具体代码实现 |
| **产品PRD** | `/Users/damon.lin/Documents/governor/governor/02-产品思考库/产品PRD` | 产品需求文档 |
| **产品思考** | `/Users/damon.lin/Documents/governor/governor/02-产品思考库/产品思考文档` | 产品设计思路 |
| **市场调研** | `/Users/damon.lin/Documents/governor/governor/02-产品思考库/市场调研` | 调研资料 |

## 工作流程
1. 每次对话形成方案/设计 → 存入技术文档目录
2. 需要开发实现 → 代码放在 ChatData 目录
3. 产品相关内容 → 对应产品PRD/产品思考文档目录

---

name: agent-training-deep-dive
description: Agent训练范式、LoRA-Orchestrator架构、多租户系统设计详解
type: reference
originSessionId: eddc1338-35ee-49a2-b51d-55be8874a02d
---

# Agent训练范式核心知识

## 关键洞察

### 训练阶段分层
- **预训练**：海量互联网文本，Scaling Law红利减退
- **持续预训练/中训练**：混合数据（视觉、长上下文、工具使用）
- **后训练（核心战场）**：RLHF/RLVR/MARL，场景化数据和合成数据是燃料

### Subagent架构训练（CTDE范式）
- **核心**：集中训练、分散执行
- **路线A**：Orchestrator可训练 + Subagent冻结（KIMI PARL，适合超大规模）
- **路线B**：Orchestrator + Subagent联合训练（WideSeek-R1 MARL，适合中等规模）
- **关键难题**：信用分配、串行崩溃

### LoRA-Orchestrator
- **本质**：可插拔的轻量参数模块（LoRA/Adapter）+ 代码运行时
- **插拔实现**：基础模型冻结，LoRA参数物理隔离存储为独立文件
- **训练方式**：SFT学基本编排能力 → RL对齐系统级目标
- **Masked Supervision**：只计算Orchestrator决策的Loss，屏蔽环境Observation和无关动作

### 多租户架构
- **底座共享**：Base LLM统一部署在共享推理服务，底座28GB只加载一份
- **LoRA隔离**：各项目LoRA存储在共享模型仓库（S3/MinIO），按需动态挂载
- **项目工程化**：纯API调用，通过Header（X-Project-ID）路由，不需要加载任何模型

### 自进化闭环
```
线上执行 → 轨迹采集 → 自动标注/PRM评分 → 增量训练新LoRA → 影子测试 → 灰度替换
```

## 必须收集的通用轨迹字段
- 请求上下文：user_query, system_prompt, tool_schema, env_state
- 模型决策：model_name, model_output, reasoning
- 执行轨迹：step_index, action, action_input, observation
- 结果反馈：task_success, execution_time, token_usage, reward

## 奖励设计
- 通用层：task_success + 效率 + 成本
- 项目层：business_metric（各项目自定义）

## Prompt约束与LoRA冲突处理
分层架构：代码硬约束 > LoRA策略 > 后处理转换
- 安全红线：代码层硬拦截
- 调度偏好：训练时注入约束
- 格式要求：后处理统一转换

## LoRA vs 小模型全量微调
- **LoRA**：站在巨人肩膀上微调，借力大模型能力，只学调度策略，几MB，8-16GB显存可训
- **小模型训练**：自己成为巨人，独立模型，几GB，需要40GB+显存，从头/全量微调

## 第三方API无法使用本地训练的LoRA
- LoRA绑定特定底座模型，数学结构与训练底座深度绑定
- 第三方API不暴露权重，不支持外部LoRA挂载
- 替代方案：开源底座本地部署 + LoRA，或本地小模型Orchestrator + 第三方API执行