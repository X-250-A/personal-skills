---
name: agent-instruction-spec
description: 撰写或修改 AGENTS.md 开发规范文件——当用户要求写或改项目的 AGENTS.md、agent 开发规范、给 coding agent 立规矩时使用。
trigger:
  - 写一份 AGENTS.md
  - 改一下 AGENTS.md
  - agent 开发规范
  - 给这个项目写 agent 规范
---

# AGENT .md撰写规范
## 角色

你是给 coding agent 写开发规范的工程师。产出物是 `AGENTS.md`。

它只做三件事：让 agent 省事（不用自己查信源）、懂事（交付标准明确）、不试探边界（能碰什么写清楚）。

## 写作规则

- 只写改代码时要遵守的规则和要跑的命令，可包含少量必要的技术栈等的描述
- 模板字段非必填，要根据项目实际自行增减标题
- 命令必须先跑通再写，禁止凭印象编
- 命令写到能直接复制的程度，带项目特有的参数、过滤、路径，不要只写入口命令
- 信源直接给路径：配置、日志、CI 在哪

## 模板

````markdown
# AGENTS.md 示例

## 开发环境提示
- 用 `pnpm dlx turbo run where <project_name>` 直接跳到某个包，不要用 `ls` 扫。
- 跑 `pnpm install --filter <project_name>` 把包装进 workspace，这样 Vite、ESLint 和 TypeScript 才能看到它。
- 用 `pnpm create vite@latest <project_name> -- --template react-ts` 新建一个 React + Vite 包，TypeScript 检查已配好。
- 核对每个包 package.json 里的 name 字段确认包名，跳过顶层那个。

## 测试说明
- CI 流程在 .github/workflows 目录里找。
- 跑 `pnpm turbo run test --filter <project_name>` 执行该包定义的全部检查。
- 在包根目录直接跑 `pnpm test`。合并前提交必须通过全部测试。
- 只想跑一个步骤时，加 Vitest 过滤：`pnpm vitest run -t "<test name>"`。
- 测试或类型报错要修到整套全绿。
- 移动文件或修改 import 后，跑 `pnpm lint --filter <project_name>` 确认 ESLint 和 TypeScript 规则仍通过。
- 改过的代码要补测试，即使没人要求。

## PR 说明
- 标题格式：`[<project_name>] <Title>`
- 提交前必须跑 `pnpm lint` 和 `pnpm test`。
````

## 反例

````markdown
# 刷题站

本仓库是 FastAPI + sqlite3 + 原生 JS 的刷题站。      ← 项目说明与技术栈
判题编译器在 C:\Users\Lenovo\msys64\ucrt64\bin。     ← 本机环境
空代码提交必须返回 400。                             ← 业务规则

## 1. 开发规范

- 遵循现有目录分层。                                 ← 换任何项目都成立
- 错误显式抛出，不静默吞异常。                       ← 空话
- 更新锁文件 pyproject.toml。                        ← 项目里没有，凭空写

## 2. 常用命令

uvicorn main:app --reload                            ← 实测不可靠
uv run pytest                                        ← 入口级命令，没有项目特异的参数
````
