# personal-skills

个人技能仓库 —— 一套可被 Claude Desktop 加载的自定义技能（skill）集合。每个技能都是一个独立的 Markdown 文档，以 YAML 前置元数据（frontmatter）声明技能名称和触发条件，正文为 Claude 提供完成特定任务所需的完整工作流指引。

## 项目简介

personal-skills 是一个用于扩展 Claude Desktop 能力的技能插件仓库。当你对 Claude 说出特定关键词（如"每天早上"、"整理我的记忆"、"写一篇技术文档"），Claude Desktop 会自动匹配对应的技能文件，加载其中的工作流指引，从而以更一致、更高质量的方式完成你的请求。

本仓库目前包含 4 个技能，覆盖以下场景：

- **自动化排程** —— 将当前会话内容提炼为可定期执行的后台任务
- **新手引导** —— 帮助新用户完成 Cowork 的上手配置
- **记忆维护** —— 定期整理和合并持久化记忆文件，去除冗余和过时信息
- **中文技术文档撰写** —— 按 lecture-note 风格或 README 风格撰写结构化中文文档

每个技能都是一个自包含的 SKILL.md 文件，存放在 `skills/<技能名>/` 目录下，不依赖外部脚本或配置。

## 前置条件

| 依赖项 | 最低版本 | 说明 |
|--------|----------|------|
| Claude Desktop | 最新稳定版 | 需启用 Cowork 模式以支持技能加载 |
| 操作系统 | Windows / macOS / Linux | 无特殊限制 |
| 文本编辑器 | 任意 | 用于编辑 SKILL.md 文件 |

## 快速开始

**1. 克隆仓库**

```bash
git clone <your-repo-url> personal-skills
cd personal-skills
```

**2. 确认技能文件结构**

```bash
ls skills/
# 输出：schedule/  setup-cowork/  consolidate-memory/  技术文档撰写与整理/
```

**3. 在 Claude Desktop 中注册技能**

将仓库根目录路径配置为技能来源目录（具体配置方式见下方安装指南），重启 Claude Desktop 使技能生效。

**4. 验证技能已加载**

在 Claude Desktop 的对话中直接说出某个技能的触发短语，例如"帮我每天早八点检查邮件"，Claude 应自动加载排程技能并引导你完成后续设置。

## 安装指南

### 通过技能目录加载

技能通过 Claude Desktop 的技能目录机制加载。将本仓库放置在任意路径后，在 Claude Desktop 的技能设置中将该路径添加到技能搜索目录列表。

```
技能来源路径示例：
/path/to/personal-skills/skills
```

Claude Desktop 启动时会扫描该目录下的所有子目录，读取每个子目录中的 `SKILL.md` 文件并解析其 YAML frontmatter。

### 目录结构约定

```
personal-skills/
├── .claude-plugin/
│   └── plugin.json          # 插件元数据声明
├── manifest.json            # 技能注册清单
└── skills/
    ├── schedule/
    │   └── SKILL.md
    ├── setup-cowork/
    │   └── SKILL.md
    ├── consolidate-memory/
    │   └── SKILL.md
    └── 技术文档撰写与整理/
        └── SKILL.md
```

每个技能子目录的名称即为技能的标识符，`SKILL.md` 为唯一的技能定义文件。

## 使用示例

### 示例 1：创建每日定时任务

**场景**：你想每天上午 9 点自动获取一次新闻摘要。

在 Claude Desktop 中输入：

> 每天早上 9 点帮我搜索最新的 AI 领域新闻并做一个简短摘要。

Claude 加载 `schedule` 技能，自动提炼任务内容，生成 cron 表达式 `0 9 * * *`，并调用 `create_scheduled_task` 工具完成注册。预期结果是一个命名为 `daily-ai-news` 的后台任务出现在排程列表中。

### 示例 2：整理逐渐臃肿的记忆文件

**场景**：使用一段时间后，你的记忆文件夹积累了若干内容重叠或过时的记录。

在 Claude Desktop 中输入：

> 帮我整理一下记忆文件，合并重复的，清理过期的。

Claude 加载 `consolidate-memory` 技能，执行三阶段流程：列出并读取所有记忆文件 → 合并重叠主题、将相对时间改为绝对日期、删除可重新推导的信息 → 精简 `MEMORY.md` 索引。预期结果是一份清爽的记忆目录和一份变更摘要。

### 示例 3：错误处理 —— 触发词不够明确

**场景**：你说的话恰好包含某个技能的关键词，但实际意图并不匹配。

> 今天早上我准备写一份技术文档，你能帮我吗？

这里虽然出现了"早上"和"技术文档"，但你的真实意图是"写文档"而非"设置排程"。Claude 的技能路由会根据当前会话内容综合判断，优先匹配表述更完整的 `技术文档撰写与整理` 技能。如果匹配结果不符合预期，你可以直接说出目标技能的名称来强制路由。

## 配置说明

每个技能通过 `SKILL.md` 中的 YAML frontmatter 进行声明式配置，无需额外配置文件。

### SKILL.md frontmatter 字段

| 字段 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `name` | string | 是 | 技能标识名，支持中文或 kebab-case |
| `description` | string | 是 | 触发条件描述，Claude Desktop 将此文本作为匹配依据 |

示例：

