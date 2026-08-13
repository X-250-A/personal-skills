# personal-skills

personal-skills 是一个个人技能仓库，收集了一套可为 Claude Desktop（Cowork 模式）加载的自定义技能（skill）。每个技能是一个独立的**文件夹 + SKILL.md** 结构，以 YAML 前置元数据（frontmatter）声明技能名称和触发条件，正文为 AI 提供执行特定任务的完整工作流指引。

本仓库目前包含 **4 个技能**，覆盖两大类场景：

- **通用思维工具**（2 个）—— 对想法进行压力测试、内容仓库的 git 提交与推送全流程
- **文档撰写**（2 个）—— 项目架构设计文档、Prompt 撰写与优化

每个技能采用**主文件 + references 按需加载**的结构：SKILL.md 只放流程、模板与约束，完整示例与详细模板放在 `references/` 目录下，模型执行时按需加载，既保证约束不丢失，又避免主文件臃肿稀释注意力。

每个技能自包含，不依赖外部脚本或配置；放在 Cowork 的技能搜索路径下即可被自动识别和加载。

---

## 前置条件

| 依赖项            | 要求                      | 说明                   |
| -------------- | ----------------------- | -------------------- |
| Claude Desktop | 最新稳定版                   | 需启用 Cowork 模式以支持技能加载 |
| 操作系统           | Windows / macOS / Linux | 无特殊限制                |
| 文本编辑器          | 任意                      | 用于编辑或新增 SKILL.md 文件  |

---

## 快速开始

**1. 克隆仓库**

```bash
git clone git@github.com:X-250-A/personal-skills.git
cd personal-skills
```

**2. 查看技能目录**

```bash
ls -R
# 泛用skill/     —— 2 个通用思维工具
# 文档撰写/      —— 2 个文档撰写类技能
```

**3. 在 Claude Desktop 中注册技能**

将本仓库根目录路径添加为技能来源目录。Claude Desktop 启动时会递归扫描该路径下的所有 `skills/` 子目录，解析 `SKILL.md` 中的 frontmatter 并自动注册。

**4. 验证技能已加载**

在 Claude Desktop 对话中说出的触发短语。例如输入"帮我评测一下这个想法"，Claude 应自动加载压力测试技能并引导你完成分析。

---

## 安装指南

技能通过 Claude Desktop 的技能目录机制加载。将本仓库放置在任意路径后，在 Claude Desktop 的技能设置中将该路径添加到技能搜索目录列表。

```
技能来源路径示例：
/path/to/personal-skills
```

Claude Desktop 启动时会递归扫描该目录及其子目录，查找 `SKILL.md` 文件并解析其 YAML frontmatter。技能的识别依据是所在文件夹名称（作为技能 ID）和 frontmatter 中的 `description` 字段（作为触发匹配依据）。

### 从源码安装

```bash
git clone git@github.com:X-250-A/personal-skills.git
# 无需额外构建或安装步骤
```

---

## 使用示例

### 示例 1：对想法进行压力测试

**场景**：你有一个产品方案，想在推进前找出潜在问题。

在 Claude Desktop 中输入：

> 帮我评测一下这个想法：我想做一个面向大学生的 AI 写作助手，按月付费。

Claude 加载"想法可行性的压力测试"技能，从核心假设、逻辑漏洞、边界条件、实施障碍、替代方案、二阶效应等维度系统性地审视方案，给出改进建议和风险评估。快速/标准/深入三种模式按问题规模自动匹配，完整示例按需加载 `references/examples-*.md`。

### 示例 2：提交内容仓库

**场景**：你更新了本仓库的 skill 文件，想按规范提交并推送。

在 Claude Desktop 中输入：

> 提交项目

Claude 加载"项目git审查，提交与github推送工作流"技能，按"审查改动 → 更新 README → 规范提交 → pull --rebase → 推送"五步流程执行，commit message 遵循 Conventional Commits 规范（`docs:` 优先）。

### 示例 3：撰写项目架构文档

**场景**：你有一个项目，想产出面向开发者的架构设计文档。

在 Claude Desktop 中输入：

> 帮我写一份这个项目的架构文档，深入讲模块设计和设计决策

Claude 加载"项目架构文档撰写"技能，确认项目类型（Web/前端/CLI/库/数据/嵌入式）与写作模式（交互/一次性/快速骨架），按 10 章节结构输出；章节完整模板在 `references/chapter-templates.md` 按需加载。

