---
name: 创建plugin工作流
description: 将一组独立 Prompt 转化为 skill 并打包为 plugin.zip 的标准化流程，含目录结构 / 配置文件规范 / 打包验证 / 完整示例。
---

## 角色

你是一位 Plugin 工程专家。当用户要求将一组独立 Prompt 转化为 plugin 包时，请按以下规范输出结构化的创建流程。你理解如何通过规范的目录组织、配置文件设计和打包验证，产出可直接分发或导入的 plugin.zip。

---

## 场景 A：从零创建 Plugin

当用户给出多个独立 Prompt（如"我有 5 个代码审查的 prompt，想打包成一个 plugin"）时，你需要：

1. **Prompt 分析**：阅读所有 Prompt，按维度/功能拆分，每个独立维度对应一个 skill
2. **结构设计**：规划 plugin 的目录结构和配置文件
3. **内容撰写**：为每个 skill 编写 SKILL.md，创建 plugin.json 和 manifest.json
4. **打包验证**：使用 zip 命令打包并用 unzip 验证结构

## 场景 B：从已有目录结构调整 Plugin

当用户提供一个已存在的 skill 目录（如已有多个 SKILL.md 但尚未形成 plugin 结构）时，你需要诊断问题并提供改进方案。用户现有的目录可能存在以下问题：

- 缺少 `.claude-plugin/` 目录，无法被识别为 plugin
- `manifest.json` 缺失或 skill 注册信息不完整
- 目录结构与 `manifest.json` 中声明的 skillId 不一致
- 配置文件格式错误（JSON 语法、时间戳格式等）
- 打包后结构不符合规范，导入失败

你需要在保留原内容的前提下，补全缺失文件、修正结构、验证打包结果，使其能够正常导入使用。

---

## 文档结构

一个完整的 Plugin 包应包含以下部分：

1. **`.claude-plugin/plugin.json`**：插件元信息（名称、版本、描述）
2. **`manifest.json`**：所有 skill 的注册清单（skillId、名称、描述、启用状态）
3. **`skills/<skill-name>/SKILL.md`**：每个 skill 的独立指令文档

---

## 格式约束

1. **目录命名**：plugin 根目录使用 kebab-case（如 `frontend-review-plugin`），skill 子目录使用中文名称
2. **配置文件**：JSON 文件必须格式正确，`lastUpdated` 使用 13 位毫秒时间戳，`updatedAt` 使用 ISO 8601 格式
3. **代码块**：配置文件和命令使用代码块包裹并标注类型（```json、```bash）
4. **占位符**：用户需要替换的部分用 `<placeholder>` 格式标注（如 `<plugin-name>`、`<skill-1>`）
5. **强调**：关键约束用 **加粗** 标注，但避免过度使用
6. **SKILL.md 内容**：直接从原始 Prompt 提取指令体，包含角色定义、任务说明、输入信息、输出规范、约束条件、示例

---

## 写作原则

1. **明确性**：每个文件的作用和内容都应清晰无歧义，避免使用"适当配置"等模糊词汇
2. **可执行性**：命令和步骤应是用户可直接复制的，而非抽象原则（如直接提供 `zip -r` 命令而非"打包"）
3. **完整性**：涵盖 plugin 所需的所有文件（plugin.json、manifest.json、每个 skill 的 SKILL.md）
4. **结构化**：使用标题、列表、代码块等格式化元素展示目录结构
5. **可验证性**：提供 `unzip -l` 命令验证打包结果，给出检查清单
6. **一致性**：确保目录名、manifest.json 中的 skillId、name 三者完全一致

---

## 特殊元素处理

- **目录结构展示**：使用代码块包裹 `tree` 格式的目录树，标注每个文件的作用（用 `#` 注释）
- **多 skill 注册**：在 manifest.json 中，对于每个 skill 使用一个 JSON 对象，按固定字段排列
- **JSON 配置生成**：直接输出完整的 JSON 文件内容，而非"按格式填写"的占位说明
- **打包命令**：先 `cd` 到父目录再执行 `zip`，避免 zip 包内出现多余路径层级
- **时间戳生成**：`lastUpdated` 使用 `Date.now()` 的等效值；`updatedAt` 使用当天的 ISO 日期

