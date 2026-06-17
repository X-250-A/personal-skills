# personal-skills

personal-skills 是一个个人技能仓库，收集了一套可为 Claude Desktop（Cowork 模式）加载的自定义技能（skill）。每个技能是一个独立的**文件夹 + SKILL.md** 结构，以 YAML 前置元数据（frontmatter）声明技能名称和触发条件，正文为 Claude 提供执行特定任务的完整工作流指引。

本仓库目前包含 **12 个技能**，覆盖三大场景：

- **中文技术文档撰写**（5 个）—— 按特定风格撰写 README、知识教程、项目架构文档、学术论文，以及编写与优化 Prompt
- **通用思维工具**（2 个）—— 对想法进行压力测试、将独立 Prompt 打包为 plugin.zip
- **前端审查**（5 个）—— 对前端代码进行代码规范、组件设计、逻辑正确性、交互体验、API 数据处理五个维度的审查

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
# 文档撰写/      —— 5 个中文文档撰写类技能
# 泛用skill/     —— 2 个通用思维工具
# 项目审查/      —— 5 个前端审查类技能
```

**3. 在 Claude Desktop 中注册技能**

将本仓库根目录路径添加为技能来源目录。Claude Desktop 启动时会递归扫描该路径下的所有 `skills/` 子目录，解析 `SKILL.md` 中的 frontmatter 并自动注册。

**4. 验证技能已加载**

在 Claude Desktop 对话中说出的触发短语。例如输入"帮我写一份 README"，Claude 应自动加载 README 撰写技能并引导你完成文档编写。

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

### 示例 1：撰写项目 README

**场景**：你刚完成一个新项目，需要一份面向开发者的 README 文档。

在 Claude Desktop 中输入：

> 帮我写一份 README。项目叫 MyApp，用 Python + FastAPI 搭建的 REST API 服务，数据库用 PostgreSQL。

Claude 加载 `project-readme` 技能，按"项目简介 → 前置条件 → 快速开始 → 安装指南 → 使用示例 → 配置说明 → 项目结构 → 贡献指南 → 常见问题 → 更新日志"的结构输出完整的 Markdown 文档。

### 示例 2：对想法进行压力测试

**场景**：你有一个产品方案，想在推进前找出潜在问题。

在 Claude Desktop 中输入：

> 帮我评测一下这个想法：我想做一个面向大学生的 AI 写作助手，按月付费。

Claude 加载"想法可行性的压力测试"技能，从核心假设、逻辑漏洞、边界条件、实施障碍、替代方案、二阶效应等维度系统性地审视方案，给出改进建议和风险评估。

### 示例 3：前端代码审查（组件审查）

**场景**：你刚写完一个 React 组件，想让 Claude 帮你检查设计是否合理。

在 Claude Desktop 中输入：

> 帮我审查一下这个组件：```tsx function UserCard(props) { ... }```

Claude 加载"组件审查"技能，从 Props 设计、结构合理性、可复用性等维度评估组件质量。

### 示例 4：将一组 Prompt 打包为 plugin

**场景**：你有一组独立 Prompt，想组织为可分发、可导入的 plugin.zip。

在 Claude Desktop 中输入：

> 我有 5 个代码审查的 prompt，帮我打包成一个 plugin。

Claude 加载"创建plugin工作流"技能，按"目录结构 → 配置文件 → SKILL.md 撰写 → 打包验证"的完整流程输出。

### 示例 5：技能路由不匹配时的处理

**场景**：你说的话包含某个技能的关键词，但实际意图不匹配。

> 我想写一篇关于"压力测试在软件开发中的应用"的论文。

这里虽然出现了"压力测试"，但真实意图是"写论文"而非"评测想法"。Claude 的技能路由会根据会话上下文综合判断，优先匹配"学术论文撰写"技能。如果匹配结果不符合预期，可以直接说出目标技能名称来强制路由。

---

## 配置说明

每个技能通过 `SKILL.md` 文件中的 YAML frontmatter 进行声明式配置，无需额外配置文件。

### frontmatter 字段

| 字段            | 类型       | 必填  | 说明                               |
| ------------- | -------- | --- | -------------------------------- |
| `name`        | string   | 是   | 技能标识名，支持中文、英文或 kebab-case        |
| `description` | string   | 是   | 触发条件描述，Claude Desktop 将此文本作为匹配依据 |
| `trigger`     | string[] | 否   | 额外的触发短语列表，提高匹配准确度（仅部分技能使用）       |

示例（来自 `README撰写/SKILL.md`）：

```yaml
---
name: project-readme
description: 撰写项目 README 文档——当用户提供项目信息要求撰写项目文档、README、快速上手指南时使用
---
```

### 技能文件组织方式

技能以文件夹 + SKILL.md 形式组织：

```
<skill-name>/
└── SKILL.md
```

- 文件夹名称为技能标识 ID
- `SKILL.md` 中包含 YAML frontmatter 和完整的技能指令正文
- 相比单一 `.skill.md` 文件，文件夹结构便于为技能附带额外资源（如示例代码、模板文件）

---

## 技能清单

### 文档撰写

| 技能 ID                | 路径                       | 用途                         |
| -------------------- | ------------------------ | -------------------------- |
| project-readme       | `文档撰写/README撰写/SKILL.md` | 撰写项目 README 与快速上手指南        |
| prompt-writing       | `文档撰写/prompt撰写/SKILL.md` | 从零撰写或优化技术文档相关的 Prompt      |
| knowledge-doc        | `文档撰写/知识文档撰写/SKILL.md`   | 面向初学者的 lecture-note 风格技术教程 |
| project-architecture | `文档撰写/项目架构文档撰写/SKILL.md` | 项目架构设计文档，含模块设计与设计决策记录      |
| 学术论文撰写               | `文档撰写/本科论文撰写/SKILL.md`   | 本科学术论文撰写与润色辅导              |

### 通用思维工具

| 技能 ID       | 路径                             | 用途                        |
| ----------- | ------------------------------ | ------------------------- |
| 想法可行性的压力测试  | `泛用skill/想法可行性的压力测试/SKILL.md`  | 全方位批判性审视想法或方案             |
| 创建plugin工作流 | `泛用skill/创建plugin工作流/SKILL.md` | 将独立 Prompt 打包为 plugin.zip |

### 前端审查

| 技能 ID      | 路径                              | 用途                   |
| ---------- | ------------------------------- | -------------------- |
| 代码规范审查     | `项目审查/前端审查/代码规范审查/SKILL.md`     | 审查命名风格、导入导出、文件组织     |
| 组件审查       | `项目审查/前端审查/组件审查/SKILL.md`       | 审查 Props 接口、组件结构、复用性 |
| 逻辑正确性审查    | `项目审查/前端审查/逻辑正确性审查/SKILL.md`    | 审查条件分支、异步流程、边界情况     |
| 交互与体验审查    | `项目审查/前端审查/交互与体验审查/SKILL.md`    | 审查加载/空/错误状态、用户反馈、无障碍 |
| API与数据处理审查 | `项目审查/前端审查/API与数据处理审查/SKILL.md` | 审查请求管理、错误边界、竞态处理     |

---

## 项目结构

```
personal-skills/
├── README.md                              # 本文件
├── 文档撰写/                              # 中文技术文档撰写类技能（5个）
│   ├── README撰写/SKILL.md                # project-readme
│   ├── prompt撰写/SKILL.md                # prompt-writing
│   ├── 知识文档撰写/SKILL.md              # knowledge-doc
│   ├── 项目架构文档撰写/SKILL.md          # project-architecture
│   └── 本科论文撰写/SKILL.md              # 学术论文撰写
├── 泛用skill/                             # 通用思维工具（2个）
│   ├── 创建plugin工作流/SKILL.md          # 创建plugin工作流
│   └── 想法可行性的压力测试/SKILL.md      # 想法可行性的压力测试
└── 项目审查/                              # 前端代码审查类技能（5个）
    └── 前端审查/
        ├── 代码规范审查/SKILL.md          # 代码规范审查
        ├── 组件审查/SKILL.md              # 组件审查
        ├── 逻辑正确性审查/SKILL.md        # 逻辑正确性审查
        ├── 交互与体验审查/SKILL.md        # 交互与体验审查
        └── API与数据处理审查/SKILL.md     # API与数据处理审查