```yaml
---
name: "schedule"
description: "Create or update a scheduled task that runs automatically."
---
```

### manifest.json 字段

仓库根目录的 `manifest.json` 为每个技能提供统一注册入口。

| 字段 | 类型 | 说明 |
|------|------|------|
| `skillId` | string | 技能唯一 ID |
| `name` | string | 显示名称 |
| `description` | string | 简短功能描述 |
| `creatorType` | string | 创建者类型（`anthropic` 或 `user`） |
| `updatedAt` | number | 最后更新时间（Unix 毫秒时间戳） |
| `enabled` | boolean | 是否启用 |

## SKILL.md 编写规范

技能文件的正文部分为 Claude 提供执行任务所需的完整指引。以下为编写建议：

**结构要求**

- 以一级标题（`#`）开篇，点明技能用途
- 正文采用分步骤或分阶段的流程结构，每个步骤明确告知 Claude 应当做什么、怎样判断
- 涉及工具调用时，使用反引号标注工具名称（如 `` `create_scheduled_task` ``）
- 指明决策边界：何时使用、何时不使用、何时询问用户

**内容要求**

- 自包含：技能正文不应引用"当前会话"中的临时信息，因为排程任务会在全新的会话中执行
- 可执行：每个步骤应是 Claude 可直接执行的明确指令
- 适度约束：既不过于死板（给 Claude 合理的判断空间），也不过于模糊

**示例骨架**

```markdown
---
name: "my-skill"
description: "Brief trigger description"
---

# 技能名称

## 步骤一：分析输入

[描述 Claude 应如何理解用户意图]

## 步骤二：执行核心逻辑

[描述具体操作步骤]

## 步骤三：输出与确认

[描述如何向用户呈现结果]
```

## 项目结构

```
personal-skills/
├── .claude-plugin/
│   └── plugin.json            # 插件元信息（名称、版本、描述）
├── manifest.json              # 技能清单，注册所有可用技能
├── skills/
│   ├── schedule/
│   │   └── SKILL.md           # 自动化排程技能
│   ├── setup-cowork/
│   │   └── SKILL.md           # Cowork 新手引导技能
│   ├── consolidate-memory/
│   │   └── SKILL.md           # 记忆整理与维护技能
│   └── 技术文档撰写与整理/
│       └── SKILL.md           # 中文技术文档撰写技能
└── README.md                  # 本文件
```

各目录职责：

- `.claude-plugin/` —— 存放插件级别的元数据，供 Claude Desktop 识别插件身份和版本
- `manifest.json` —— 技能注册表，Claude Desktop 通过此文件发现并加载技能
- `skills/` —— 所有技能定义文件的存放目录，每个子目录对应一个技能

## 贡献指南

**提交新技能**

1. 在 `skills/` 下创建以技能名命名的子目录
2. 在该目录中编写 `SKILL.md`，遵循上文的编写规范
3. 在 `manifest.json` 中注册新技能条目，填写 `skillId`、`name`、`description`、`creatorType`（用户创建的使用 `"user"`）、`updatedAt`（当前 Unix 毫秒时间戳）
4. 若为新用户技能，还需在 `.claude-plugin/plugin.json` 中确认插件信息无误

**修改已有技能**

直接编辑对应目录下的 `SKILL.md`，更新 `manifest.json` 中该技能的 `updatedAt` 时间戳。

**代码风格要求**

- SKILL.md 使用 Markdown 格式，YAML frontmatter 后紧跟空行
- 技能 `name` 使用有意义的标识：英文技能用 kebab-case，中文技能直接用中文
- `description` 需包含触发关键词和使用场景，以便 Claude Desktop 准确匹配
- 避免在技能正文中使用 emoji（除非技能本身要求）

## 常见问题

**问：为什么我新建的技能没有出现在 Claude Desktop 中？**

确认以下三点：(1) `skills/<技能名>/SKILL.md` 文件存在且 frontmatter 字段完整；(2) `manifest.json` 中已注册该技能且 `enabled` 为 `true`；(3) Claude Desktop 已重启以重新扫描技能目录。

**问：两个技能的触发词有重叠怎么办？**

Claude Desktop 的路由逻辑会根据会话上下文选择最匹配的技能。如果多次出现误匹配，可以调整冲突技能的 `description` 字段，使其触发条件更加差异化。

**问：技能文件支持引用外部脚本吗？**

当前不支持。所有逻辑必须内嵌在 `SKILL.md` 的 Markdown 正文中。如需执行代码，技能应指导 Claude 使用其已有的工具（如 Bash）来完成。

**问：我能否在技能中引用其他技能？**

技能应为自包含单元，不直接引用其他技能。如果多个技能共享部分逻辑，建议将共性内容抽象为一个独立技能（如辅助函数库），或直接在需要处重复该逻辑。

**问：创建定时任务后，任务存储在什么位置？**

通过 `schedule` 技能创建的定时任务存储在你的本地调度目录中。每个任务是一个独立的 `SKILL.md` 文件，可在 Claude Desktop 中查看、修改或暂停。

## 更新日志

| 版本 | 日期 | 变更说明 |
|------|------|----------|
| 1.0.0 | 2026-06 | 初始版本，包含 schedule、setup-cowork、consolidate-memory、技术文档撰写与整理 共 4 个技能 |

更多帮助请参考 Claude Desktop 官方文档。
