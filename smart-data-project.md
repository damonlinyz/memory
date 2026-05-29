---
name: smart-data-project
description: 智能用数应用项目 - LobeChat前端 + Gemma Orchestrator + PostgreSQL
type: project
originSessionId: current
---

# 智能用数应用项目

## 项目信息

**代码仓库**：git@github.com:damonlinyz/smartdata.git

**文档目录**：
- 技术文档：`/Users/damon.lin/Documents/governor/governor/02-产品思考库/技术文档/2026-05-11-智能用数应用技术架构方案.md`
- 产品PRD：`/Users/damon.lin/Documents/governor/governor/02-产品思考库/产品PRD/2026-05-11-智能用数产品PRD.md`

## 架构设计

### 核心组件
- **前端**：LobeChat（纯UI，不做AI推理）
- **Orchestrator**：Python + Gemma 31B（意图识别、Plan生成、Skill调度）
- **数据层**：PostgreSQL + pgvector
- **Skill执行**：Python服务

### 用户流程
```
用户提问 → 意图识别 → Plan生成 → 用户确认 → 执行查询 → 结果展示
```

### 意图类型
- Query（查询）：数据查询
- Analysis（分析）：归因分析、维度分析
- Chart（制图）：图表生成

### Plan结构
- 由模型自主判断Step数量
- 每个Step包含Skill + Tool Calls
- 支持多查询合并（如"查询上海销量和渗透率"= 2个查询Step + 1个合并展示）

### 重试策略
- 单个Task累计重试 ≤ 6次
- 单个Tool Call重试 ≤ 3次
- 失败不阻塞其他Task

## 数据库状态

**已安装**：
- PostgreSQL 17（Homebrew）
- pgvector 0.8.2（需要PostgreSQL 17）

**数据库信息**：
- 用户：damon
- 数据库：smartdata
- 连接：psql -U damon -h /tmp -d smartdata

**注意**：.pg0目录下有另一个PostgreSQL 18实例（端口5432），与Homebrew的PostgreSQL 17可能冲突

## 开发清单（MVP阶段）

1. PostgreSQL + pgvector 安装 - 部分完成（DB已建，待验证pgvector）
2. 数据库Schema创建 - 待完成
3. 示例数据生成 - 待完成
4. Orchestrator后端（意图识别、Plan生成、Skill调度）- 待完成
5. LobeChat定制（Plan UI展示）- 待完成

## 当前问题/待决策

### LLM推理方案
- Gemma 31B 需要 GPU，本地无法运行
- 用户考虑方案：
  1. 第三方 API（Gemma API / MiniMax API）
  2. 换 CPU 可跑的模型（LLaMA 3 8B / Qwen 2 7B）
  3. 服务器部署（有/无显卡配置）
- **用户正在思考中**

## 用户偏好记录

- 用户希望后端全部用Python实现
- 用户使用Apple Silicon Mac
- 用户有MiniMax API环境（用于MCP）
- 暂时不需要Docker
- 无可用GPU，考虑服务器部署