### 示例 4：撰写或优化 Prompt

**场景**：你需要一个结构化 Prompt，或想优化现有的 prompt。

在 Claude Desktop 中输入：

> 帮我写一个生成 API 文档的 prompt

Claude 加载"prompt撰写"技能，按 7 组件结构（角色/任务/输入/输出/约束/示例/异常处理）输出完整 Prompt；经迭代验证的完整示例在 `references/examples-tech.md`、`references/examples-optimize.md` 按需加载。

---

## 配置说明

每个技能通过 `SKILL.md` 文件中的 YAML frontmatter 进行声明式配置，无需额外配置文件。

### frontmatter 字段

| 字段            | 类型       | 必填  | 说明                               |
| ------------- | -------- | --- | -------------------------------- |
| `name`        | string   | 是   | 技能标识名，支持中文、英文或 kebab-case        |
| `description` | string   | 是   | 触发条件描述，Claude Desktop 将此文本作为匹配依据 |
| `trigger`     | string[] | 否   | 额外的触发短语列表，提高匹配准确度（仅部分技能使用）       |

示例（来自 `想法可行性的压力测试/SKILL.md`）：

```yaml
---
name: "想法可行性的压力测试"
description: "当用户提出想法并要求评测时使用——对想法进行全方位批判性审视，揭示潜在风险、逻辑漏洞和实施障碍"
trigger:
  - "帮我评测一下这个想法"
  - "看看这个方案有什么问题"
---
```

### 技能文件组织方式

技能以文件夹 + SKILL.md + references/ 形式组织：

```
<skill-name>/
├── SKILL.md          # 技能主文件：流程、模板、约束
└── references/       # 按需加载的完整示例与详细模板
    └── *.md
```

- 文件夹名称为技能标识 ID
- `SKILL.md` 中包含 YAML frontmatter 和完整的技能指令正文
- `references/` 存放完整示例、详细模板等扩展资源，主文件通过加载指引引用，模型执行时按需加载——避免主文件臃肿，同时保证约束不丢失

---

## 技能清单

### 通用思维工具

| 技能 ID | 路径 | 用途 |
| -------------------- | ------------------------ | -------------------------- |
| 想法可行性的压力测试 | `泛用skill/想法可行性的压力测试/SKILL.md` | 全方位批判性审视想法或方案；快速/标准/深入三档模式 |
| 项目git审查，提交与github推送工作流 | `泛用skill/项目git审查，提交与github推送工作流/SKILL.md` | 内容仓库端到端提交流程：审查→README→提交→同步→推送 |

### 文档撰写

| 技能 ID | 路径 | 用途 |
| -------------------- | ------------------------ | -------------------------- |
| project-architecture | `文档撰写/项目架构文档撰写/SKILL.md` | 项目架构设计文档：10 章节结构、交互/一次性/快速骨架三模式、Mermaid 图表 |
| prompt-writing | `文档撰写/prompt撰写/SKILL.md` | 从零撰写或优化技术文档相关的 Prompt：7 组件结构、质量检查清单 |

---

## 项目结构

```
personal-skills/
├── README.md                              # 本文件
├── 泛用skill/                             # 通用思维工具（2个）
│   ├── 想法可行性的压力测试/
│   │   ├── SKILL.md
│   │   └── references/                    # 快速/标准/深入三模式完整示例
│   └── 项目git审查，提交与github推送工作流/
│       ├── SKILL.md
│       └── references/                    # README撰写、提交注释撰写子规范
└── 文档撰写/                              # 文档撰写类技能（2个）
    ├── 项目架构文档撰写/
    │   ├── SKILL.md
    │   └── references/                    # 10章节完整模板 + Mermaid 示例
    └── prompt撰写/
        ├── SKILL.md
        └── references/                    # 从零撰写、优化 Prompt 完整示例
```

各目录职责：

- `泛用skill/` —— 通用思维技能：想法压力测试、内容仓库 git 提交流程
- `文档撰写/` —— 文档撰写技能：项目架构设计文档、Prompt 撰写与优化

---

## 贡献指南

### 新增技能

1. 在合适的父目录下创建新文件夹作为技能 ID
2. 在该文件夹中创建 `SKILL.md` 文件
3. 编写 YAML frontmatter，至少包含 `name` 和 `description` 两个字段
4. 正文按技能规范编写：先定义角色，再明确任务流程、输出格式、风格要求、约束条件和触发条件
5. 完整示例与详细模板放入 `references/` 目录，主文件只写流程与约束，通过加载指引引用
6. 在 `README.md` 的技能清单和项目结构中补充新条目

