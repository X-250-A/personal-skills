---
name: content-push
description: 端到端提交流程——审查项目变更、更新 README（遵循 references/README撰写 规范）、按 Conventional Commits 规范提交、推送至远程仓库。当用户要求提交项目、推送、发布、走提交流程时使用
trigger:
  - "提交项目"
  - "帮我推送"
  - "走一遍提交流程"
  - "content push"
  - "发布项目"
---

# content-push SKILL

## 适用场景

个人维护的轻量级内容仓库，仓库中不涉及代码，只有：
- 文档 / SKILL / README
- 配置文件
- 项目结构变更

AI 在执行前应确保当前目录在对应 git 仓库下。

## 工作流总览

```
Step 1 — 审查项目改动
Step 2 — 更新 README（遵循 references/README撰写 规范）
Step 3 — 提交变更（遵循 references/提交注释撰写 规范）
Step 4 — 同步远程（git pull --rebase）
Step 5 — 推送至远程
```

---

## Step 1：审查项目改动

```bash
git status
git diff
```

基于输出分析：
- 新增了什么文件
- 修改了什么文件
- 删除了什么文件
- 是否有目录/配置层面的结构变更

### 删除类变更确认（必须）

**如果有任何文件被删除（`git status` 中出现 `deleted:`）**，在继续流程之前：
1. 列出所有被删除的文件路径
2. 告知用户删除原因（低频/过时/被替换）
3. **必须获得用户明确确认后才能继续**。未确认前不得进入 Step 2/3

> 删除是不可逆操作（虽然 git 历史可恢复，但工作区内容无法找回），必须由用户拍板。

### 首次提交判断

如果 `git diff` 输出为空，但 `git status` 显示有 untracked files，说明是仓库初始化的首次添加场景——**继续流程**而非提前结束。

如果 `git diff` 为空且 `git status` 为 clean，告知用户"当前没有需要提交的变更"，**结束流程**。

---

## Step 2：更新 README

遵循 **references/README撰写/README规范.md** 的规范执行。重点参考以下原则：

- **判断是否需要更新**：项目新增了模块/功能文档 → 补充入口链接；描述与现状不符 → 修正；README 已最新 → 跳过此步
- **内容依项目类型适配**：当前仓库为纯内容仓库（文档/SKILL），按适配版结构执行（跳过快速开始、配置说明、API 文档等代码板块）
- **编造防范**：用户未提供的信息按兜底处理，禁止编造。README规范.md 中有完整的编造防范规则

更新完成后**告知用户 README 做了哪些修改**。

---

## Step 3：提交变更

遵循 **references/提交注释撰写/提交注释规范.md** 的规范执行。

核心规则摘要如下：

### 类型前缀

| 类型 | 含意 | 内容仓库典型场景 |
|------|------|----------------|
| `docs:` | 文档变更 | 新增/修改/删除 SKILL、知识文档、README |
| `chore:` | 维护工作 | 配置文件、仓库元信息变化 |
| `refactor:` | 结构调整 | 文件迁移、目录重组、重命名 |
| `style:` | 格式修正 | Markdown 排版修正 |
| `init:` | 首次提交 | 仓库初始化 |

### 标准格式

```
<类型>: <不超过 50 字，动词开头，不加句号>
```

### 决策逻辑

按**改动的文件数**统计各类型占比，文件数接近时以**改动行数最大的类别**为准。无法判断时降级为 `docs:`（内容仓库保底类型）。

### 执行命令

```bash
git add -A
git commit -m "<message>"
```

多行 body 时使用：
```bash
git commit -m "<类型>: 一句话" -m "- 改动点1\n- 改动点2"
```

### 提交前确认（必须）

**执行 `git commit` 之前**：
1. 展示拟提交的完整 commit message（含类型前缀与 body）
2. 简要说明类型判定依据（哪些文件归入该类型）
3. **必须获得用户确认后才能执行 commit**。用户要求修改时按意见调整后重新展示

> commit message 是给协作者（含未来的自己）看的，宁可多一轮确认，不提交用户不满意的信息。

失败时必须报告具体错误原因。

---

## Step 4：同步远程

在推送前确保本地与远程同步，避免推送被拒。

```bash
git pull --rebase
```

- **成功** → 继续 Step 5
- **有冲突** → 告知用户具体冲突文件，由用户手动解决冲突后，重新从 Step 4（或直接执行 `git push`）继续
- **当前分支无 upstream**（首次推送前） → 跳过此步，直接进入 Step 5

---

## Step 5：推送至远程

```bash
git push
```

首次推送当前分支时，根据 git 提示处理 upstream：

```bash
git push --set-upstream origin <分支名>
```

推送成功后告知用户**推送到哪个远程仓库的哪个分支**。

---

## 流程概览

```
1. git status / git diff → 审查变更（含删除确认 + 首次提交判断）
2. 按 references/README撰写 规范更新 README（如需）
3. 按 references/提交注释撰写 规范生成 message → 展示并确认 → git add + git commit
4. git pull --rebase（有 upstream 时）
5. git push

任何步骤出错 → 告知用户具体错误，不跳过
删除文件 / commit message → 必须先获得用户确认
```
