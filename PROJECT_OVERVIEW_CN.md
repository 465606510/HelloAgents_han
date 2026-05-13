# HelloAgents 项目概览 (高级生产版)

HelloAgents 是一个从零开始构建、原生支持 OpenAI API 协议的高性能多智能体框架。它以“极简、可扩展、生产就绪”为核心设计目标，不仅提供了经典的 Agent 范式，还引入了知识外化（Skills）、子代理编排（Subagents）等前沿工程实践。

## 🌟 核心理念：一切皆工具 (Everything is a Tool)

HelloAgents 彻底简化了智能体架构：**除了核心的 Agent 逻辑，一切能力皆为工具**。
- **记忆 (Memory)**、**知识库 (RAG)**、**计划 (Planning)** 等模块在框架中都被统一抽象为特定的“工具”。
- 这种设计消除了繁琐的抽象层，让开发者能直观地通过“定义工具 -> 注册工具 -> 智能体调用”这一逻辑闭环来构建复杂应用。

## 🏗️ 核心功能模块

### 1. 知识外化系统 (Skills) - 替代传统 RAG
- **按需加载**：领域知识存储在 `skills/` 目录下的 Markdown 文件中，Agent 仅在需要时通过 `SkillTool` 加载，显著节省 Token 并减少无关信息干扰。
- **渐进披露**：支持元数据扫描与完整内容按需读取，兼顾广度与深度。
- **丰富技能库**：内置 PDF 处理、金融分析、视频理解、ASR/TTS、前端设计等多种成熟技能。

### 2. 子代理机制 (Subagent Mechanism)
- **复杂任务拆解**：主 Agent 可通过 `TaskTool` 启动隔离的子代理处理特定子任务。
- **上下文隔离**：子代理拥有独立的对话历史，避免主上下文被细节信息污染。
- **细粒度控制**：支持为子代理配置不同的工具访问权限（如只读模式）和不同的模型。

### 3. 可观测性与稳定性 (Observability & Stability)
- **TraceLogger**：实时记录执行轨迹，生成双格式日志（机器可读的 JSONL 和人类交互式的 HTML）。
- **熔断器 (Circuit Breaker)**：自动监测工具调用状态，防止由于工具连续失败导致的死循环和 Token 浪费。
- **乐观锁文件系统**：`FileTools` 支持文件冲突检测，确保在并发或复杂编辑场景下的数据一致性。

## 📂 项目结构

```text
HelloAgents_han/
├── hello_agents/           # 框架核心源码
│   ├── agents/            # Agent 实现：Simple, ReAct, Reflection, PlanAndSolve
│   ├── core/              # 底层基石：LLM 适配、流式处理、消息协议、配置
│   ├── tools/             # 工具引擎：注册表、熔断器、文件工具、子代理工具
│   ├── context/           # 上下文工程：GSSC 流水线、Token 计数、截断管理
│   └── observability/     # 可观测性：追踪与日志审计
├── docs/                   # 专题技术指南（异步、子代理、可观测性、配置等）
├── examples/               # 丰富的生产级示例代码
├── skills/                 # 领域知识技能库（PDF, Video, ASR, Finance, etc.）
└── tests/                  # 完善的单元测试与集成测试
```

## 🚀 快速开始

### 1. 部署环境
```bash
pip install -e .
cp .env.example .env  # 填入你的 LLM API Key
```

### 2. 编写你的第一个 Agent
```python
from hello_agents import ReActAgent, HelloAgentsLLM, Config

# 开启高级特性
config = Config(subagent_enabled=True, trace_enabled=True)
agent = ReActAgent("助手", HelloAgentsLLM(), config=config)

# 运行并观察 Trace 日志
agent.run("分析 skills 目录并给我一份总结报告。")
```

## 🛠️ 典型应用场景
- **专业文档处理**：利用 PDF 技能和文件工具进行大规模文档解析与生成。
- **复杂任务编排**：利用子代理机制处理需要多步骤、多模型协作的科研或工程任务。
- **AI 助手中台**：利用 FastAPI SSE 服务将 Agent 能力集成到 Java/Vue 现有业务系统中。

---
*更多详细信息，请参阅 `docs/` 目录下的专题文档。*
