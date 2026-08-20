# Python 自查清单（个人踩坑沉淀）

本文件是 code-self-review 的 Python 专项检查清单，审查 Python 代码时逐项对照。每条均来自实际踩坑，标注来源场景便于理解"为什么查"。

**两类条目**：
- **代码级**（[C]）：代码本身的问题，命中 → 计入审查报告对应严重度，必须配修复建议
- **工程级**（[E]）：依赖/环境/数据源配置问题，命中 → 提醒用户并附一句话修复方向，不展开

## 1. FastAPI 生命周期（lifespan）

- [C] `async def lifespan(app: FastAPI)` —— 必须接收 app 参数；写 `def lifespan():` 启动即 TypeError
- [C] `yield` 必须放在 `async with engine.begin()` 块**外**——块内持有事务连接，SQLite 单写锁下报 `database is locked`
- [C] 关闭段用 `await engine.dispose()` —— engine 的方法且是异步；conn 没有 dispose

## 2. SQLAlchemy 异步

- [C] `create_async_engine` 必须配异步驱动 URL：`sqlite+aiosqlite:///...`；写 `sqlite:///...` 报 "requires an async driver"
- [C] 查询必须参数化（`:name` bind 参数 / `text()`），禁止 f-string 拼接用户输入（SQL 注入）
- [C] 关系加载检查 N+1：用 `selectinload` / `joinedload`，勿循环内逐个查

## 3. pydantic v2

- [C] 所有模型字段必须有类型注解——无注解属性报 PydanticUserError
- [C] Settings/配置类注意必填校验：缺失环境变量应显式报错（ValidationError），不要用 `change-me` 占位符静默通过

## 4. uv / 依赖管理（工程级）

- [E] dev 依赖组写在 `[dependency-groups]`（PEP 735），不是 `[project.optional-dependencies]`——写错位置 `uv sync` 静默跳过 dev 组且不报错
- [E] 依赖锁定与 CI：干净环境缺 key/缺依赖会暴露本地没测出的问题——提醒用户 CI 环境校验

## 5. 异常处理

- [C] 禁止静默吞异常（`except Exception: return 原文` 类写法）——故障完全无感知；必须打日志/留痕
- [C] 外部服务调用：限流 429 是常态 → 重试（15/30/45s 退避）+ 降级兜底，不能假设永远可用
- [E] 外部服务调用处应留痕（日志/指标），便于故障定位——静默失败无法排查，间歇性故障优先怀疑外部服务限流/超时

## 6. Windows / 编码（工程级）

- [E] `print` emoji/特殊字符前 `sys.stdout.reconfigure(encoding="utf-8")`，否则 GBK 崩溃
- [E] 路径处理：原生工具（git/rg/node/python）不认 MSYS 路径（`/c/...`），传 `C:/...` 风格
- [E] 写 .bat 给 cmd 跑：内容必须纯 ASCII 或 GBK 编码，UTF-8 中文会乱码

## 7. 外部数据契约（工程级）

- [E] 接口字段映射先拿真实样本实测确认再写死——凭推断写的字段说明会照错写
- [E] 免费数据接口（东财/天天基金等）：限流必然 → 重试 + 多源 fallback
- [E] 筛选/过滤逻辑优先硬指标（量化门槛），名称关键词过滤永远有漏网之鱼

---

## 使用方式

- 代码级条目命中 → 计入审查报告对应严重度，配修复建议
- 工程级条目命中 → 一句话提醒 + 一句话修复方向，不展开
- **checklist 命中与审查维度发现重叠时合并为一条**，不重复报告（如 L4 注入风险与清单第 2 条参数化是同一问题，报一次）
- 未涉及（代码不含该模式）→ 跳过
- 不确定 → 标注 [需核实]，不强行判定
- **使用中发现新坑 → 追加到对应分类，标注日期与场景，让清单随使用生长**
