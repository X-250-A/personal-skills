# personal-skills

personal-skills 是一个个人技能仓库，收集了一套可为 Claude Desktop（Cowork 模式）加载的自定义技能（skill）。每个技能是一个独立的 Markdown 文件，以 YAML 前置元数据（frontmatter）声明技能名称和触发条件，正文为 Claude 提供执行特定任务的完整工作流指引。

本仓库目前包含 6 个技能，覆盖两大场景：

- **中文技术文档撰写** —— 按特定风格撰写 README、知识教程、项目架构文档、毕业论文，以及编写与优化 Prompt 本身
- **通用思维工具** —— 对想法或方案进行系统性压力测试，揭示潜在风险和逻辑漏洞

每个技能自包含，不依赖外部脚本或配置；放在 Cowork 的技能搜索路径下即可被自动识别和加载。

## 前置条件

| 依赖项            | 要求                      | 说明                   |
| -------------- | ----------------------- | -------------------- |
| Claude Desktop | 最新稳定版                   | 需启用 Cowork 模式以支持技能加载 |
| 操作系统           | Windows / macOS / Linux | 无特殊限制                |
| 文本编辑器          | 任意                      | 用于编辑或新增 .skill.md 文件 |

## 快速开始

**1. 克隆仓库**

```bash
git clone git@github.com:X-250-A/personal-skills.git
cd personal-skills
```

**2. 确认技能文件**

```bash
ls 文档撰写/
# 输出：README撰写.skill.md  prompt撰写.skill.md  知识文档撰写.skill.md  项目架构文档撰写.skill.md  本科论文撰写.skill.md

ls 泛用skill/
# 输出：想法可行性的压力测试skill.md
```

**3. 在 Claude Desktop 中注册技能**

将本仓库根目录路径添加为技能来源目录。Claude Desktop 启动时会递归扫描该路径下的所有 `.skill.md` 文件，解析其 frontmatter 并自动注册。

**4. 验证技能已加载**

在 Claude Desktop 对话中说出某个技能的触发短语。例如输入"帮我写一份 README"，Claude 应自动加载 README 撰写技能并引导你完成文档编写。

## 安装指南

技能通过 Claude Desktop 的技能目录机制加载。将本仓库放置在任意路径后，在 Claude Desktop 的技能设置中将该路径添加到技能搜索目录列表。

```
技能来源路径示例：
/path/to/personal-skills
```

Claude Desktop 启动时会递归扫描该目录及其子目录，读取所有 `.skill.md` 文件并解析 YAML frontmatter。技能的识别依据是文件名（作为技能 ID）和 frontmatter 中的 `description` 字段（作为触发匹配依据）。

目录结构：

```
personal-skills/
├── README.md
├── 文档撰写/
│   ├── README撰写.skill.md           # 项目 README 撰写技能
│   ├── prompt撰写.skill.md           # Prompt 撰写与优化技能
│   ├── 知识文档撰写.skill.md         # 技术知识教程撰写技能
│   ├── 项目架构文档撰写.skill.md     # 项目架构设计文档撰写技能
│   └── 本科论文撰写.skill.md         # 本科学术论文撰写与润色技能
└── 泛用skill/
    └── 想法可行性的压力测试skill.md  # 想法压力测试技能
```

## 使用示例

### 示例 1：撰写项目 README

**场景**：你刚完成一个新项目，需要一份面向开发者的 README 文档。

在 Claude Desktop 中输入：

> 帮我写一份 README。项目叫 MyApp，用 Python + FastAPI 搭建的 REST API 服务，数据库用 PostgreSQL。

Claude 加载 `project-readme` 技能，按"项目简介 → 前置条件 → 快速开始 → 安装指南 → 使用示例 → 配置说明 → 项目结构 → 贡献指南 → 常见问题 → 更新日志"的结构输出完整的 Markdown 文档。

### 示例 2：整理零散笔记为知识文档