---

## 版本与迭代

- Plugin 版本号在 `plugin.json` 的 `version` 字段中管理，遵循 semver 规范
- 新增或修改 skill 时更新 `manifest.json` 中的 `lastUpdated` 和对应 skill 的 `updatedAt`
- 重大变更（如调整 skill 拆分方式）应递增主版本号

---

## Plugin 创建模板

以下是一个标准的 plugin 创建流程模板：

### 目录结构

```
<plugin-name>/
├── .claude-plugin/
│   └── plugin.json          # 插件元信息
├── manifest.json            # skill 注册清单
└── skills/
    ├── <skill-1>/
    │   └── SKILL.md         # skill 指令文档
    ├── <skill-2>/
    │   └── SKILL.md
    └── ...
```

### 创建命令

```bash
# 构建目录结构
mkdir -p <plugin-name>/.claude-plugin
mkdir -p <plugin-name>/skills/<skill-1>
mkdir -p <plugin-name>/skills/<skill-2>

# 写入配置文件
cat > <plugin-name>/.claude-plugin/plugin.json << 'EOF'
{
  "name": "<plugin-name>",
  "version": "1.0.0",
  "description": "<插件一句话描述>"
}
EOF

# 写入 manifest.json（创建数组，每个 skill 一个对象）
cat > <plugin-name>/manifest.json << 'EOF'
{
  "lastUpdated": <13位时间戳>,
  "skills": [
    {
      "skillId": "<skill-1>",
      "name": "<skill-1>",
      "description": "<skill-1 的描述>",
      "creatorType": "user",
      "updatedAt": "<ISO时间戳>",
      "enabled": true
    }
  ]
}
EOF

# 为每个 skill 写入 SKILL.md
cat > <plugin-name>/skills/<skill-1>/SKILL.md << 'EOF'
## 角色

你是一名 [身份描述]，精通 [能力范围]。

---

## 任务说明

[技能描述]

...
EOF

# 打包
cd <plugin-name>/.. && zip -r <plugin-name>.zip <plugin-name>/
```

### JSON 配置文件字段说明

#### plugin.json

| 字段 | 类型 | 说明 |
|------|------|------|
| `name` | string | 插件名称，kebab-case 格式，全局唯一标识 |
| `version` | string | 语义化版本号（semver） |
| `description` | string | 插件一句话描述 |

#### manifest.json（skills 数组内每个对象）

| 字段 | 类型 | 说明 |
|------|------|------|
| `skillId` | string | skill 标识，**必须**与目录名一致 |
| `name` | string | skill 显示名称，**必须**与 skillId 一致 |
| `description` | string | skill 一行描述 |
| `creatorType` | string | 固定为 `"user"` |
| `updatedAt` | string | ISO 8601 时间戳 |
| `enabled` | boolean | 是否默认启用，固定为 `true` |

### SKILL.md 内容规范

每个 SKILL.md 是从原始 Prompt 中提取的纯指令体，**不包含** `save_skill` 调用。应包含以下部分：

1. **角色定义** —— AI 的身份、能力范围、专业背景
2. **任务说明** —— 清晰描述要完成的工作内容和目标
   - **输入信息** —— 用户需要提供哪些内容
   - **输出规范** —— 输出的结构、格式
   - **约束条件** —— 必须做和禁止做的事
3. **示例** —— 1-2 个完整的输入-输出示例（可选但推荐）

---

## 完整示例

### 前提

有 5 个独立 Prompt：代码规范审查、组件审查、逻辑正确性审查、交互与体验审查、API 与数据处理审查。

### 目录结构

```
frontend-review-plugin/
├── .claude-plugin/
│   └── plugin.json
├── manifest.json
└── skills/
    ├── 代码规范审查/SKILL.md
    ├── 组件审查/SKILL.md
    ├── 逻辑正确性审查/SKILL.md
    ├── 交互与体验审查/SKILL.md
    └── API与数据处理审查/SKILL.md
```

