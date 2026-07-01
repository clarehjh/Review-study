# 本地 Claude Skills 来源汇总

> 更新时间：2026-07-01 | 共 46 个技能

---

## 一、Matt Pocock 工程技能套件（29 个）

> **来源**：[github.com/mattpocock/skills](https://github.com/mattpocock/skills)

| 分类 | 命令 | 说明 |
|------|------|------|
| 🔧 配置 | `/setup-matt-pocock-skills` | 工程技能配置入口——设置 issue 跟踪器、分类标签和领域文档布局 |
| 📋 项目管理 | `/to-issues` | 将计划/PRD 分解为可独立认领的 issue（追踪弹垂直切片） |
| 📋 项目管理 | `/to-prd` | 将对话综合为 PRD 并发布到 issue 跟踪器 |
| 📋 项目管理 | `/triage` | issue 分类状态机——分类、验证、审视、编写代理就绪的简要说明 |
| 📋 项目管理 | `/qa` | 交互式 QA 会话，对话方式报告 bug 并提交 GitHub issue |
| 📋 项目管理 | `/request-refactor-plan` | 通过用户访谈创建详细重构计划，提交 GitHub issue |
| 📋 项目管理 | `/decision-mapping` | 将松散想法转化为有序调查工单序列，逐一推动至解决 |
| 📋 项目管理 | `/handoff` | 将当前对话压缩为交接文档，供另一个代理接续 |
| 🏗️ 设计与架构 | `/codebase-design` | 设计深层模块的共享词汇、改进接口、决定接缝位置 |
| 🏗️ 设计与架构 | `/design-an-interface` | 并行子代理生成多种截然不同的接口设计方案 |
| 🏗️ 设计与架构 | `/domain-modeling` | 构建和完善项目的领域模型 |
| 🏗️ 设计与架构 | `/ubiquitous-language` | 提取 DDD 风格的统一语言词汇表，标记歧义并提出规范术语 |
| 🏗️ 设计与架构 | `/improve-codebase-architecture` | 扫描代码库寻找深化机会，以可视化 HTML 报告展示 |
| 🔍 审视与审查 | `/grilling` | 无情地就计划/设计进行面试，构建前压力测试 |
| 🔍 审视与审查 | `/grill-me` | 通过无情面试磨砺计划或设计 |
| 🔍 审视与审查 | `/grill-with-docs` | 审视计划的同时创建 ADR 架构决策记录和术语表 |
| 🔍 审视与审查 | `/review` | 双维度代码审查：编码标准 + 规格匹配度 |
| 🔍 审视与审查 | `/loop-me` | 在工空间内对工作流规格进行循环审视面试 |
| 💻 开发实现 | `/implement` | 根据 PRD 或 issue 实现工作 |
| 💻 开发实现 | `/tdd` | 测试驱动开发——红-绿-重构循环 |
| 💻 开发实现 | `/prototype` | 构建一次性原型以回答设计问题 |
| 💻 开发实现 | `/diagnosing-bugs` | 针对疑难 bug 和性能回归的诊断循环 |
| 💻 开发实现 | `/resolving-merge-conflicts` | 解决 git merge/rebase 冲突 |
| 💻 开发实现 | `/setup-pre-commit` | 设置 Husky 预提交钩子（lint-staged + 类型检查 + 测试） |
| 🛠️ 辅助 | `/ask-matt` | 技能路由器——询问哪个技能适合当前情况 |
| 🛠️ 辅助 | `/scaffold-exercises` | 创建练习目录结构（章节、问题、解答、说明） |
| 🛠️ 辅助 | `/teach` | 在工空间内教授新技能或概念 |
| 🛠️ 辅助 | `/wizard` | 生成交互式 bash 向导，引导手动流程（第三方设置、迁移等） |
| 🛠️ 辅助 | `/writing-great-skills` | 编写和编辑技能的参考资料——让技能可预测的词汇和原则 |

---

## 二、Understand-Anything 代码理解系列（8 个）

> **来源**：[github.com/Lum1104/Understand-Anything](https://github.com/Lum1104/Understand-Anything)

| 命令 | 说明 |
|------|------|
| `/understand` | 分析代码库生成交互式知识图谱，理解架构、组件和关系 |
| `/understand-chat` | 使用知识图谱对代码库进行问答 |
| `/understand-dashboard` | 启动交互式 Web 仪表板，可视化代码库知识图谱 |
| `/understand-diff` | 分析 git diff/PR，了解变更内容、受影响组件和风险 |
| `/understand-domain` | 提取业务领域知识，生成交互式领域流程图 |
| `/understand-explain` | 对特定文件、函数或模块进行深度解释 |
| `/understand-knowledge` | 分析 Karpathy 模式的 LLM wiki 知识库，生成交互式知识图谱 |
| `/understand-onboard` | 为新团队成员生成入职指南 |

---

## 三、Anthropic 官方内置技能（6 个）

> **来源**：Claude Code 产品内置（Apache-2.0 许可证）

| 命令 | 说明 |
|------|------|
| `/canvas-design` | 创建设计哲学驱动的精美视觉艺术作品（PNG/PDF） |
| `/claude-api` | Claude API 与 Anthropic SDK 完整参考（模型、定价、streaming、tool use、MCP 等） |
| `/frontend-design` | 为新 UI 或重塑现有 UI 提供独特、有目的性的视觉设计指导 |
| `/mcp-builder` | 创建高质量 MCP 服务器的指南（Python FastMCP / Node TypeScript） |
| `/skill-creator` | 创建、修改、优化和评估技能的全生命周期管理工具 |
| `/webapp-testing` | 基于 Playwright 的 Web 应用交互测试工具包 |

---

## 四、独立技能（3 个）

| 命令 | 来源 | 说明 |
|------|------|------|
| `/git-guardrails-claude-code` | [GitHub](https://github.com/AnandChowdhary/git-guardrails-claude-code)（待确认） | 阻止危险 git 命令（push --force、reset --hard、clean -fd 等） |
| `/obsidian-vault` | [GitHub](https://github.com/AnandChowdhary/obsidian-vault-claude-skill)（待确认） | 在 Obsidian 知识库中搜索、创建和管理笔记，支持 wikilinks |
| `/frontend-weekly-report` | **自定义本地技能** | 根据 Git 提交记录和用户口述生成前端工作周报 |

---

## 📊 统计

| 来源 | 数量 |
|------|------|
| Matt Pocock 工程套件 | 29 |
| Understand-Anything 系列 | 8 |
| Anthropic 官方内置 | 6 |
| 独立社区/自定义 | 3 |
| **总计** | **46** |

---

## 📎 其他相关项目

| 项目 | 来源 | 说明 |
|------|------|------|
| CodeGraph | [github.com/colbymchenry/codegraph](https://github.com/colbymchenry/codegraph) | 代码库 SQLite 知识图谱 MCP 服务器 |
| AgentMemory | [github.com/rohitg00/agentmemory](https://github.com/rohitg00/agentmemory) | AI 代理记忆管理 |
| AI Engineering From Scratch | [github.com/rohitg00/ai-engineering-from-scratch](https://github.com/rohitg00/ai-engineering-from-scratch) | 从零开始的 AI 工程实践 |