### 修改已有技能

直接编辑对应 `SKILL.md` 文件。如果技能行为有重大变更，在文件末尾以 HTML 注释 `<!-- vX.Y 变更说明 -->` 记录。

### 编写规范

- 使用 Markdown 格式，frontmatter 后紧跟空行
- 技能 `name` 使用有意义的标识：英文技能用 kebab-case，中文技能直接用中文
- `description` 需包含触发关键词和使用场景，以便 Claude Desktop 准确匹配
- 正文自包含：不引用"当前会话"中的临时信息，每条指令应是 AI 可直接执行的明确操作
- 避免使用 emoji（除非技能本身要求）

### 提交流程

内容仓库统一使用"项目git审查，提交与github推送工作流"技能：审查改动 → 更新 README → 按 Conventional Commits 规范提交（`docs:` 优先）→ `git pull --rebase` → 推送。

---

## 常见问题

**问：为什么我新加的技能没有被 Claude Desktop 识别？**

确认：(1) 目录中包含 `SKILL.md` 文件；(2) frontmatter 包含 `name` 和 `description` 字段且格式正确；(3) 文件存放路径在 Claude Desktop 的技能搜索目录范围内；(4) Claude Desktop 已重启以重新扫描。

**问：多个技能的触发词有重叠怎么办？**

Claude Desktop 的路由逻辑会根据会话上下文选择最匹配的技能。如果多次出现误匹配，可以调整冲突技能的 `description` 字段，使其触发条件更加差异化。也可以添加 `trigger` 数组字段，列出具体的触发短语。

**问：技能文件能引用外部脚本或配置吗？**

当前不支持。所有逻辑必须内嵌在 `SKILL.md` 的 Markdown 正文中。如需执行代码，技能应引导 AI 使用其已有的工具（如 Bash）来完成。

**问：`references/` 目录的作用是什么？**

`references/` 存放完整示例、详细模板等扩展资源（如压力测试的三模式示例、架构文档的章节模板、Prompt 撰写的完整示例）。主文件通过加载指引引用它们，模型执行时按需加载：既保持主文件精简、避免稀释注意力，又确保输出质量基准不丢失。

**问：能否在技能中引用其他技能？**

技能应为自包含单元，不直接引用其他技能。如果多个技能共享部分逻辑，建议将共性内容抽象为独立技能，或在需要处重复该逻辑。

**问：这些技能和 Claude Code / Claude Agent SDK 有什么关系？**

这些技能是为 Claude Desktop 的 Cowork 模式设计的，不属于 Claude Code 或 Claude Agent SDK 的功能范畴。它们通过 Cowork 的技能加载机制生效。

---

## 更新日志

| 版本    | 日期      | 变更说明                                                                                                    |
| ----- | ------- | ------------------------------------------------------------------------------------------------------- |
| 3.1.0 | 2026-08 | 精简仓库：移除低频技能 8 个（前端审查 5 件套、本科论文、知识文档、README 撰写、plugin 工作流）；prompt撰写 与 项目架构文档撰写 拆分为"主文件 + references 按需加载"结构 |
| 3.0.1 | 2026-07 | 更新说明：更新日志补充；修正各SKILL.md中的路径目录名称，使其与仓库实际路径一致 |
| 3.0.0 | 2026-06 | 重构为文件夹 + SKILL.md 模式；新增"创建plugin工作流"技能；新增"前端审查"系列 5 个技能；整合三大分类：文档撰写(5) / 泛用skill(2) / 前端审查(5)，总计 12 个技能 |
| 2.0.0 | 2026-06 | 将技能按类别归入 `文档撰写/` 和 `泛用skill/` 目录；将原"技术文档撰写与整理"拆分为 5 个独立技能；新增"想法可行性的压力测试"技能                              |
| 1.0.0 | 2026-06 | 初始版本，包含 schedule、setup-cowork、consolidate-memory、技术文档撰写与整理 共 4 个技能                                      |

---

> 更多帮助请参考 [个人仓库 Wiki](https://github.com/X-250-A/personal-skills/wiki) 或提交 [Issue](https://github.com/X-250-A/personal-skills/issues)

<!-- TODO: 如果 Wiki 实际尚未启用，请移除上述链接或替换为有效地址 -->