**场景**：你有一份关于 Docker 的零散学习笔记，想整理成递进式教程。

在 Claude Desktop 中输入：

> 帮我把这份 Docker 笔记整理成面向初学者的知识文档。

Claude 加载 `knowledge-doc` 技能，以 lecture-note 风格重组内容：先列学习目标，再按"概念 → 基础操作 → 进阶操作 → 常见问题"递进展开，配 Mermaid 流程图，末尾附章节小结和练习建议。

### 示例 3：对想法进行压力测试

**场景**：你有一个产品方案，想在推进前找出潜在问题。

在 Claude Desktop 中输入：

> 帮我评测一下这个想法：我想做一个面向大学生的 AI 写作助手，按月付费。

Claude 加载"想法可行性的压力测试"技能，从核心假设、逻辑漏洞、边界条件、实施障碍、替代方案、二阶效应等维度系统性地审视你的方案，给出改进建议和风险评估。

### 示例 4：撰写 Prompt

**场景**：你想撰写一个新的 skill prompt，用于特定技术文档的生成。

在 Claude Desktop 中输入：

> 帮我写一个 prompt，用来生成自动化的 API 接口文档。

Claude 加载 `prompt-writing` 技能，按"角色定义 → 任务说明 → 输入说明 → 输出规范 → 约束条件 → 示例 → 异常处理"的结构输出完整的 Prompt 文档。

### 示例 5：技能路由不匹配时的处理

**场景**：你说的话包含某个技能的关键词，但实际意图不匹配。

> 我想写一篇关于"压力测试在软件开发中的应用"的论文。

这里虽然出现了"压力测试"，但真实意图是"写论文"而非"评测想法"。Claude 的技能路由会根据会话上下文综合判断，优先匹配 `thesis-writing` 技能。如果匹配结果不符合预期，你可以直接说出目标技能名称来强制路由。

## 配置说明

每个技能通过 `.skill.md` 文件中的 YAML frontmatter 进行声明式配置，无需额外配置文件。

### frontmatter 字段

| 字段            | 类型       | 必填  | 说明                               |
| ------------- | -------- | --- | -------------------------------- |
| `name`        | string   | 是   | 技能标识名，支持中文、英文或 kebab-case        |
| `description` | string   | 是   | 触发条件描述，Claude Desktop 将此文本作为匹配依据 |
| `trigger`     | string[] | 否   | 额外的触发短语列表，提高匹配准确度（仅部分技能使用）       |

示例（来自 `README撰写.skill.md`）：

```yaml
---
name: project-readme
description: 撰写项目 README 文档——当用户提供项目信息要求撰写项目文档、README、快速上手指南时使用
---
```

### 技能文件的命名约定

技能文件以 `.skill.md` 为扩展名，文件名本身作为技能的识别 ID。例如 `README撰写.skill.md` 中，`README撰写` 即该技能的标识。中文命名使技能用途一目了然。

## 技能清单

| 技能 ID                | 文件                         | 用途                         |
| -------------------- | -------------------------- | -------------------------- |
| project-readme       | 文档撰写/README撰写.skill.md     | 撰写项目 README 与快速上手指南        |
| prompt-writing       | 文档撰写/prompt撰写.skill.md     | 从零撰写或优化技术文档相关的 Prompt      |
| knowledge-doc        | 文档撰写/知识文档撰写.skill.md       | 面向初学者的 lecture-note 风格技术教程 |
| project-architecture | 文档撰写/项目架构文档撰写.skill.md     | 项目架构设计文档，含模块设计与设计决策记录      |
| thesis-writing       | 文档撰写/本科论文撰写.skill.md       | 本科学术论文撰写与润色辅导              |
| 想法可行性的压力测试           | 泛用skill/想法可行性的压力测试skill.md | 全方位批判性审视想法或方案              |

## 项目结构

