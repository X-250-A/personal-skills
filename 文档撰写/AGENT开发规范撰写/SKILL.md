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

## 写作规则

- 不涉及技术栈，架构和具体业务

## 模板

````markdown
请你在开发时遵循以下规范以保证开发体验：

## 1. 开发规范

**代码规范**

- <能判对错的规则>

**依赖管理**

- 项目管理依赖的方式

**测试事项**

-  <端> ，跑 <命令>

## 2. 常用命令

```bash
<命令>    # 一句话作用
```

## 3. 注意事项

<一句话>
````

## 反例

| 写法 | 问题 |
| --- | --- |
| 本仓库根目录是一个 javascript 应用 | 技术栈写错，且技术栈不该写 |
| 更新锁文件（pyproject.toml 等） | 项目里没有这个文件，凭空写 |
| 热重载更新（uvicorn --reload） | 与实测相反，会误导 |
| 错误显式抛出，不静默吞异常 | 换任何项目都成立，空话 |
| 判题编译器（MSYS2，不是 Visual Studio） | 括号解释，且属于本机环境 |