```

各目录职责：

- `文档撰写/` —— 存放与中文技术文档撰写相关的 5 个技能，涵盖 README、知识教程、架构文档、Prompt 和学术论文
- `泛用skill/` —— 存放不限于文档撰写场景的通用思维技能，包括想法压力测试和 plugin 打包
- `项目审查/前端审查/` —— 存放前端代码审查相关的 5 个技能，覆盖代码规范、组件设计、逻辑正确性、交互体验和 API 数据处理

---

## 贡献指南

### 新增技能

1. 在合适的父目录下创建新文件夹作为技能 ID
2. 在该文件夹中创建 `SKILL.md` 文件
3. 编写 YAML frontmatter，至少包含 `name` 和 `description` 两个字段
4. 正文按技能规范编写：先定义角色，再明确任务流程、输出格式、风格要求、约束条件和触发条件
5. 在 `README.md` 的技能清单和项目结构中补充新条目

### 修改已有技能

直接编辑对应 `SKILL.md` 文件。如果技能行为有重大变更，在文件末尾以 HTML 注释 `<!-- vX.Y 变更说明 -->` 记录。

### 编写规范

- 使用 Markdown 格式，frontmatter 后紧跟空行
- 技能 `name` 使用有意义的标识：英文技能用 kebab-case，中文技能直接用中文
- `description` 需包含触发关键词和使用场景，以便 Claude Desktop 准确匹配
- 正文自包含：不引用"当前会话"中的临时信息，每条指令应是 Claude 可直接执行的明确操作
- 避免使用 emoji（除非技能本身要求）

### 提交流程

1. Fork 本仓库
2. 创建特性分支：`git checkout -b feat/new-skill`
3. 提交变更：`git commit -m "feat: 添加 xxx 技能"`
4. 推送分支：`git push origin feat/new-skill`
5. 创建 Pull Request

---

## 常见问题

**问：为什么我新加的技能没有被 Claude Desktop 识别？**

确认：(1) 目录中包含 `SKILL.md` 文件；(2) frontmatter 包含 `name` 和 `description` 字段且格式正确；(3) 文件存放路径在 Claude Desktop 的技能搜索目录范围内；(4) Claude Desktop 已重启以重新扫描。

**问：多个技能的触发词有重叠怎么办？**

Claude Desktop 的路由逻辑会根据会话上下文选择最匹配的技能。如果多次出现误匹配，可以调整冲突技能的 `description` 字段，使其触发条件更加差异化。也可以添加 `trigger` 数组字段，列出具体的触发短语。

**问：技能文件能引用外部脚本或配置吗？**

当前不支持。所有逻辑必须内嵌在 `SKILL.md` 的 Markdown 正文中。如需执行代码，技能应引导 Claude 使用其已有的工具（如 Bash）来完成。

**问：能否在技能中引用其他技能？**

技能应为自包含单元，不直接引用其他技能。如果多个技能共享部分逻辑，建议将共性内容抽象为独立技能，或在需要处重复该逻辑。

**问：这个仓库的目录结构从 `.skill.md` 变为文件夹 `SKILL.md` 后有什么变化？**

原来是每个技能一个独立的 `.skill.md` 文件，目前已统一迁移为 `<技能名称>/SKILL.md` 的文件夹结构。这种组织方式更符合 Cowork 的技能加载规范，也便于为技能附带额外资源文件。

**问：这些技能和 Claude Code / Claude Agent SDK 有什么关系？**

这些技能是为 Claude Desktop 的 Cowork 模式设计的，不属于 Claude Code 或 Claude Agent SDK 的功能范畴。它们通过 Cowork 的技能加载机制生效。

---

## 更新日志

| 版本    | 日期      | 变更说明                                                                                                    |
| ----- | ------- | ------------------------------------------------------------------------------------------------------- |
| 3.0.0 | 2026-06 | 重构为文件夹 + SKILL.md 模式；新增"创建plugin工作流"技能；新增"前端审查"系列 5 个技能；整合三大分类：文档撰写(5) / 泛用skill(2) / 前端审查(5)，总计 12 个技能 |
| 2.0.0 | 2026-06 | 将技能按类别归入 `文档撰写/` 和 `泛用skill/` 目录；将原"技术文档撰写与整理"拆分为 5 个独立技能；新增"想法可行性的压力测试"技能                              |
| 1.0.0 | 2026-06 | 初始版本，包含 schedule、setup-cowork、consolidate-memory、技术文档撰写与整理 共 4 个技能                                      |

---

> 更多帮助请参考 [个人仓库 Wiki](https://github.com/X-250-A/personal-skills/wiki) 或提交 [Issue](https://github.com/X-250-A/personal-skills/issues)

<!-- TODO: 如果 Wiki 实际尚未启用，请移除上述链接或替换为有效地址 -->