```
personal-skills/
├── README.md                                   # 本文件
├── 文档撰写/
│   ├── README撰写.skill.md                     # project-readme：项目 README 撰写指南
│   ├── prompt撰写.skill.md                     # prompt-writing：Prompt 撰写与优化
│   ├── 知识文档撰写.skill.md                   # knowledge-doc：技术教程（lecture-note 风格）
│   ├── 项目架构文档撰写.skill.md               # project-architecture：架构设计文档
│   └── 本科论文撰写.skill.md                   # thesis-writing：本科论文撰写与润色
└── 泛用skill/
    └── 想法可行性的压力测试skill.md            # 方案压力测试与批判性思维
```

各目录职责：

- `文档撰写/` —— 存放与中文技术文档撰写相关的 5 个技能，涵盖 README、知识教程、架构文档、Prompt 和学术论文
- `泛用skill/` —— 存放不限于文档撰写场景的通用思维技能

## 贡献指南

**新增技能**

1. 在合适的目录（`文档撰写/` 或 `泛用skill/`）下创建新的 `.skill.md` 文件
2. 编写 YAML frontmatter，至少包含 `name` 和 `description` 两个字段
3. 正文按技能规范编写：先定义角色（你是一位……），再明确任务流程、输出格式、风格要求、约束条件和触发条件
4. 在 `README.md` 的技能清单和项目结构中补充新条目

**修改已有技能**

直接编辑对应 `.skill.md` 文件。如果技能行为有重大变更，在文件末尾以 HTML 注释 `<!-- vX.Y 变更说明 -->` 记录。

**编写规范**

- 使用 Markdown 格式，frontmatter 后紧跟空行
- 技能 `name` 使用有意义的标识：英文技能用 kebab-case，中文技能直接用中文
- `description` 需包含触发关键词和使用场景，以便 Claude Desktop 准确匹配
- 正文自包含：不引用"当前会话"中的临时信息，每条指令应是 Claude 可直接执行的明确操作
- 避免使用 emoji（除非技能本身要求）

## 常见问题

**问：为什么我新加的技能没有被 Claude Desktop 识别？**

确认：(1) 文件名以 `.skill.md` 结尾；(2) frontmatter 包含 `name` 和 `description` 字段且格式正确；(3) 文件存放路径在 Claude Desktop 的技能搜索目录范围内；(4) Claude Desktop 已重启以重新扫描。

**问：多个技能的触发词有重叠怎么办？**

Claude Desktop 的路由逻辑会根据会话上下文选择最匹配的技能。如果多次出现误匹配，可以调整冲突技能的 `description` 字段，使其触发条件更加差异化。也可以添加 `trigger` 数组字段，列出具体的触发短语。

**问：技能文件能引用外部脚本或配置吗？**

当前不支持。所有逻辑必须内嵌在 `.skill.md` 的 Markdown 正文中。如需执行代码，技能应引导 Claude 使用其已有的工具（如 Bash）来完成。

**问：能否在技能中引用其他技能？**

技能应为自包含单元，不直接引用其他技能。如果多个技能共享部分逻辑，建议将共性内容抽象为独立技能，或在需要处重复该逻辑。

**问：这些技能和 Claude Code / Claude Agent SDK 有什么关系？**

这些技能是为 Claude Desktop 的 Cowork 模式设计的，不属于 Claude Code 或 Claude Agent SDK 的功能范畴。它们通过 Cowork 的技能加载机制生效。

## 更新日志

| 版本    | 日期      | 变更说明                                                                                                          |
| ----- | ------- | ------------------------------------------------------------------------------------------------------------- |
| 2.0.0 | 2026-06 | 重构仓库结构：将技能按类别归入 `文档撰写/` 和 `泛用skill/` 目录；将原"技术文档撰写与整理"拆分为 5 个独立技能（README、Prompt、知识文档、架构文档、论文）；新增"想法可行性的压力测试"技能 |
| 1.0.0 | 2026-06 | 初始版本，包含 schedule、setup-cowork、consolidate-memory、技术文档撰写与整理 共 4 个技能                                            |