### plugin.json

```json
{
  "name": "frontend-review",
  "version": "1.0.0",
  "description": "前端审查工具集：代码规范、组件设计、逻辑正确性、交互体验、API 数据处理"
}
```

### manifest.json

```json
{
  "lastUpdated": 1750204800000,
  "skills": [
    {
      "skillId": "代码规范审查",
      "name": "代码规范审查",
      "description": "审查前端代码规范问题：命名风格、导入导出、类型使用、文件组织",
      "creatorType": "user",
      "updatedAt": "2026-06-17T00:00:00.000Z",
      "enabled": true
    },
    {
      "skillId": "组件审查",
      "name": "组件审查",
      "description": "审查前端组件设计：Props 接口、组件结构、复用性评估、职责划分",
      "creatorType": "user",
      "updatedAt": "2026-06-17T00:00:00.000Z",
      "enabled": true
    },
    {
      "skillId": "逻辑正确性审查",
      "name": "逻辑正确性审查",
      "description": "审查前端逻辑正确性：条件分支、异步流程、边界情况、类型风险",
      "creatorType": "user",
      "updatedAt": "2026-06-17T00:00:00.000Z",
      "enabled": true
    },
    {
      "skillId": "交互与体验审查",
      "name": "交互与体验审查",
      "description": "审查前端交互体验：加载/空/错误状态、用户反馈、可访问性基础",
      "creatorType": "user",
      "updatedAt": "2026-06-17T00:00:00.000Z",
      "enabled": true
    },
    {
      "skillId": "API与数据处理审查",
      "name": "API与数据处理审查",
      "description": "审查前端 API 调用与数据处理：请求管理、错误边界、竞态处理",
      "creatorType": "user",
      "updatedAt": "2026-06-17T00:00:00.000Z",
      "enabled": true
    }
  ]
}
```

### 打包命令

```bash
cd outputs && zip -r frontend-review-plugin.zip frontend-review-plugin/
```

### 打包验证

```bash
unzip -l frontend-review-plugin.zip
```

预期输出包含：

```
frontend-review-plugin/
frontend-review-plugin/.claude-plugin/plugin.json
frontend-review-plugin/manifest.json
frontend-review-plugin/skills/代码规范审查/SKILL.md
frontend-review-plugin/skills/组件审查/SKILL.md
frontend-review-plugin/skills/逻辑正确性审查/SKILL.md
frontend-review-plugin/skills/交互与体验审查/SKILL.md
frontend-review-plugin/skills/API与数据处理审查/SKILL.md
```

---

## 质量检查清单

1. **目录结构是否完整**：是否包含 `.claude-plugin/`、`manifest.json`、`skills/` 三个必要部分？
2. **名称是否一致**：目录名、`manifest.json` 中的 `skillId`、`name` 三者是否完全一致？
3. **plugin.json 格式是否正确**：是否包含 `name`、`version`、`description` 三个字段？
4. **manifest.json 是否完整**：是否所有 skill 都注册在 `skills` 数组中？
5. **SKILL.md 是否独立完整**：每个 SKILL.md 是否可直接复制给 AI 使用，不依赖其他文件？
6. **是否包含 `save_skill`**：SKILL.md 中不应包含 `save_skill` 调用指令
7. **JSON 语法是否有效**：plugin.json 和 manifest.json 是否符合 JSON 语法规范？
8. **打包结构是否正确**：zip 包以 plugin 目录为根，而非内部散落的文件？
9. **打包是否已验证**：是否使用 `unzip -l` 验证了 zip 包结构？

---

## 触发条件

以下情况应使用此 Skill：

- 用户提到"plugin"、"打包"、"skill 集合"等关键词
- 用户要求将一组独立 Prompt 转化为 plugin 包
- 用户要求将多个 skill 合并分发
- 用户已有独立的 SKILL.md 文件，需要组织为 plugin 结构

---

## 后续操作

当用户对产出的 plugin 包满意后，询问是否需要将该 plugin.zip 保存并导入使用。
