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
# Sample AGENTS.md file

## Dev environment tips
- Use `pnpm dlx turbo run where <project_name>` to jump to a package instead of scanning with `ls`.
- Run `pnpm install --filter <project_name>` to add the package to your workspace so Vite, ESLint, and TypeScript can see it.
- Use `pnpm create vite@latest <project_name> -- --template react-ts` to spin up a new React + Vite package with TypeScript checks ready.
- Check the name field inside each package's package.json to confirm the right name—skip the top-level one.

## Testing instructions
- Find the CI plan in the .github/workflows folder.
- Run `pnpm turbo run test --filter <project_name>` to run every check defined for that package.
- From the package root you can just call `pnpm test`. The commit should pass all tests before you merge.
- To focus on one step, add the Vitest pattern: `pnpm vitest run -t "<test name>"`.
- Fix any test or type errors until the whole suite is green.
- After moving files or changing imports, run `pnpm lint --filter <project_name>` to be sure ESLint and TypeScript rules still pass.
- Add or update tests for the code you change, even if nobody asked.

## PR instructions
- Title format: [<project_name>] <Title>
- Always run `pnpm lint` and `pnpm test` before committing.
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
